<template>
  <div>
    <div
     ref='barchart'
     :class="{echartbox: isechartbox, doubleechartbox: isdoubleechartbox}"
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
require('echarts/lib/component/legendScroll')
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
export default {
  name: 'Bar',
  props: {
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
    showY: {
      type: Boolean,
      required: false,
      default: false
    },
    doublewidth: {
      type: Boolean,
      required: false,
      default: false
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
      isechartbox: true,
      isdoubleechartbox: this.doublewidth,
      x_name: this.xname,
      y_name: this.yname
    }
  },
  watch: {
    bardata (val) {
      this.title = val.title
      this.legend_data = val.legendData
      this.axis_data = val.axisData
      for (let i of this.axis_data) {
        i.replace(/[\r\n]/g, '')
      }
      this.series_data = val.seriesData
      this.drawBar()
    }
  },
  mounted () {
    this.drawBar()
  },
  methods: {
    drawBar () {
      let myCharts = echarts.init(this.$refs.barchart)
      myCharts.clear()
      myCharts.setOption({
        color: ['#4FC3F7', '#e88b0f', '#4E76BA', '#F1CB48', '#7460EE', '#50d5cd', '#E3405C', '#c79121', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a', '#F8F4F3', '#D59486', '#B1468B'],
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
          position: function (point, params, dom, rect, size) {
            // 鼠标坐标和提示框位置的参考坐标系是：以外层div的左上角那一点为原点，x轴向右，y轴向下
            // 提示框位置
            let x = 5 // x坐标位置
            let y = 5 // y坐标位置
            // 当前鼠标位置
            let pointX = point[0]
            let pointY = point[1]
            // 提示框大小
            let boxWidth = size.contentSize[0]
            let boxHeight = size.contentSize[1]
            // boxWidth > pointX 说明鼠标左边放不下提示框
            if (boxWidth > pointX) {
              x = 100
            } else { // 左边放的下
              x = pointX - boxWidth
            }
            // boxHeight > pointY 说明鼠标上边放不下提示框
            if (boxHeight > pointY) {
              y = -40
            } else { // 上边放得下
              y = pointY - boxHeight
            }
            return [x, y]
          },
          axisPointer: {
            type: 'shadow'
          },
          formatter: function (params) {
            // 判断当前页面是否是visualScr,如果是则不添加$符号
            let scrHref = window.location.href
            let Total = 0
            let relVal = params[0].name
            for (let i = 0; i < params.length; i++) {
              let series = params[i]
              Total = Total + parseInt(series.data)
              if (params[i].seriesName === 'Closure') {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + params[i].value + '%'
              } else if (params[i].seriesName === 'P0' || params[i].seriesName === 'P1' || params[i].seriesName === 'P2' || params[i].seriesName === 'ToTal shortage PN') {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
              } else if (params[i].seriesName === 'Utilization%') {
                relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',') + '%'
              } else {
                if (scrHref.includes('visualScr')) {
                  relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
                } else {
                  relVal += '<br/>' + params[i].marker + params[i].seriesName + ' : ' + '$' + parseInt(params[i].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
                }
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
          containLabel: true
        },
        legend: {
          bottom: 5,
          type: 'scroll',
          data: this.legend_data,
          pageIconColor: '#63aa5c',
          pageTextStyle: {
            color: '#B6B6B6'
          },
          animation: true,
          animationDurationUpdate: 800,
          textStyle: {
            color: '#B6B6B6'
          },
          selected: {
            'TOTAL': false
          },
          selector: [
            {
              type: 'all or inverse',
              title: '全选'
            },
            {
              type: 'inverse',
              title: '反选'
            }
          ]
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
              rotate: 45,
              interval: 0,
              margin: 10,
              formatter: function (value, index) {
                // 判断当前页面是否是visualScr,如果是则不添加$符号
                let scrHref = window.location.href
                if (value.length >= 12 && scrHref.includes('visualScr') === true) {
                  const newValue = value.split('|')[0] + '\n' + value.split('|')[1]
                  return newValue
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
        yAxis: [
          {
            type: 'value',
            left: '10',
            name: this.y_name,
            scale: true,
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
                // 判断当前页面是否是visualScr,如果是则不添加$符号
                let scrHref = window.location.href
                if (value >= 100 && value < 1000) {
                  if (scrHref.includes('visualScr') === true) {
                    return value
                  } else {
                    value = '$' + value + 'M'
                  }
                } else if (value >= 1000 && value < 10000000) {
                  value = value / 1000 + 'K'
                } else if (value === 0) {
                  return value
                } else if (value < 100) {
                  return value + 'M'
                } else {
                  value = (value / 1000000).toString().replace(/(?=(?!^)(\d{3})+$)/g, ',') + 'M'
                }
                return value
              }
            }
          },
          {
            type: 'value',
            scale: true,
            show: this.showY,
            splitLine: {
              lineStyle: {
                color: '#898D95'
              }
            },
            max: 100,
            min: 0,
            axisLine: {
              lineStyle: {
                color: '#898D95'
              }
            },
            axisLabel: {
              formatter: '{value}%', // 右侧以百分比进行展示
              show: true,
              textStyle: {
                color: '#BFC2C8',
                fontSize: '12'
              }
            },
            textStyle: {
              color: '#BFC2C8',
              fontSize: '12'
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
.doubleechartbox {
  width: 100%;
  height: 370px;
  margin: 0 auto;
}
</style>
