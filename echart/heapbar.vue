<template>
  <div>
    <div
      ref='barchart'
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
// 引入折线图组件
require('echarts/lib/chart/line')
// 引入提示框和title组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
export default {
  name: 'Heapbar',
  props: {
    // texttitle: String,
    // legenddata: Array,
    // axisdata: Array,
    // seriesdata: Array,
    textlink: {
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
    bardata: Object
  },
  data () {
    return {
      link: this.textlink,
      title: '',
      legend_data: this.bardata.legendData,
      click_link: this.clicklink,
      axis_data: this.bardata.axisData,
      series_data: this.bardata.seriesData,
      x_name: this.xname,
      y_name: this.yname
    }
  },
  watch: {
    bardata (val) {
      this.title = val.title
      this.legend_data = val.legendData
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
      let myCharts = echarts.init(this.$refs.barchart)
      myCharts.clear()
      myCharts.setOption({
        color: ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#83D0D5', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a'],
        title: {
          text: this.title,
          left: '35',
          top: '20',
          link: this.link,
          textStyle: {
            color: '#B6B6B6'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              show: false,
              formatter: function (params) {
                if (params.seriesData.length === 0) {
                  // 获取数据
                  window.mouseCurValue = params.value
                  return window.mouseCurValue
                }
              }
            }
          },
          formatter: function (params) {
            let res = params[0].name
            let sum = 0
            let Total = 0
            // 定义一个开关
            let TF = true
            for (let i = 0; i < params.length; i++) {
              let series = params[i]
              Total = Total + parseInt(series.data)
              sum += Number(series.data)
              if (sum >= window.mouseCurValue && TF) {
                res = series.axisValue + '<br/>' + series.marker + series.seriesName + ':' + '$' + series.data.toString().replace(/(?=(?!^)(\d{3})+$)/g, ',')
                TF = false
              }
            }
            res = res + '<br/>' + '<span style="display:inline-block;margin-right:5px;border-radius:10px;width:10px;height:10px;background-color:red;"></span>' + 'TOTAL' + ':' + '$' + Total.toString().replace(/(?=(?!^)(\d{3})+$)/g, ',')
            return res
          }
        },
        grid: {
          left: '15%',
          bottom: '30%'
        },
        legend: {
          data: this.legend_data,
          left: 'center',
          bottom: '0',
          textStyle: {
            color: '#B6B6B6'
          }
        },
        xAxis: [
          {
            type: 'category',
            position: 'bottom',
            name: this.x_name,
            nameLocation: 'center',
            nameGap: '50',
            data: this.axis_data,
            axisTick: {
              show: true
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
            axisLine: {
              lineStyle: {
                color: '#BFC2C8'
              }
            },
            axisTick: {
              show: true
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
                if (value >= 0 && value < 1000) {
                  value = value + ''
                } else if (value >= 1000 && value < 10000000) {
                  value = value / 1000 + 'K'
                } else if (value === 0) {
                  return value
                } else {
                  value = (value / 1000000).toString().replace(/(?=(?!^)(\d{3})+$)/g, ',') + 'M'
                  // value = Number((value / 1000000).toFixed(0)).toLocaleString()
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
    height: 300px;
    margin: 0 auto;
  }
</style>
