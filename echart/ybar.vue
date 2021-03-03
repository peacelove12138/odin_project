<template>
  <div>
    <div
     ref='ybarchart'
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
  name: 'YBar',
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
    ybardata: Object
  },
  data () {
    return {
      link: this.textlink,
      title: '',
      click_link: this.clicklink,
      legend_data: this.ybardata.legendData,
      axis_data: this.ybardata.axisData,
      series_data: this.ybardata.seriesData,
      x_name: this.xname,
      y_name: this.yname
    }
  },
  watch: {
    ybardata (val) {
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
      let myCharts = echarts.init(this.$refs.ybarchart)
      myCharts.setOption({
        color: ['#E7820A', '#F1CB48', '#2962FF', '#4FC3F6', '#735FEE', '#F485F8', '#9dce8a', '#213354', '#263E62', '#325280', '#446CAE', '#213453'],
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
            var relVal = params[0].name
            for (var i = 0, l = params.length; i < l; i++) {
              relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + '$' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
            }
            return relVal
          }
        },
        grid: {
          left: '23%',
          bottom: '30%',
          containLabel: false
        },
        legend: {
          data: this.legend_data,
          left: 'center',
          bottom: '0',
          textStyle: {
            color: '#B6B6B6'
          }
        },
        yAxis: [
          {
            type: 'category',
            data: this.axis_data,
            name: this.y_name,
            nameLocation: 'center',
            nameGap: '75',
            axisTick: {
              show: false
            },
            axisLabel: {
              textStyle: {
                color: '#BFC2C8'
              },
              interval: 0,
              formatter: function (value) {
                if (value.length > 12) {
                  // console.log(value.slice(0, 8))
                  return value.slice(0, 12) + '...'
                } else {
                  return value
                }
              }
            },
            axisLine: {
              lineStyle: {
                color: '#BFC2C8'
              }
            }
          }
        ],
        xAxis: [
          {
            type: 'value',
            position: 'bottom',
            name: this.x_name,
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
  margin-top: -10px;
}
</style>
