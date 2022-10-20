<template>
  <div class='questions'>
    <el-card >
      <div class="btn_wrapper">
        <span style="font-size: 12px; color: pink;">说明：目前支持学科和关键字条件筛选</span>
         <el-button type="success" icon="el-icon-edit" size="small">新增试题</el-button>
      </div>
      <my-form  @search="searchtable"></my-form>
       <el-alert
        style="margin-bottom: 15px;"
        :title="`数据一共${counts}条`"
        :closable="false"
        type="info"
        show-icon
        >
      </el-alert>
      <my-table @table="table" :tableData="tableData" ></my-table>
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
import MyForm from '../components/H_questions/MyForm.vue'
import MyTable from '../components/H_questions/MyTable.vue'
import { list } from '../../api/hmmm/questions'
export default {
  components: { MyForm, MyTable },
  data () {
    return {
      tableData: [],
      pages: 0,
      page: 1,
      pagesize: 5,
      counts: 0
    }
  },
  methods: {
    async table () {
      const { data } = await list({
        page: this.page,
        pagesize: this.pagesize
      })
      console.log(data)
      this.tableData = data.items
      this.pages = data.pages
      this.counts = data.counts
    },
    async searchtable (formData) {
      this.page = 1
      const arr = Object.keys(formData).filter(item => formData[item])
      const form = {}
      arr.forEach(item => form[item] = formData[item])
      const { data } = await list({
        page: this.page,
        pagesize: this.pagesize,
        ...form
      })
      this.tableData = data.items
      this.pages = data.pages
      this.counts = data.counts
    }
  },
  created () {
    this.table()
  }
}
</script>

<style scoped lang='less'>
.questions{
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
