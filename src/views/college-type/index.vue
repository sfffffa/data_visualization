<template>
  <div class="app-container">
    <el-card style="width:100%; margin:0 auto; padding-top: 20px;">
      <el-row>
        <el-col :span="13"><div class="grid-content">
          <div id="mainChart" :style="{width: '100%', height: '640px'}"></div>
        </div></el-col>

        <el-col :span="1"><div class="grid-content">
          <el-divider direction="vertical" ></el-divider>
        </div></el-col>

        <el-col :span="10"><div class="grid-content">
          <div id="myChart" :style="{width: '100%', height: '640px'}"></div>
        </div></el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import Papa from 'papaparse'
export default {
  data() {
    return {
      data:[],
      Engineering:[],
      Party:[], 
      LiberalArts:[], 
      IvyLeague:[], 
      State:[]
    }
  },
  methods: {
    getData(){
      this.$axios
        .get("data/salaries-by-college-type.csv")
        .then(res => {
          let csv= res.data;
          Papa.parse(csv, {
            complete: (results) => {
              this.data=results.data;

              this.Engineering=this.data.filter((item)=>{
                return item[1]=='Engineering';
              });
              this.Party=this.data.filter((item)=>{
                return item[1]=='Party';
              });
              this.LiberalArts=this.data.filter((item)=>{
                return item[1]=='Liberal Arts';
              });
              this.IvyLeague=this.data.filter((item)=>{
                return item[1]=='Ivy League';
              });
              this.State=this.data.filter((item)=>{
                return item[1]=='State';
              });

              // console.log("Finished:", this.IvyLeague);
              
              // console.log("Finished:", this['IvyLeague']);
            }
          });
        }); 
    },
    draw() {
      if(this.draw.myChart==null){ 
        // 初始化echarts实例
        this.draw.myChart = this.$echarts.init(document.getElementById('mainChart'));
      }
      let option = {
        title: {
          text: '学校类型',
          textAlign:'left',
          textVerticalAlign: 'top',
        },
        legend: {
          data: [
            'Engineering', 
            'Party', 
            'Liberal Arts', 
            'Ivy League', 
            'State'
            ],
        },
        tooltip: {
          // trigger: 'axis'
        },
        radar: {
          // shape: 'circle',
          indicator: [
            { name: 'Starting Median Salary', max: 60500 },
            { name: 'Mid-Career 10th Percentile Salary', max: 61800 },
            { name: 'Mid-Career 25th Percentile Salary', max: 82800 },
            { name: 'Mid-Career 50th Percentile Salary', max: 120125 },
            { name: 'Mid-Career 75th Percentile Salary', max: 184125 },
            { name: 'Mid-Career 90th Percentile Salary', max: 270000 }
          ]
        },
        series: [
          {
            name: 'Budget vs spending',
            type: 'radar',
            data: [
              {
                value: [59057.89, 61793.33, 81384.21, 103842.11, 134868.42, 173333.33],
                name: 'Engineering'
              },
              {
                value: [45715.00, 44052.63, 60005.00, 84685.00, 118100.00, 166947.37],
                name: 'Party'
              },
              {
                value: [45746.81, 47478.57,	61936.17, 89378.72,	131076.60, 191142.86],
                name: 'Liberal Arts'
              },
              {
                value: [60475.00, 57900.00, 82787.50, 120125.00, 184125.00, 269625.00],
                name: 'Ivy League'
              },
              {
                value: [44126.29, 41886.29, 56689.71, 78567.43, 106970.86, 147571.43],
                name: 'State'
              }
            ]
          }
        ]
      };
      //防止越界，重绘canvas
      window.onresize = this.draw.myChart.resize;
      this.draw.myChart.on('click', (params) => {
        // 控制台打印数据的名称
        console.log(params.name);
        this.draw2(params.name.replace(/\s*/g,""));
      });
      this.draw.myChart.setOption(option);
    },
    draw2(which) {
      if(this.draw2.myChart==null){ 
        // 初始化echarts实例
        this.draw2.myChart = this.$echarts.init(document.getElementById('myChart'));
      }
      let option = {
        tooltip: {
          // trigger: 'axis'
        },
        xAxis: {
          type: 'category',
          data: this[which].map((item)=>{
            return item[0];
          }),
          axisLabel: { interval: 0, rotate: 45 }
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            data: this[which].map((item)=>{
              return item[3];
            }),
            type: 'bar'
          }
        ]
      };
      //防止越界，重绘canvas
      window.onresize = this.draw2.myChart.resize;
      this.draw2.myChart.setOption(option);
    },
    runtimeFormatter(row,column){
      let runtime = row.runtime;
      if(runtime==0){
        return '-'
      }
      return runtime;
    },
  },
  mounted(){
    this.getData();
    this.draw();
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
    height: 52em;
    margin: 0 8px;
    vertical-align: middle;
    position: relative;
  } 
</style>
