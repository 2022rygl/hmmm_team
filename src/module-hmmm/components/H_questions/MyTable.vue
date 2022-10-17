<template>
     <div>
        <el-table
      :data="tableData"
      style="width: 100%"
      class="table"
      >
      <el-table-column
        prop="number"
        label="试题编号"
        width="120"
        >
      </el-table-column>
      <el-table-column
        prop="subject"
        label="学科"
        >
      </el-table-column>
      <el-table-column
        prop="catalog"
        label="目录">
      </el-table-column>
      <el-table-column
        prop="questionType"
        label="题型"
        :formatter="tablevalue">
      </el-table-column>
      <el-table-column
        label="题干"
        width="280">
        <template slot-scope="{row}">
            <div v-html="row.question">
            </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="addDate"
        label="录入时间"
        width="180"
        :formatter="tablevalue">
      </el-table-column>
      <el-table-column
        prop="difficulty"
        label="难度"
        :formatter="tablevalue">
      </el-table-column>
      <el-table-column
        prop="creator"
        label="录入人"
        >
      </el-table-column>
      <el-table-column
      width="180"
      label="操作"
      >
      <template slot-scope="{row}">
         <el-tooltip content="预览" placement="bottom" effect="light">
          <el-button @click="details(row)" plain type="primary" icon="el-icon-view" circle></el-button>
         </el-tooltip>
          <el-tooltip content="修改" placement="bottom" effect="light">
          <el-button @click="edit(row)" plain type="success" icon="el-icon-edit" circle></el-button>
          </el-tooltip>
          <el-tooltip content="删除" placement="bottom" effect="light">
          <el-button @click="del(row)" plain type="danger" icon="el-icon-delete" circle></el-button>
          </el-tooltip>
          <el-tooltip content="加入精选" placement="bottom" effect="light">
          <el-button @click="selected(row)"  plain type="warning" icon="el-icon-check" circle></el-button>
          </el-tooltip>
      </template>
      </el-table-column>
    </el-table>
    <my-dialog
    :dialogVisible.sync="dialogVisible"
    :data="dialogData"
    ></my-dialog>
     </div>
</template>

<script>
import * as data from '@/api/hmmm/constants'
import dayjs from 'dayjs'
import { remove, choiceAdd, detail } from '../../../api/hmmm/questions'
import MyDialog from './MyDialog.vue'
export default {
  components: { MyDialog },
  props: {
    tableData: {
      type: Array,
      default: () => ([])
    }
  },
  data () {
    return {
      directoryData: [],
      dialogVisible: false,
      dialogData: {}
    }
  },
  methods: {
    async details (row) {
      this.dialogVisible = true
      const { data } = await detail(row)
      this.dialogData = data
      console.log(data)
    },
    async del (row) {
      await this.$confirm('此操作将永久删除该题目, 是否继续?', '提示', {
        type: 'warning'
      })
      const { data } = await remove(row)
      if (!data.success) return this.$message.error('删除失败')
      this.$message.success('删除成功')
      this.$emit('table')
    },
    async selected ({ id }) {
      await choiceAdd({ id, choiceState: 1 })
      this.$message.success('加入成功')
      this.$emit('table')
    },
    tablevalue (row, column, cellValue) {
      if (column.label === '题型') {
        data.questionType.forEach(item => {
          if (item.value === +cellValue) {
            cellValue = item.label
          }
        })
      } else if (column.label === '录入时间') {
        cellValue = dayjs(cellValue).format('YYYY-MM-DD HH:mm:ss')
      } else if (column.label === '难度') {
        data.difficulty.forEach(item => {
          if (item.value === +cellValue) {
            cellValue = item.label
          }
        })
      }
      return cellValue
    }
  }
}
</script>
<style lang="less">
table{
  .el-button--medium.is-circle{
    padding: 8px;
  }
}

</style>
