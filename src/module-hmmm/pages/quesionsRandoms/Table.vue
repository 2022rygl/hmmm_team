<template>
  <el-table :data="tableData" style="width: 100%" fit highlight-current-row>
    <el-table-column label="编号" prop="id" width="220"> </el-table-column>
    <el-table-column
      label="题型"
      prop="questionType"
      width="80"
      :formatter="formatterFn"
    >
    </el-table-column>
    <el-table-column label="题目编号" prop="questionIDs" width="220">
      <template slot-scope="{ row }">
        <div
          v-for="item in row.questionIDs"
          :key="item.number"
          @click="getPreview(item)"
        >
          <span class="quesItem">
            {{ item.number }}
          </span>
        </div>
      </template>
    </el-table-column>
    <el-table-column label="录入时间" prop="addTime"> </el-table-column>
    <el-table-column label="答题时间(s)" prop="totalSeconds"> </el-table-column>
    <el-table-column label="正确率(%)" prop="accuracyRate"> </el-table-column>
    <el-table-column label="录入人" prop="userName"> </el-table-column>
    <el-table-column label="操作" prop="name" width="80">
      <template slot-scope="{ row }">
        <el-button
          type="danger"
          size="small"
          icon="el-icon-delete"
          circle
          @click="delQuestion(row)"
        />
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
import { removeRandoms } from '@/api/hmmm/questions.js'
import { questionType } from '@/api/hmmm/constants.js'

export default {
  name: 'Table',
  components: {},
  props: {
    tableData: {
      type: Array,
      default: () => ([])
    },
    previewDialog: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {}
  },
  watch: {},
  computed: {},
  methods: {
    getPreview (item) {
      this.$emit('update:previewDialog', true)
      this.$emit('getDetail', item)
    },
    async delQuestion (row) {
      console.log(row)
      try {
        await this.$confirm('此操作将永久删除该题组, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        await removeRandoms(row)
        this.$message.success('删除成功')
        this.$emit('updateList')
      } catch (error) {
        console.log(error)
      }
    },
    formatterFn (row, column, cellValue) {
      const res = questionType.find((item) => item.value === +cellValue)
      return res ? res.label : '其他题型'
    }
  },
  // 生命周期 - 创建完成（访问当前this实例）
  created () {}
}
</script>
<style lang="scss"  scoped >
.el-table {
  .el-button--danger {
    color: #f56c6c;
    background: #fef0f0;
    border-color: #fbc4c4;
  }

  .el-button--danger:hover {
    background: #f78989;
    border-color: #f78989;
    color: #fff;
  }
  .el-button--danger:focus {
    background: #f56c6c;
    border-color: #f56c6c;
    color: #fff;
  }
}

.quesItem {
  cursor: pointer;
  color: rgb(0, 153, 255);
}
</style>
