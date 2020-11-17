<template>
  <div class="page_view">
    <div class="title">会议室预约量</div>
    <div id="myChart1" class="chart-view"></div> 

    <el-radio-group v-model="select" @change="showChart1()">
      <el-radio-button label="7">最近一周</el-radio-button>
      <el-radio-button label="14">最近两周</el-radio-button>
      <el-radio-button label="30">最近一月</el-radio-button>
    </el-radio-group>
  </div>
</template>

<script>

export default {
  name: 'echart',
  data () {
    return {
      chart1_title: '会议室预约量',
      select: '7',
      dataList_7_name: [],
      dataList_7_count: [],
      dataList_14_name: [],
      dataList_14_count: [],
      dataList_30_name: [],
      dataList_30_count: [],
    }
  },
  
  mounted(){
    //页面加载完成后,才执行
    this.showChart1();
  },
  methods: {
    showChart1()
    {
        // 基于准备好的dom，初始化echarts实例
        let myChart1 = this.$echarts.init(document.getElementById('myChart1'))
        myChart1.setOption({
            color: ['#3398DB'],
            tooltip: {
                trigger: 'axis',
                axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                    type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                }
            },
            grid: {
                top:"50px",
                left:"40px",
                right:"40px",
                width:"auto", //图例宽度
            },
            xAxis: [
                {
                    type: 'category',
                    data: [],
                    axisTick: {
                        alignWithLabel: true
                    },
                    "axisLabel":{
                      interval: 0
                    }
                }
            ],
            yAxis: [
                {
                    type: 'value'
                }
            ],
            series: [
                {
                    name: '预约量',
                    type: 'bar',
                    barWidth: '60%',
                    data: [],
                }
            ]
        });
        
        this.$http({
        url: this.$http.adornUrl("/meeting/meet/echart"),
        method: "get",
        }).then(({ data }) => {
        if (data && data.code === 0) {
          console.log(data);
            for(let i=0; i<data.day7.length; i++) {
                this.dataList_7_name[i] = data.day7[i].name;
                this.dataList_7_count[i] = data.day7[i].count;
                this.dataList_14_name[i] = data.day14[i].name;
                this.dataList_14_count[i] = data.day14[i].count;
                this.dataList_30_name[i] = data.mon[i].name;
                this.dataList_30_count[i] = data.mon[i].count;
            }
        }
        if(this.select == "7") {
          console.log("7777");
          console.log(this.dataList_7_name);
          myChart1.setOption({
           xAxis: [
                {
                    data: this.dataList_7_name,
                }
            ],
             series: [
                {
                    name: '预约量',
                    data: this.dataList_7_count,
                }
            ]
        });
        } else if(this.select == "14") {
          console.log("14 14");
          console.log(this.dataList_14_name);
          myChart1.setOption({
           xAxis: [
                {
                    data: this.dataList_14_name,
                }
            ],
             series: [
                {
                    name: '预约量',
                    data: this.dataList_14_count,
                }
            ]
        });
        } 
        else {
          console.log("30 30");
          console.log(this.dataList_30_name);
          myChart1.setOption({
           xAxis: [
                {
                    data: this.dataList_30_name,
                }
            ],
             series: [
                {
                    name: '预约量',
                    data: this.dataList_30_count,
                }
            ]
        });
        }
      });
    }
  }
}
</script>

<style>

.page_view
{
  padding:20px 3%;
  text-align: center;
}

.title{
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.chart-view{
  margin: 20px auto;
  width: 600px;
  height: 600px;
}


</style>

