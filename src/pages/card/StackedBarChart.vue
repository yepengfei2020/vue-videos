<template>
<!--堆叠柱图-->
    <div :id="ecid" class="echarts4" />
</template>
<script>
import { getSYData } from '@/api/data.js'
// import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
// import { debounce } from '@/utils'

export default {
  props: {
    ecid: {
      type: String,
      required: true
    },
    com_card: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      disguisedIndexcode: null,
      comCode: null,
      indexCode: null,
      chrtCode: null
    }
  },
  mounted () {
    this.disguisedIndexcode = this.com_card.disguisedIndexcode
    this.comCode = this.com_card.comCode
    this.indexCode = this.com_card.indexCode
    this.chrtCode = this.com_card.chrtCode

    this.init()
  },
  methods: {
    init () {
      let option = {
        title: {
          show: true, // 显示策略，默认值true,可选为：true（显示） | false（隐藏）
          text: '主标题', // 主标题文本，'\n'指定换行
          x: 'right', // 水平安放位置，默认为'left'，可选为：'center' | 'left' | 'right' | {number}（x坐标，单位px）
          y: 'bottom', // 垂直安放位置，默认为top，可选为：'top' | 'bottom' | 'center' | {number}（y坐标，单位px）
          textStyle: {// 主标题文本样式{"fontSize": 18,"fontWeight": "bolder","color": "#333"}
            color: '#5A5A5A',
            fontSize: 10
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          },
          textStyle: {
            align: 'left'
          },
          formatter: function (params) {
            var res = '<div><p>' + params[0].name + '</p></div>'
            for (var i = 0; i < params.length; i++) {
              res += '<p>' + params[i].seriesName + ':' + params[i].data.value + params[i].data.unit + '</p>'
            }
            return res
          }
        },
        legend: {
          x: 60,
          y: 2,
          itemWidth: 15, // 设置宽度
          itemHeight: 6 // 设置高度
        },
        xAxis: [
          {
            type: 'category',
            data: ['2015年', '2016年', '2017年', '2018年', '2019年'],
            axisPointer: {
              type: 'shadow'
            },
            axisLabel: { // X轴线 标签修改
              show: true,
              rotate: 20,
              interval: 0,
              textStyle: {
                padding: [0, -30, -20, 20], // ---坐标轴名称相对位置
                color: '#5A5A5A' // 坐标值的具体的颜色
              }
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            axisLabel: { // X轴线 标签修改
              textStyle: {
                color: '#5A5A5A' // 坐标值得具体的颜色
              }
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            splitLine: {show: false}
          },
          {
            type: 'value',
            axisLabel: { // X轴线 标签修改
              textStyle: {
                padding: [0, -15, 0, 20], // ---坐标轴名称相对位置
                align: 'right',
                color: '#5A5A5A' // 坐标值得具体的颜色
              }
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            }
          }
        ],
        grid: { // 设置canvas内部表格的内距
          x: 40,
          // y: 50,
          x2: '15%',
          // y2: 60,
          borderWidth: 10
        },
        series: [
          {
            name: '本期赔付率',
            type: 'bar',
            stack: '1',
            data: [],
            barWidth: 10,
            itemStyle: {
              normal: {
                color: '#32749E'
              }
            }
          },
          {
            name: '本期费用率',
            type: 'bar',
            stack: '1',
            data: [],
            barWidth: 10,
            itemStyle: {
              normal: {
                color: '#B31C29'
              }
            }
          },
          {
            name: '同期赔付率',
            type: 'bar',
            stack: '2',
            data: [26, 59, 90, 24, 27],
            barWidth: 10,
            itemStyle: {
              normal: {
                color: '#448CCB'
              }
            }
          },
          {
            name: '同期费用率',
            type: 'bar',
            stack: '2',
            data: [26, 59, 90, 24, 27],
            barWidth: 10,
            itemStyle: {
              normal: {
                color: '#E6350D'
              }
            }
          },
          {
            name: '同比',
            type: 'line',
            yAxisIndex: 1,
            data: [],
            symbolSize: 6,
            lineStyle: {
              color: 'transparent',
              width: 1.5
            }, // 线条的样式
            itemStyle: {
              normal: {
                color: '#FADC58', // 拐点颜色
                lineStyle: {
                // shadowColor: 'rgba(0,0,0,0.4)',
                // shadowBlur: 20,
                // shadowOffsetY: 20,
                  color: '#FADC58', // 改变折线颜色
                  width: 1.5
                }
              }
            }
          }
        ]
      }
      let chart = this.$echarts.init(
        document.getElementById(this.ecid),
        'shine'
      )
      getSYData(this.comCode, this.disguisedIndexcode, this.chrtCode).then(res => {
        var result = res.data.result
        console.log('堆叠柱图:', result)
        this.unit = result[0].unit
        const bbb = []
        const bbb1 = []
        const bbb2 = []
        const bbb3 = []
        const bbb4 = []
        const indexCodes = this.indexCode.split(',')
        const xAxisData = []
        const series0Name = []
        const series2Name = []
        const series4Name = []
        // const series0tongQi = []
        // const series0benQi = []
        // const series2tongQi = []
        // const series2benQi = []
        // const series4Data = []
        const iaf0 = indexCodes[0].split(':')
        const iaf0Data = result.filter(item => item.indexCode === iaf0[0])[0].dmPcDtoExtList
        iaf0Data.forEach(element => {
          const aaa = {}
          const aaa1 = {}
          xAxisData.push(element.busiDate)
          aaa.value = element.benQi
          aaa.unit = this.unit
          aaa1.value = element.tongQi
          aaa1.unit = this.unit
          // series0tongQi.push(element.tongQi)
          // series0benQi.push(element.benQi)
          series0Name.push(element.indexName)
          bbb.push(aaa)
          bbb1.push(aaa1)
        })
        option.xAxis[0].data = xAxisData
        option.series[0].data = bbb
        option.series[0].name = '本期' + series0Name[0]
        option.series[2].data = bbb1
        option.series[2].name = '同期' + series0Name[0]
        // const iaf1 = indexCodes[1].split(':')
        // const iaf1Data = result.filter(item => item.indexCode === iaf1[0])[0].dmPcDtoExtList
        // iaf1Data.forEach(element => {
        //   series1benQi.push(element.benQi)
        //   seeies1tongQi.push(element.tongQi)
        //   series1Name.push(element.indexName)
        //   // console.log('1', series1Name)
        // })
        // // console.log('seeies1tongQi', seeies1tongQi)
        // // console.log('series1Name[0]', '同期' + series1Name[0])
        // option.series[1].data = seeies1tongQi
        // option.series[1].name = '同期' + series1Name[0]

        const iaf2 = indexCodes[2].split(':')
        const iaf2Data = result.filter(item => item.indexCode === iaf2[0])[0].dmPcDtoExtList
        iaf2Data.forEach(element => {
          const aaa2 = {}
          const aaa3 = {}
          // series2tongQi.push(element.tongQi)
          // series2benQi.push(element.benQi)
          aaa2.value = element.benQi
          aaa2.unit = this.unit
          aaa3.value = element.tongQi
          aaa3.unit = this.unit
          series2Name.push(element.indexName)
          bbb2.push(aaa2)
          bbb3.push(aaa3)
        })
        option.series[1].data = bbb2
        option.series[1].name = '本期' + series2Name[0]
        option.series[3].data = bbb3
        option.series[3].name = '同期' + series2Name[0]
        // const iaf3 = indexCodes[3].split(':')
        // const iaf3Data = result.filter(item => item.indexCode === iaf3[0])[0].dmPcDtoExtList
        // iaf3Data.forEach(element => {
        //   series3Data.push(element.benQi)
        //   series3Name.push(element.indexName)
        // })
        const iaf4 = indexCodes[4].split(':')
        const iaf4Data = result.filter(item => item.indexCode === iaf4[0])[0].dmPcDtoExtList
        iaf4Data.forEach(element => {
          const aaa4 = {}
          // series4Data.push(element.tongBi)
          aaa4.value = element.tongBi
          aaa4.unit = this.unit
          series4Name.push(element.indexName)
          bbb4.push(aaa4)
        })
        option.series[4].data = bbb4
        option.series[4].name = '同比'
        const d = xAxisData[xAxisData.length - 1]
        option.title.text = '截止业务日期:' + (d === undefined ? '--' : d)
        chart.setOption(option)
        this.$nextTick(() => {
          chart.resize()
        })
      })
    }
  }
}
</script>
<style >
@import '../../assets/css/wealthInsurance.css';
</style>
