<template>
  <div :class="className" :id="id" :style="{height:height,width:width}"></div>
</template>

<script>
  import echarts from 'echarts'
  // 散点图
  require('echarts/lib/chart/scatter')
  // 散点图放大
  require('echarts/lib/chart/effectScatter')
  // 地图
  require('echarts/lib/chart/map')
  // 图例
  require('echarts/lib/component/legend')
  // 提示框
  require('echarts/lib/component/tooltip')
  // 地图geo
  require('echarts/lib/component/geo')
  // 中国地图
  require('echarts/map/js/china')
  import store from '@/store'
  import resize from './mixins/resize'
  export default {
    mixins: [resize],
    props: {
      className: {
        type: String,
        default: 'chart'
      },
      id: {
        type: String,
        default: 'chart'
      },
      width: {
        type: String,
        default: '100%'
      },
      height: {
        type: String,
        default: '100%'
      }
    },
    data() {
      console.log(555)
      return {
        chart: null
      }
    },
    mounted() {
      console.log(6666)
      this.initChart()
    },
    beforeDestroy() {
      if (!this.chart) {
        return
      }
      this.chart.dispose()
      this.chart = null
    },
    methods: {
      initChart() {
        this.chart = echarts.init(document.getElementById(this.id))
        const showLoadingDefault = {
          text: 'loding....',
          color: '#23531',
          textColor: '#fff',
          // 地图背景色
          maskColor: '#272D3A',
          zlevel: 0
        }
        this.chart.setOption({
          backgroundColor: '#272D3A',
          // 标题
          title: {
            text: 'tttttttt',
            left: 'center',
            textStyle: {
              color: '#fff'
            }
          },
          // 地图上圆点的提示
          tooltip: {
            trigger: 'item',
            formatter: function(params) {
              return params.name + ' : ' + params.value[2]
            }
          },
          // 图例按钮 点击可选择哪些不显示
          legend: {
            orient: 'vertical',
            left: 'left',
            top: 'bottom',
            data: ['地区热度', 'top5'],
            textStyle: {
              color: '#fff'
            }
          },
          // 地理坐标系组件
          geo: {
            map: 'china',
            label: {
              // true会显示城市名
              emphasis: {
                show: true
              }
            },
            itemStyle: {
              // 地图背景色
              normal: {
                areaColor: '#465471',
                borderColor: '#282F3C'
              },
              // 悬浮时
              emphasis: {
                areaColor: '#8796B4'
              }
            }
          },
          // 系列列表
          series: [
            {
              name: '地区热度',
              // 表的类型 这里是散点
              type: 'scatter',
              // 使用地理坐标系，通过 geoIndex 指定相应的地理坐标系组件
              coordinateSystem: 'geo',
              data: [],
              // 标记的大小
              symbolSize: 12,
              // 鼠标悬浮的时候在圆点上显示数值
              label: {
                normal: {
                  show: false
                },
                emphasis: {
                  show: false
                }
              },
              itemStyle: {
                normal: {
                  color: '#ddb926'
                },
                // 鼠标悬浮的时候圆点样式变化
                emphasis: {
                  borderColor: '#fff',
                  borderWidth: 1
                }
              }
            },
            {
              name: 'top5',
              // 表的类型 这里是散点
              type: 'effectScatter',
              // 使用地理坐标系，通过 geoIndex 指定相应的地理坐标系组件
              coordinateSystem: 'geo',
              data: [],
              // 标记的大小
              symbolSize: 12,
              showEffectOn: 'render',
              rippleEffect: {
                brushType: 'stroke'
              },
              hoverAnimation: true,
              label: {
                normal: {
                  show: false
                }
              },
              itemStyle: {
                normal: {
                  color: '#f4e925',
                  shadowBlur: 10,
                  shadowColor: '#333'
                }
              },
              zlevel: 1
            }
          ]
        })
        this.chart.showLoading(showLoadingDefault)
        console.log(2222)
        store.commit('openLoading')
        store.dispatch('fetchHeatChinaRealData', this.chart)
        setInterval(() => {
          store.dispatch('fetchHeatChinaRealData', this.chart)
        }, 1000)
      }
    }
  }
</script>
