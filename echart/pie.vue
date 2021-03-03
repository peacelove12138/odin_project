<template>
  <div>
    <div
     ref='piechart'
     :class="{echartbox: isechartbox, doubleechartbox: isdoubleechartbox}"
    >
    </div>
  </div>
</template>

<script>
let echarts = require('echarts/lib/echarts')
// 引入饼图组件
require('echarts/lib/chart/pie')
// 引入提示框和title组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
require('echarts/lib/component/legend')
export default {
  name: 'Pie',
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
    doublewidth: {
      type: Boolean,
      required: false,
      default: false
    },
    titletype: {
      type: String,
      required: false,
      default: ''
    },
    pieColor: {
      type: Array,
      required: false,
      default () {
        return ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#83D0D5', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a']
      }
    },
    piedata: Object
  },
  data () {
    return {
      link: this.textlink,
      click_link: this.clicklink,
      title: this.piedata.title,
      legend_data: this.piedata.legendData,
      series_data: this.piedata.seriesData,
      isechartbox: true,
      isdoubleechartbox: this.doublewidth,
      title_type: this.titletype,
      date: this.date
    }
  },
  watch: {
    piedata (val) {
      this.title = val.title
      this.legend_data = val.legendData
      this.series_data = val.seriesData
      this.drawPie()
    }
  },
  mounted () {
    this.drawPie()
  },
  methods: {
    drawPie () {
      let myCharts = echarts.init(this.$refs.piechart)
      myCharts.setOption({
        color: this.pieColor,
        title: {
          text: this.title,
          left: '30',
          top: '5',
          link: this.link,
          textStyle: {
            color: '#B6B6B6'
          }
        },
        tooltip: {
          trigger: 'item',
          formatter: function (params) {
            let relVal = []
            relVal += '<br/>' + params['data'].name + ' : ' + parseInt(params['data'].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
            return relVal
          }
        },
        legend: {
          bottom: 0,
          left: 'center',
          data: this.legend_data,
          textStyle: {
            color: '#B6B6B6'
          },
          show: true
        },
        series: [
          {
            name: this.title,
            type: 'pie',
            radius: '60%',
            minAngle: 25,
            center: ['50%', '45%'],
            avoidLabelOverlap: false,
            data: this.series_data,
            label: {
              normal: {
                position: 'outer',
                formatter: function (p) {
                  let value = parseInt(p.value).toFixed(0)
                  let num = (value || 0).toString()
                  let result = ''
                  while (num.length > 3) {
                    result = ',' + num.slice(-3) + result
                    num = num.slice(0, num.length - 3)
                  }
                  if (num) {
                    result = num + result
                  }
                  return result + '\n' + '(' + p.percent.toFixed(2) + '%)'
                }
              }
            }
          }
        ]
      })
      myCharts.on('click', this.clickEcharts)
    },
    clickEcharts (param) {
      if (this.click_link) {
        if (this.title_type.length) {
          this.$emit('getChartBlockData', param.name, this.title_type)
        } else {
          this.$emit('getChartBlockData', param.name)
        }
      }
    }
  }
}
</script>

<style lang='less' scoped>
.echartbox {
  width: 360px;
  height: 300px;
  margin: 0 auto;
}
.doubleechartbox {
  width: 800px;
  height: 600px;
  margin: 0 auto;
}
</style>
