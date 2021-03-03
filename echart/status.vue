<template>
  <div>
    <div
     ref='statusbarchart'
     class='echartbox'
    >
    </div>
  </div>
</template>

<script>
// 引入基本模板
let echarts = require('echarts/lib/echarts')
// 引入柱状图组件
require('echarts/lib/chart/bar')
// 引入提示框和title组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
export default {
  name: 'StatusBar',
  props: {
    // subtext: String,
    // texttitle: String,
    // axisdata: Array,
    // seriesdata: Array,
    textlink: {
      type: String,
      required: false,
      default: ''
    },
    sublink: {
      type: String,
      required: false,
      default: ''
    },
    clicklink: {
      type: Boolean,
      required: false,
      default: false
    },
    xname: {
      type: String,
      required: false,
      default: ''
    },
    yname: {
      type: String,
      required: false,
      default: ''
    },
    statusdata: Object
  },
  data () {
    return {
      sun_link: this.sublink,
      link: this.textlink,
      x_name: this.xname,
      y_name: this.yname,
      title: this.statusdata.title,
      subtitle: this.statusdata.subtitle,
      axis_data: this.statusdata.AxisData,
      series_data: this.statusdata.seriesData
    }
  },
  watch: {
    statusdata (val) {
      this.title = val.title
      this.subtitle = val.subtitle
      this.axis_data = val.axisData
      this.series_data = val.seriesData
      this.drawBar()
    }
  },
  mounted () {
    // this.drawBar()
  },
  methods: {
    drawBar () {
      let myCharts = echarts.init(this.$refs.statusbarchart)
      myCharts.setOption({
        title: {
          text: this.title,
          left: '35',
          link: this.link,
          textStyle: {
            color: '#B6B6B6'
          },
          subtext: this.subtitle,
          sublink: this.sub_link,
          subtextStyle: {
            color: '#B6B6B6',
            fontSize: 18,
            fontWeight: 'bold'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          }
        },
        grid: {
          left: '15%',
          bottom: '30%',
          top: '25%'
        },
        legend: {
          show: false
        },
        xAxis: [
          {
            type: 'category',
            position: 'bottom',
            name: this.x_name,
            nameLocation: 'center',
            nameGap: '35',
            data: this.axis_data,
            axisTick: {
              show: false
            },
            axisLabel: {
              textStyle: {
                color: '#BFC2C8'
              },
              rotate: 30,
              interval: 0
            },
            axisLine: {
              lineStyle: {
                color: '#BFC2C8'
              }
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            left: '10',
            name: this.y_name,
            nameLocation: 'center',
            nameGap: '45',
            axisTick: {
              show: false
            },
            axisLine: {
              lineStyle: {
                color: '#BFC2C8'
              }
            },
            splitLine: {
              lineStyle: {
                color: '#898D95'
              }
            },
            axisLabel: {
              textStyle: {
                color: '#BFC2C8'
              },
              interval: 0,
              formatter: function (value, index) {
                if (value >= 1000 && value < 10000000) {
                  value = value / 1000 + 'K'
                } else if (value >= 1000000 && value < 1000000000) {
                  value = value / 1000000 + 'M'
                } else if (value >= 1000000000) {
                  value = value / 1000000000 + 'B'
                }
                return value
              }
            }
          }
        ],
        series: this.series_data
      })
      window.addEventListener('resize', function () {
        myCharts.resize()
      })
      myCharts.on('click', this.clickEcharts)
    },
    clickEcharts (param) {
      if (this.click_link) {
        this.$emit('getChartBlockData', param.name)
      }
    }
  }
}
</script>

<style lang='less' scoped>

.echartbox {
  width: 100%;
  height:300px;
  margin: 0 auto;
}
</style>
