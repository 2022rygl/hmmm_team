<template>
  <div class='questions-choice'>
    <el-card>
      <div class="btn_wrapper">
        <span style="font-size: 12px; color: pink;">说明：目前支持学科和关键字条件筛选</span>
         <el-button type="success" icon="el-icon-edit" size="small">新增试题</el-button>
      </div>
      <my-form @search="searchtable"></my-form>
      <el-tabs v-model="activeName" type="card">
        <el-tab-pane label="全部" name="全部" />
        <el-tab-pane label="待核实" name="待审核" />
        <el-tab-pane label="已核实" name="通过" />
        <el-tab-pane label="已拒绝" name="拒绝" />
      </el-tabs>
      <el-alert
        style="margin-bottom: 15px;"
        :title="`数据一共${counts}条`"
        :closable="false"
        type="info"
        show-icon
        />
      <my-table :tableData="tableData" @table="table"></my-table>
      <el-pagination
      class="page"
      background
      @size-change="table"
      @current-change="table"
      :page-count="pages"
      :current-page.sync="page"
      :page-sizes="[5, 10, 20, 50]"
      :page-size.sync="pagesize"
      layout="prev, pager, next, sizes, jumper"
      >
    </el-pagination>
    </el-card>
  </div>
</template>

<script>
import { chkType } from '@/api/hmmm/constants'
import MyForm from '../../module-hmmm/components/H_questions/MyForm.vue'
import MyTable from '../../module-hmmm/components/H_questions-choice/MyTable.vue'
import { choice } from '../../api/hmmm/questions'
export default {
  components: { MyForm, MyTable },
  data () {
    return {
      tableData: [],
      pages: 0,
      page: 1,
      pagesize: 5,
      counts: 0,
      activeName: ''
    }
  },
  methods: {
    async table () {
      const { data } = await choice({
        page: this.page,
        pagesize: this.pagesize,
        chkState: this.chkState
      })
      this.tableData = data.items
      this.pages = data.pages
      this.counts = data.counts
      console.log(data)
    },
    async searchtable (formData) {
      this.page = 1
      const arr = Object.keys(formData).filter(item => formData[item])
      const form = {}
      arr.forEach(item => form[item] = formData[item])
      const { data } = await choice({
        page: this.page,
        pagesize: this.pagesize,
        chkState: this.chkState,
        ...form
      })
      this.tableData = data.items
      this.pages = data.pages
      this.counts = data.counts
    }

  },
  watch: {
    chkState: 'table'
  },
  computed: {
    chkState () {
      let num
      chkType.forEach(item => {
        if (item.label === this.activeName) {
          num = item.value
        }
      })
      return num
    }
  },
  created () {
    this.table()
  }
}

</script>

<style scoped lang='less'>
.questions-choice{
    padding: 0 10px;
    margin: 10px 0;
    .btn_wrapper{
      display: flex;
      justify-content: space-between;
      margin-bottom: 15px;
    }
    .page{
      display: flex;
      justify-content: flex-end;
      margin-top: 20px;
    }
}
</style>
