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
  name: 'SpecialPie',
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
      myCharts.clear()
      myCharts.setOption({
        color: ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#83D0D5', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a', '#FF3366', '#CC33CC', '#CC0033', '#CCCC33', '#FF6666', '#66FF66', '#6633CC', '#9999FF', '#663333', '#FF3333', '#FFCCCC', '#000066'],
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
            relVal += '<br/>' + params['data'].name + ' : ' + '$' + parseInt(params['data'].value).toFixed(0).replace(/(?=(?!^)(\d{3})+$)/g, ',')
            return relVal
          }
        },
        legend: {
          bottom: 0,
          left: 'center',
          // orient : 'vertical',
          x: 250,
          y: 235,
          data: this.legend_data,
          textStyle: {
            color: '#B6B6B6'
          }
        },
        series: [
          {
            name: this.title,
            type: 'pie',
            radius: '55%',
            center: ['50%', '30%'],
            avoidLabelOverlap: false,
            data: this.series_data,
            label: {
              normal: {
                show: false,
                position: 'outer',
                formatter: function (p) {
                  return name + '\n' + '(' + p.percent.toFixed(2) + '%)'
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
  width: 460px;
  height: 405px;
  margin: 0 auto;
}
.doubleechartbox {
  width: 800px;
  height: 600px;
  margin: 0 auto;
}
</style>
