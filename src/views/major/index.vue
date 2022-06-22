<template>
  <div class="app-container">
    <el-card style="width:100%; margin:0 auto; padding-top: 20px">
      <div id="payBack" :style="{width: '100%', height: '800px'}"></div>
    </el-card>
  </div>
</template>

<script>
import Papa from 'papaparse'
export default {
  data() {
    return {
      region:{
        data:[],
      },
      collegeType:{
        data:[],
      },
      payBack:{
        data:[],
        x:[
          "Mid-Career 10th Percentile Salary",
          "Mid-Career 25th Percentile Salary",
          "Mid-Career 50th Percentile Salary",
          "Mid-Career 75th Percentile Salary",
          "Mid-Career 90th Percentile Salary",
          ],
        major:[],
      },
    }
  },
  computed:{
  },
  methods: {
    getData(){
      this.$axios
        .get("data/degrees-that-pay-back.csv")
        .then(res => {
          let csv= res.data;
          Papa.parse(csv, {
            complete: (results) => {
              this.payBack.data=results.data;
              this.payBack.major=results.data.slice(1).map((node)=>{
                return node[0];
              })
              console.log("Finished1:", results.data);
              this.drawPayBack();
            }
          });
        });      
    },
    drawPayBack(){
      if(this.drawPayBack.myChart==null){ 
        // 初始化echarts实例
        this.drawPayBack.myChart = this.$echarts.init(document.getElementById('payBack'));
      }
      let option = {
        title: {
          text: '各个专业收入一览'
        },
        tooltip: {
          // trigger: 'axis'
        },
        legend: {
          data: this.payBack.major,
          type:'scroll',
          orient:'vertical',
          left:'right',
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        xAxis: {
          type: 'category',
          // boundaryGap: false,
          data: this.payBack.x
        },
        yAxis: {
          type: 'value'
        },
        series: this.payBack.data.slice(1).map((node)=>{
          let data=[];
          data.push(parseInt(node[4]));
          data.push(parseInt(node[5]));
          data.push(parseInt(node[2]));
          data.push(parseInt(node[6]));
          data.push(parseInt(node[7]));
          return {
            name:node[0],
            type: 'line',
            data:data
          }
        })
      };
      //防止越界，重绘canvas
      // window.onresize = this.drawPayBack.myChart.resize;
      this.drawPayBack.myChart.setOption(option);
    },
    querySucceed(database) {
      this.$notify({
        title: '成功',
        message: "成功获取"+database+"的查询结果",
        type: 'success'
      });
    },
    queryFail(database) {
      this.$notify.error({
        title: '错误',
        message: "未能获取"+database+"的查询结果",
      });
    },
    runtimeFormatter(row,column){
      let runtime = row.runtime;
      if(runtime==0){
        return '-'
      }
      return runtime;
    },
  },
  watch:{
  },
  mounted(){
    this.getData();
  }
}
</script>

<style lang="scss" scoped>
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .el-divider--vertical{
    display: inline-block;
    width: 1px;
    height: 31em;
    margin: 0 8px;
    vertical-align: middle;
    position: relative;
  } 
</style>
