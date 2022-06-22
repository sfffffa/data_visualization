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
      California:[],
      Western:[], 
      Midwestern:[], 
      Southern:[], 
      Northeastern:[]
    }
  },
  methods: {
    getData(){
      this.$axios
        .get("data/salaries-by-region.csv")
        .then(res => {
          let csv= res.data;
          Papa.parse(csv, {
            complete: (results) => {
              this.data=results.data;

              this.California=this.data.filter((item)=>{
                return item[1]=='California';
              });
              this.Western=this.data.filter((item)=>{
                return item[1]=='Western';
              });
              this.Midwestern=this.data.filter((item)=>{
                return item[1]=='Midwestern';
              });
              this.Southern=this.data.filter((item)=>{
                return item[1]=='Southern';
              });
              this.Northeastern=this.data.filter((item)=>{
                return item[1]=='Northeastern';
              });
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
          text: '地域',
          textAlign:'left',
          textVerticalAlign: 'top',
        },
        legend: {
          data: [
            'California', 
            'Western', 
            'Midwestern', 
            'Southern', 
            'Northeastern'
            ],
        },
        tooltip: {
          // trigger: 'axis'
        },
        radar: {
          // shape: 'circle',
          indicator: [
            { name: 'Starting Median Salary', max: 51033},
            { name: 'Mid-Career 10th Percentile Salary', max: 49102 },
            { name: 'Mid-Career 25th Percentile Salary', max: 67154 },
            { name: 'Mid-Career 50th Percentile Salary', max: 93133 },
            { name: 'Mid-Career 75th Percentile Salary', max: 129576 },
            { name: 'Mid-Career 90th Percentile Salary', max: 181927 }
          ]
        },
        series: [
          {
            name: 'Budget vs spending',
            type: 'radar',
            data: [
              {
                value: [51032.14, 47777.27, 67153.57, 93132.14, 127350.00, 167909.09],
                name: 'California'
              },
              {
                value: [44414.29, 42985.29, 56580.95, 78200.00, 106026.19, 143823.53],
                name: 'Western'
              },
              {
                value: [44225.35, 43076.56, 57026.76, 78180.28, 107594.37, 147689.06],
                name: 'Midwestern'
              },
              {
                value: [44521.52, 43074.65, 57506.33, 79505.06, 109662.03, 152769.01],
                name: 'Southern'
              },
              {
                value: [48496.00, 49101.22, 65479.00, 91352.00, 129576.00, 181926.83],
                name: 'Northeastern'
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
        this.draw2(params.name);
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
