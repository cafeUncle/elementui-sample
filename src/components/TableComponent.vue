<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-dialog title="图表" :visible.sync="showDialog">
      <div id="line" style="width: 600px;height:400px;"></div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showDialog = false">取 消</el-button>
        <el-button type="primary" @click="showDialog = false">确 定</el-button>
      </div>
    </el-dialog>
    <el-form :inline="true" :model="formInline" ref="formInline" :rules="formInlineRule" class="demo-form-inline">
      <el-form-item label="审批人" prop="user">
        <el-input v-model="formInline.user" placeholder="审批人"></el-input>
      </el-form-item>
      <el-form-item label="队列" prop="curType" label-width="80px">
        <el-select v-model="formInline.curType" placeholder="队列" @change="onTypeChange">
          <el-option
            v-for="item in serviceType"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item slot-scope="props">
          <!-- 如何为 elementUI 扩展 iconfont 的图标库 https://www.jianshu.com/p/22558a1b7b97 图标大小可以在 iconfont.css 中修改 font-size -->
          <el-button type="primary" @click="parse" class="iconfont icon-zhexiantu">主要按钮</el-button>
          <el-button type="primary" @click="show(props.row)">显示图表</el-button>
          <el-button type="primary" @click="checkForm('formInline')" class="el-icon-caret-right">校验按钮</el-button>
      </el-form-item>
    </el-form>
    <el-table
      @click="handleClick"
      :data="dataTable"
      style="width: 100%">
      <el-table-column type="expand">
        <template scope="props">
          <el-table
            :data="props.row.subRows"
            style="width: 100%">
            <el-table-column
              align="left"
              header-align="center"
              label="时间"
              prop="time">
            </el-table-column>
            <el-table-column
              label="次数"
              prop="times"
              :filters="[{ text: '1', value: 1 }, { text: '2', value: 2 }, { text: '4', value: 4 }]"
              :filter-method="filterTag"
              filter-placement="bottom-end">
            </el-table-column>
          </el-table>
        </template>
      </el-table-column>
      <el-table-column
        label="name"
        prop="name">
      </el-table-column>
      <el-table-column
        label="times"
        prop="times">
      </el-table-column>
      <el-table-column
        label="操作">
        <el-button type="primary" size="small">操作</el-button>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import serviceType from '@/store/service_type'
import res from '@/store/res'
import '@/assets/iconfont/iconfont.css'
import echarts from 'echarts'

export default {
  props: ['msg'],
  data () {
    const validateUser = (rule, value, callback) => {
      if (!value || value === '') {
        return callback(new Error('请输入user'))
      }
      console.log(this.formInline.curType)
      return callback()
    }
    return {
      serviceType: serviceType,
      formInline: {
        user: null,
        curType: null
      },
      formInlineRule: {
        user: [{ validator: validateUser, trigger: 'blur' }]
      },
      tableData5: null,
      showDialog: false
    }
  },
  computed: {
    dataTable () {
      if (!this.tableData5) {
        return []
      }
      const data = this.tableData5
      return [
        { name: '召回内容次数', times: this.getTimes(data.fetch_times), subRows: this.getTimeRows(data.fetch_times) },
        { name: '召回内容条目数', times: this.getTimes(data.fetch_count), subRows: this.getTimeRows(data.fetch_count) },
        { name: '过滤后剩余的内容条目数', times: this.getTimes(data.filtered_reason_count), subRows: this.getTimeRows(data.filtered_reason_count) },
        { name: '过滤时被丢弃次数', times: this.getTimes(data.discard_times), subRows: this.getTimeRows(data.discard_times) },
        { name: '过滤时被丢弃条目数', times: this.getTimes(data.discard_count), subRows: this.getTimeRows(data.discard_count) },
        { name: '排序后在前 6 条的内容条目数', times: this.getTimes(data.ranking_win_count), subRows: this.getTimeRows(data.ranking_win_count) },
        { name: '重排序后在前 6 条的内容条目数', times: this.getTimes(data.tuning_win_count), subRows: this.getTimeRows(data.tuning_win_count) },
        { name: '下发次数', times: this.getTimes(data.page_times), subRows: this.getTimeRows(data.page_times) }
      ]
    }
  },
  methods: {
    show (row) {
      console.log(row)
      this.showDialog = true // 此时触发了dialog show，但dom渲染是异步的，直接获取其中的div可能获取不到
      // Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和MessageChannel，如果执行环境不支持，会采用 setTimeout(fn, 0)代替。
      // [简单理解Vue中的nextTick](https://www.jianshu.com/p/a7550c0e164f)
      this.$nextTick(() => {
        // 基于准备好的dom，初始化echarts实例
        const myChart = echarts.init(document.getElementById('line'))

        // 指定图表的配置项和数据
        const option = {
          title: {
            text: 'ECharts 入门示例'
          },
          tooltip: {},
          legend: {
            data: ['销量']
          },
          xAxis: {
            data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
          },
          yAxis: {},
          series: [{
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
          }]
        }

        // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option)
      })
    },
    checkForm (formName) {
      this.$refs[formName].validate((valid) => {
        console.log(valid)
        return valid
      })
    },
    onTypeChange (a) {
      console.log(a)
    },
    getTimes (col) {
      return col ? col.times : 0
    },
    getTimeRows (col) {
      if (!col || col === {}) {
        return []
      }
      if (!col.timeRows || !col.timeRows.data || col.timeRows.data === {}) {
        return []
      }
      const map = col.timeRows.data
      const arr = []
      for (const k in map) {
        arr.push({ time: k, times: map[k] })
      }
      return arr
    },
    handleClick (row) {
      console.log(row)
    },
    selectChange (val) {
      console.log(val)
    },
    formatter (row, column) {
      console.log(column)
      return row.address
    },
    filterTag (value, row) {
      console.log(row, value)
      return row.times === value
    },
    parse () {
      console.log(res)
      const actionMap = {}
      res.map(r => {
        const [, time] = r.key.split(':')
        const cols = r.cols
        for (const i in cols) {
          const col = cols[i]
          const [colActionKey, actionReason] = col[0].split(':')
          const times = col[1]
          if (!actionMap[colActionKey]) {
            actionMap[colActionKey] = {}
          }
          if (!actionMap[colActionKey].times) {
            actionMap[colActionKey].times = 0
          }
          actionMap[colActionKey].times = actionMap[colActionKey].times + times
          if (!actionMap[colActionKey].timeRows) {
            actionMap[colActionKey].timeRows = { data: {}, reasons: {} }
          }
          if (!actionMap[colActionKey].timeRows.data[time]) {
            actionMap[colActionKey].timeRows.data[time] = 0
          }
          actionMap[colActionKey].timeRows.data[time] = actionMap[colActionKey].timeRows.data[time] + times
          if (actionReason) {
            if (!actionMap[colActionKey].timeRows.reasons[actionReason]) {
              actionMap[colActionKey].timeRows.reasons[actionReason] = 0
            }
            actionMap[colActionKey].timeRows.reasons[actionReason] = actionMap[colActionKey].timeRows.reasons[actionReason] + times
          }
        }
      })
      console.log(actionMap)
      this.tableData5 = actionMap
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!--<style scoped lang="scss">-->
<!--h3 {-->
<!--  margin: 40px 0 0;-->
<!--}-->
<!--ul {-->
<!--  list-style-type: none;-->
<!--  padding: 0;-->
<!--}-->
<!--li {-->
<!--  display: inline-block;-->
<!--  margin: 0 10px;-->
<!--}-->
<!--a {-->
<!--  color: #42b983;-->
<!--}-->
<!--</style>-->
