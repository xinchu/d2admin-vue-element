<template>
  <Container type="ghost" :responsive="true" class="demo-chart-index">
    <el-row :gutter="20">
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(0)" title="G2LineBase"></ChartCardHeader>
          <G2LineBase :ref="chart[0].refName" v-bind="chart[0]" @ready="isReady(0)"></G2LineBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(1)" title="G2LineStep"></ChartCardHeader>
          <G2LineStep :ref="chart[1].refName" v-bind="chart[1]" @ready="isReady(1)"></G2LineStep>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(2)" title="G2ColumnBase"></ChartCardHeader>
          <G2ColumnBase :ref="chart[2].refName" v-bind="chart[2]" @ready="isReady(2)"></G2ColumnBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(3)" title="G2BarBase"></ChartCardHeader>
          <G2BarBase :ref="chart[3].refName" v-bind="chart[3]" @ready="isReady(3)"></G2BarBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(4)" title="G2PieBase"></ChartCardHeader>
          <G2PieBase :ref="chart[4].refName" v-bind="chart[4]" @ready="isReady(4)"></G2PieBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(5)" title="G2NightingaleRoseBase"></ChartCardHeader>
          <G2NightingaleRoseBase :ref="chart[5].refName" v-bind="chart[5]" @ready="isReady(5)"></G2NightingaleRoseBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(6)" title="G2RadarBase"></ChartCardHeader>
          <G2RadarBase :ref="chart[6].refName" v-bind="chart[6]" @ready="isReady(6)"></G2RadarBase>
        </el-card>
      </el-col>
      <el-col class="col" :span="8">
        <el-card class="header-in">
          <ChartCardHeader slot="header" @refresh="handleRefreshData(7)" title="G2AreaBase"></ChartCardHeader>
          <G2AreaBase :ref="chart[7].refName" v-bind="chart[7]" @ready="isReady(7)"></G2AreaBase>
        </el-card>
      </el-col>
    </el-row>
  </Container>
</template>

<script>
export default {
  components: {
    ChartCardHeader: () => import('./components/ChartCardHeader')
  },
  data () {
    return {
      chart: [
        {
          api: {url: '/api/chart/G2Line', data: {type: 'base'}},
          refName: 'G2LineBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Line', data: {type: 'step'}},
          refName: 'G2LineStep',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Column', data: {type: 'base'}},
          refName: 'G2ColumnBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Bar', data: {type: 'base'}},
          refName: 'G2BarBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Pie', data: {type: 'base'}},
          refName: 'G2PieBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2NightingaleRose', data: {type: 'base'}},
          refName: 'G2NightingaleRoseBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Radar', data: {type: 'base'}},
          refName: 'G2RadarBase',
          ready: false,
          data: []
        },
        {
          api: {url: '/api/chart/G2Area', data: {type: 'base'}},
          refName: 'G2AreaBase',
          ready: false,
          data: []
        }
      ]
    }
  },
  computed: {
    // 所有的图表已经 mounted
    ready () {
      return !this.chart.find(e => !e.ready)
    }
  },
  watch: {
    // 在所有的图表 mounted 后加载数据
    ready (ready) {
      if (ready) {
        this.syncData()
        this.showInfo()
      }
    }
  },
  methods: {
    // 显示提示
    showInfo () {
      this.$notify({
        title: '提示',
        message: '点击卡片右上角的刷新按钮可以重新载入某个图表的数据',
        duration: 10000
      })
    },
    // 图表 mounted
    isReady (index) {
      this.chart[index].ready = true
    },
    // 请求图表数据
    syncData () {
      this.$axios.all(this.chart.map(e => this.$axios.post(e.api.url, e.api.data)))
        .then(this.$axios.spread((...res) => {
          res.forEach((e, index) => {
            this.chart[index].data = e
          })
        }))
    },
    // 用户触发了卡片右上角的刷新按钮
    handleRefreshData (index) {
      const api = this.chart[index].api
      this.$axios.post(api.url, api.data)
        .then(res => {
          this.chart[index].data = res
        })
    }
  }
}
</script>

<style lang="scss">
@import './style.scss';
</style>
