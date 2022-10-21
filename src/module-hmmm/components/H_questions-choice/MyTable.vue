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
        width="120"
        >
      </el-table-column>
      <el-table-column
        prop="catalog"
        label="目录"
        width="120">
      </el-table-column>
      <el-table-column
        prop="questionType"
        label="题型"
        :formatter="tablevalue"
        width="120">
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
        width="80"
        :formatter="tablevalue">
      </el-table-column>
      <el-table-column
        prop="creator"
        label="录入人"
        width="120"
        >
      </el-table-column>
      <el-table-column
        prop="chkState"
        label="审核状态"
        width="120"
        :formatter="tablevalue"
        >
        </el-table-column>
        <el-table-column
        prop="chkRemarks"
        label="审核意见"
        width="150"
        >
        </el-table-column>
         <el-table-column
        prop="chkUser"
        label="审核人"
        width="120"
        >
        </el-table-column>
        <el-table-column
        prop="publishState"
        label="发布状态"
        width="150"
        :formatter="tablevalue"
        >
        </el-table-column>
      <el-table-column
      width="200"
      label="操作"
      fixed="right"
      >
      <template slot-scope="{row}">
          <el-button class="txt" @click="details(row)" type="text">预览</el-button>
          <el-button class="txt" :disabled="row.chkState !== 0" @click="isExamineShow=true,id=row.id"  type="text">审核</el-button>
          <el-button class="txt" :disabled="row.publishState === 1" @click="edit(row)" type="text">修改</el-button>
          <el-button class="txt" @click="publish(row)" type="text">{{row.publishState === 1? '下架':'上架'}}</el-button>
          <el-button class="txt" :disabled="row.publishState === 1" @click="del(row)" type="text">删除</el-button>
      </template>
      </el-table-column>
    </el-table>
    <my-dialog
    :dialogVisible.sync="dialogVisible"
    :data="dialogData"
    ></my-dialog>
    <el-dialog
    :visible.sync="isExamineShow"
    width="21%"
    title="题目审核"
    @close="shutDown"
    >
    <el-radio v-model="chkState" :label="1">通过</el-radio>
    <el-radio v-model="chkState" :label="2">拒绝</el-radio>
    <el-input
      style="margin-top: 18px;"
      type="textarea"
      :rows="2"
      placeholder="请输入审核意见"
      v-model="chkRemarks">
    </el-input>
    <span slot="footer" class="dialog-footer">
        <el-button @click="shutDown">取 消</el-button>
        <el-button type="primary" @click="choice">确 定</el-button>
     </span>
    </el-dialog>
     </div>
</template>

<script>
import * as data from '@/api/hmmm/constants'
import dayjs from 'dayjs'
import { remove, detail, choicePublish, choiceCheck } from '../../../api/hmmm/questions'
import MyDialog from '../H_questions/MyDialog.vue'
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
      isExamineShow: false,
      chkState: 1,
      chkRemarks: '',
      dialogData: {},
      id: ''
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
    async publish (row) {
      try {
        let num = 0
        if (row.publishState === 0) num = 1
        await this.$confirm(`您确认${num === 0 ? '下架' : '上架'}这道题目吗?`, '提示', {
          type: 'warning'
        })
        const res = await choicePublish({ id: row.id, publishState: num })
        if (!res) return this.$message.error(num === 0 ? '下架失败' : '上架失败')
        this.$message.success(num === 0 ? '下架成功' : '上架成功')
        this.$emit('table')
      } catch (error) {
        //
      }
    },
    async choice () {
      if (this.chkRemarks.trim() === '') return this.$message.warning('请输入审核意见')
      const res = await choiceCheck({ id: this.id, chkState: this.chkState, chkRemarks: this.chkRemarks })
      if (!res) return alert('失败')
      this.shutDown()
      this.$message.success('操作成功')
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
      } else if (column.label === '审核状态') {
        data.chkType.forEach(item => {
          if (item.value === +cellValue) {
            cellValue = item.label
          }
        })
      } else if (column.label === '发布状态') {
        if (+cellValue === 1 && +row.chkState === 1) return '已发布'
        return '待发布'
      }
      return cellValue
    },
    shutDown () {
      this.isExamineShow = false
      this.chkState = 1
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
.txt{
    font-size: 12px;
}
</style>
