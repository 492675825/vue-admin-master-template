<template>
  <div id="myChart" :style="{width: '100%', height: '600px', marginTop:'100px'}">

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'hello',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  created() {
    this.drawLine();
  },
  methods: {
    drawLine() {
      axios({
        method: "get",
        url: `http://127.0.0.1:8000/goldData/yearlAverageClose`,
      }).then(res => {

        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('myChart'))
        // 绘制图表
        myChart.setOption({
          title: {text: '黄金年涨跌趋势'},
          toolbox:{
            show:true,
            feature: {
              dataView: { //数据视图
                show: true
              },
              restore: { //重置
                show: true
              },
              dataZoom: { //数据缩放视图
                show: true
              },
              saveAsImage: {//保存图片
                show: true
              },
              magicType: {//动态类型切换
                type: ['bar', 'line']
              }
            }
          },
          tooltip: {
            show:true
          },
          xAxis: {
            // data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
            data: res.data.data.year_number
          },
          yAxis: {},
          series: [{
            name: '平均值',
            type: 'bar',
            data: res.data.data.average_price,
            itemStyle: {
              normal: {
                label: {
                  show: true, //开启显示
                  position: 'top', //在上方显示
                  textStyle: { //数值样式
                    color: 'black',
                    fontSize: 16
                  }
                }
              }
            },
          }]
        });
      }).catch(res => {
        this.$message.error(res.message)
      })
    }
  }
}
</script>
