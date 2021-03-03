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
  name: 'Bar',
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
        color: ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#50d5cd', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a'],
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
            type: 'shadow'
          },
          formatter: function (params) {
            let Total = 0
            var relVal = params[0].name
            for (var i = 0; i < params.length; i++) {
              let series = params[i]
              Total = Total + parseInt(series.data)
              if (params[i].seriesName === 'Closure') {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + params[i].value + '%'
              } else if (params[i].seriesName === 'P0' || params[i].seriesName === 'P1' || params[i].seriesName === 'P2' || params[i].seriesName === 'ToTal shortage PN') {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
              } else {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + '$' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
              }
            }
            if (params.length === 7) {
              relVal = relVal + '<br/>' + '<span style="display:inline-block;margin-right:5px;border-radius:10px;width:10px;height:10px;background-color:blue;"></span>' + 'Total' + ':' + '$' + Total.toString().replace(/(?=(?!^)(\d{3})+$)/g, ',')
              return relVal
            } else {
              return relVal
            }
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
          },
          selected: {
            'TOTAL': false
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
            axisLine: {
              lineStyle: {
                color: '#BFC2C8'
              }
            },
            axisTick: {
              show: false
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
                if (value >= 100 && value < 1000) {
                  value = '$' + value + 'M'
                } else if (value >= 0 && value < 100) {
                  value = value + '%'
                } else if (value === 0) {
                  return value
                } else if (value >= 1000 && value < 10000000) {
                  value = value / 1000 + 'K'
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
    height:300px;
    margin: 0 auto;
  }
</style>
