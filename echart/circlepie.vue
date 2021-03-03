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
  name: 'CirclePie',
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
      title_type: this.titletype
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
        color: ['#4FC3F7', '#E8830B', '#4E76BA', '#F1CB48', '#7460EE', '#83D0D5', '#E3405C', '#F1CB48', '#1BBA83', '#F485F8', '#EEDB91', '#9dce8a'],
        title: {
          text: this.title,
          left: 'center',
          minAngle: 20,
          top: '38%',
          link: this.link,
          textStyle: {
            color: '#7d7d7d',
            fontSize: 15
          }
        },
        tooltip: {
          trigger: 'item',
          formatter: function (params) {
            let relVal = []
            relVal += '<br/>' + params['data'].name + ' : ' + params['data'].value.toString().replace(/(?=(?!^)(\d{3})+$)/g, ',')
            return relVal
          }
        },
        legend: {
          bottom: 5,
          left: 'center',
          data: this.legend_data,
          textStyle: {
            color: '#B6B6B6'
          }
        },
        series: [
          {
            name: this.title,
            minAngle: 20,
            type: 'pie',
            radius: ['30%', '50%'],
            center: ['50%', '43%'],
            // roseType: 'area',
            avoidLabelOverlap: false,
            data: this.series_data,
            label: {
              normal: {
                position: 'outer',
                formatter: function (p) {
                  if (p.data.amount === 'isAmount') {
                    // 如果amount存在，则给数值添加$符号
                    // 给数值添加千分位
                    let value = '$' + p.value.toFixed(0).toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,')
                    return value + '\n\n' + '(' + p.percent.toFixed(0) + '%)'
                  } else {
                    let value = p.value.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,')
                    return value + '\n\n' + '(' + p.percent.toFixed(2) + '%)'
                  }
                }
              }
            },
            labelLine: {
              length: 10,
              length2: 10,
              lineStyle: {
                width: 0.5,
                shadowColor: 'rgba(0, 0, 0, 0.5)',
                shadowBlur: 10,
                shadowOffsetY: 1
              }
            }
          }
        ]
      })
      myCharts.on('click', this.clickEchats)
    },
    clickEchats (param) {
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
  width: 340px;
  height: 300px;
  margin: 0 auto;
}
.doubleechartbox {
  width: 800px;
  height: 600px;
  margin: 0 auto;
}
</style>
