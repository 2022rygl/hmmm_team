<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <!-- 输入框 -->
        <el-row type="flex" justify="space-around">
          <el-col :span="21">
            <el-form
              :inline="true"
              class="demo-form-inline"
              size="small"
              :model="form"
            >
              <el-form-item label="关键字">
                <el-input
                  placeholder="根据文章标题搜索"
                  v-model="form.keyword"
                ></el-input>
              </el-form-item>
              <el-form-item label="状态">
                <el-select placeholder="请选择" v-model="form.state">
                  <el-option
                    v-for="item in status"
                    :key="item.id"
                    :label="item.value"
                    :value="item.id"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item>
                <el-button @click="delsearch">清除</el-button>
                <el-button type="primary" @click="search">搜索</el-button>
              </el-form-item>
            </el-form>
          </el-col>
          <el-col :span="2">
            <el-button
              type="success"
              icon="el-icon-edit"
              class="addbtn"
              size="small"
              @click="Add"
              >新增技巧</el-button
            >
          </el-col>
        </el-row>
        <!-- 数据一共几条 -->
        <el-alert
          :title="`数据一共${itemAllarr}条`"
          type="info"
          show-icon
          :closable="false"
        >
        </el-alert>
        <!-- 表单 -->
        <el-table :data="itemsArr" style="width: 100%">
          <el-table-column type="index" label="序号" width="100" />
          <el-table-column prop="title" label="文章标题" width="390" />
          <el-table-column prop="visits" label="阅读数" width="140" />
          <el-table-column prop="username" label="录入人" width="160" />
          <el-table-column prop="createTime" label="录入时间" width="200" />
          <el-table-column
            prop="state"
            label="状态"
            width="100"
            :formatter="formatEmployment"
          />
          <el-table-column prop="visits" label="操作">
            <template slot-scope="{ row }">
              <el-button type="text" size="small" @click="show(row)"
                >预览</el-button
              >
              <el-button type="text" size="small" @click="disabled(row)">{{
                row.state === 1 ? '禁用' : '启用'
              }}</el-button>
              <el-button
                type="text"
                size="small"
                @click="change(row)"
                :disabled="row.state === 0"
                >修改</el-button
              >
              <el-button
                type="text"
                size="small"
                @click="del(row)"
                :disabled="row.state === 0"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
    <!-- 预览 -->
    <articles-show ref="articlesShow" :articlesShow.sync="articlesShow" />
    <!-- 新增 /修改-->
    <articles-add
      ref="articlesAdd"
      :articlesAdd.sync="articlesAdd"
      @getlist="getlist"
    ></articles-add>
  </div>
</template>

<script>
import { list, remove, changeState } from '@/api/hmmm/articles.js'
import EnumHireType from '@/api/base/baseApi'
import articlesShow from './components/articlesShow.vue'
import ArticlesAdd from './components/articlesAdd.vue'
export default {
  components: { articlesShow, ArticlesAdd },
  data () {
    return {
      itemsArr: [],
      itemAllarr: 0,
      status: EnumHireType.status,
      articlesShow: false,
      articlesAdd: false,
      quyu: '',
      state: EnumHireType.status,
      form: {
        keyword: '',
        state: '',
        page: 1,
        pagesize: 10
      }
    }
  },
  created (row) {
    this.getlist()
  },
  methods: {
    formatEmployment (row, column, cellValue, index) {
      // 要去找 1所对应的值
      const res = this.status.find(item => item.id == cellValue)
      return (res && res.value) || '非正式'
    },
    // 页面数据
    async getlist () {
      const { data } = await list()
      this.itemAllarr = data.counts
      this.itemsArr = data.items
    },
    async show (row) {
      console.log(row)
      this.articlesShow = true
      this.$refs.articlesShow.list = row
    },
    async Add () {
      this.articlesAdd = true
    },
    async change (row) {
      console.log(row)
      this.articlesAdd = true
      this.$refs.articlesAdd.addform = {
        id: row.id,
        title: row.title,
        articleBody: row.articleBody,
        videoURL: row.videoURL
      }
    },
    async del (row) {
      console.log(row)
      try {
        await this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
          confirmButtonText: '确认',
          cancelButtonText: '取消',
          type: 'warning'
        })
        const res = await remove(row)
        console.log(res)
        this.$message.success('删除成功')
        this.getlist()
      } catch (error) {
        this.$message.success('删除失败')
        console.log(error)
      }
    },
    async disabled (row) {
      row.state = +!row.state
      await changeState(row)
    },
    delsearch () {
      this.form = {
        keyword: '',
        state: ''
      }
    },
    async search () {
      const { data } = await list(this.form)
      this.itemsArr = data.items
    }
  }
}
</script>

<style scoped lang='less'>
.addbtn {
  font-size: 12px;
  border-radius: 3px;
}
.el-alert {
  margin-bottom: 20px;
}
.searchfrom {
  display: flex;
}
</style>
