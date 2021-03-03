<template>
  <div class='gaugeinfo'>
    <div
     ref='gaugechart'
     class='echartbox'
    >
    </div>
    <p class='targetnum' style='color: #B6B6B6' v-show='target_num'>Target : {{this.target_num}}</p>
  </div>
</template>

<script>
// 引入基本模板
let echarts = require('echarts/lib/echarts')
// 引入仪表图组件
require('echarts/lib/chart/gauge')
// 引入提示框和title组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
export default {
  name: 'Gauge',
  props: {
    // max: Number,
    // targetnum: Number,
    // texttitle: String,
    // seriesdata: Array
    textlink: {
      type: String,
      required: false,
      default: ''
    },
    gaugedata: Object
  },
  data () {
    return {
      link: this.textlink,
      maxnum: this.gaugedata.max,
      target_num: this.gaugedata.target,
      title: this.gaugedata.title,
      series_data: this.gaugedata.data
    }
  },
  watch: {
    gaugedata (val) {
      this.maxnum = val.max
      this.target_num = val.target
      this.title = val.title
      this.series_data = val.data
      this.drawGauge()
    }
  },
  mounted () {
    // this.drawGauge()
  },
  methods: {
    drawGauge () {
      let myCharts = echarts.init(this.$refs.gaugechart)
      let maxnum = this.maxnum
      let target = this.target_num
      let rate = target / maxnum
      myCharts.setOption({
        color: ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#83D0D5', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a'],
        tooltip: {
          formatter: '{b}:{c}'
        },
        title: {
          text: this.title,
          left: '35',
          top: '20',
          link: this.link,
          textStyle: {
            color: '#B6B6B6'
          }
        },
        series: [
          {
            name: this.title,
            type: 'gauge',
            detail: {
              formatter: function (value) {
                // console.log(value)
                return 'Actual : ' + value
              },
              fontSize: '14',
              offsetCenter: [0, '25%']
            },
            center: ['50%', '68%'],
            radius: '100%',
            startAngle: 160,
            endAngle: 20,
            min: 0,
            max: maxnum,
            splitNumber: 10,
            axisTick: {
              show: false
            },
            axisLine: {
              lineStyle: {
                color: [[rate, '#4E76BA'], [1, '#E8830B']]
              }
            },
            splitLine: {
              lineStyle: {
                color: 'auto',
                width: 1
              }
            },
            axisLabel: {
              color: '#BFC2C8',
              formatter: function (value) {
                var median = maxnum / 2
                var max = maxnum
                switch (value) {
                  case 0: return '0'
                  case median: return median
                  case max: return maxnum
                }
              }
            },
            title: {
              show: false
            },
            // data: [{value: '25', name: 'Actual'}]
            data: this.series_data
          }
        ]
      })
    }
  }
}
</script>

<style lang='less' scoped>
  .gaugeinfo {
    width:350px;
    position:relative;
    margin:0 auto;
  }
  .echartbox {
    width: 350px;
    height:300px;
  }
  .targetnum {
    position:absolute;
    left: 39%;
    bottom: 30px;
    color: #fff;
    font-size: 14px;

  }
</style>
