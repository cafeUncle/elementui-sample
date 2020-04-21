<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item label="审批人">
        <el-input v-model="formInline.user" placeholder="审批人"></el-input>
      </el-form-item>
      <el-form-item label="队列">
        <el-select v-model="formInline.curType" placeholder="队列">
          <el-option
            v-for="item in serviceType"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
    </el-form>
    <el-table
      @click="handleClick"
      :data="tableData5"
      style="width: 100%">
      <el-table-column type="expand">
        <template scope="props">
          <el-table
            :data="props.row.subRows"
            style="width: 100%">
            <el-table-column
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
        label="商品 ID"
        prop="id">
      </el-table-column>
      <el-table-column
        label="商品名称"
        prop="name">
      </el-table-column>
      <el-table-column
        label="描述"
        prop="desc">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import serviceType from '@/store/service_type'

export default {
  props: ['msg'],
  data () {
    return {
      serviceType: serviceType,
      formInline: {
        user: null,
        curType: null
      },
      tableData5: [{
        id: '12987122',
        name: '好滋好味鸡蛋仔',
        category: '江浙小吃、小吃零食',
        desc: '荷兰优质淡奶，奶香浓而不腻',
        address: '上海市普陀区真北路',
        shop: '王小虎夫妻店',
        shopId: '10333',
        subRows: [
          {
            time: '二',
            times: 2
          }, {
            time: '四',
            times: 4
          }
        ]
      }, {
        id: '12987123',
        name: '好滋好味鸡蛋仔',
        category: '江浙小吃、小吃零食',
        desc: '荷兰优质淡奶，奶香浓而不腻',
        address: '上海市普陀区真北路',
        shop: '王小虎夫妻店',
        shopId: '10333',
        subRows: [
          {
            time: '二',
            times: 2
          }, {
            time: '四',
            times: 4
          }
        ]
      }, {
        id: '12987125',
        name: '好滋好味鸡蛋仔',
        category: '江浙小吃、小吃零食',
        desc: '荷兰优质淡奶，奶香浓而不腻',
        address: '上海市普陀区真北路',
        shop: '王小虎夫妻店',
        shopId: '10333',
        subRows: [
          {
            time: '二',
            times: 2
          }, {
            time: '四',
            times: 4
          }
        ]
      }, {
        id: '12987126',
        name: '好滋好味鸡蛋仔',
        category: '江浙小吃、小吃零食',
        desc: 'd荷兰优质淡奶，奶香浓而不腻',
        address: '上海市普陀区真北路',
        shop: '王小虎夫妻店',
        shopId: '10333',
        subRows: [
          {
            time: '二',
            times: 2
          }, {
            time: '四',
            times: 4
          }
        ]
      }]
    }
  },
  methods: {
    handleClick (row) {
      console.log(row)
    },
    selectChange (val) {
      console.log(val)
    },
    formatter (row, column) {
      return row.address
    },
    filterTag (value, row) {
      console.log(row, value)
      return row.times === value
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
