<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <!-- 输入框 -->
        <el-form
          :inline="true"
          class="demo-form-inline"
          size="small"
          :model="form"
        >
          <el-row>
            <el-col :span="6">
              <el-form-item label="标签名称">
                <el-input
                  placeholder="请输入"
                  style="width: 205px"
                  v-model="form.tags"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6"
              ><el-form-item label="城市">
                <el-select
                  placeholder="请选择"
                  style="width: 205px"
                  v-model="form.province"
                >
                  <el-option
                    v-for="item in provinces()"
                    :key="item.id"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="地区">
                <el-select v-model="form.city" placeholder="请选择">
                  <el-option
                    v-for="item in citys(form.province)"
                    :key="item.id"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="企业简称">
                <el-input style="width: 205px" v-model="form.shortName" />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="21">
              <el-form-item label="状态" label-width="68px">
                <el-select v-model="form.state" placeholder="请选择">
                  <el-option
                    v-for="item in status"
                    :key="item.id"
                    :label="item.value"
                    :value="item.id"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item>
                <el-button @click="delSearch">清除</el-button>
                <el-button type="primary" @click="Search">搜索</el-button>
              </el-form-item></el-col
            >
            <el-col :span="3">
              <el-button
                type="success"
                icon="el-icon-edit"
                class="addbtn"
                size="small"
                @click="Add"
                >新增技巧</el-button
              ></el-col
            >
          </el-row>
        </el-form>

        <!-- 数据一共几条 -->
        <el-alert
          :title="`数据一共${itemsAll}条`"
          type="info"
          show-icon
          :closable="false"
        >
        </el-alert>
        <!-- 表单 -->
        <el-table :data="items" style="width: 100%">
          <el-table-column type="index" label="序号" width="130" />
          <el-table-column prop="number" label="企业编号" width="130" />
          <el-table-column prop="shortName" label="企业简称" width="110" />
          <el-table-column prop="tags" label="标签" width="140" />
          <el-table-column prop="creatorID" label="创建者" width="130" />
          <el-table-column prop="addDate" label="创建日期" width="130" />
          <el-table-column prop="remarks" label="备注" width="150" />
          <el-table-column
            prop="state"
            label="状态"
            width="130"
            :formatter="formatEmployment"
          />

          <el-table-column label="操作">
            <template slot-scope="{ row }">
              <el-button
                type="primary"
                icon="el-icon-edit"
                circle
                @click="editRow(row)"
              ></el-button>
              <el-button
                :type="row.state ? 'warning' : 'success'"
                :icon="row.state ? 'el-icon-close' : 'el-icon-check'"
                circle
                @click="disabled(row)"
              >
              </el-button>
              <el-button
                type="danger"
                icon="el-icon-delete"
                circle
                @click="del(row)"
              ></el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页组件 -->
        <el-row type="flex" justify="end" align="middle" style="height: 60px">
          <el-pagination
            v-if="itemsAll >= 10"
            background
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="page.page"
            layout="sizes, prev, pager, next, jumper"
            :page-sizes="[5, 10, 15, 20]"
            :page-size="page.pagesize"
            :total="itemsAll"
          >
          </el-pagination>
        </el-row>
      </el-card>
    </div>
    <!-- 新增修改弹框 -->
    <companys-add
      ref="companysAdd"
      :companysAdd.sync="companysAdd"
      @getlist="getlist"
    />
  </div>
</template>

<script>
import { list, remove, disabled } from '@/api/hmmm/companys.js'
import { status } from '@/api/hmmm/constants.js'
import companysAdd from './components/companysAdd.vue'
import { provinces, citys } from '@/api/hmmm/citys.js'
import EnumHireType from '@/api/base/baseApi'
export default {
  components: { companysAdd },
  data () {
    return {
      provinces,
      citys,
      items: [],
      itemsAll: 0,
      state: status,
      companysAdd: false,
      status: EnumHireType.status,
      form: {
        page: 1,
        pagesize: 10,
        tags: '',
        province: '',
        city: '',
        shortName: '',
        state: ''
      },
      page: {
        page: 1,
        pagesize: 10
      }
    }
  },
  created () {
    this.getlist()
  },
  methods: {
    formatEmployment (row, column, cellValue, index) {
      // 要去找 1所对应的值
      const res = this.state.find(item => item.value === cellValue)
      return (res && res.label) || '非正式'
    },
    async getlist () {
      const { data } = await list(this.page)
      this.itemsAll = data.counts
      this.items = data.items
      console.log(this.items)
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
        this.$message.success('删除成功')
        console.log(res)
        this.getlist()
      } catch (error) {
        this.$message.success('删除失败')
        console.log(error)
      }
    },
    Add () {
      this.companysAdd = true
    },
    editRow (row) {
      this.companysAdd = true
      console.log(row)
      this.$refs.companysAdd.addform = {
        shortName: row.shortName,
        company: row.company,
        province: row.province,
        city: row.city,
        tags: row.tags,
        remarks: row.remarks,
        isFamous: true,
        id: row.id,
        number: row.number,
        addDate: row.addDate,
        creatorID: row.creatorID,
        state: row.state
      }
    },
    delSearch () {
      this.form = {
        tags: '',
        province: '',
        city: '',
        shortName: '',
        state: ''
      }
    },
    async Search () {
      const { data } = await list(this.form)
      this.items = data.items
    },
    async disabled (row) {
      console.log(row)
      row.state = +!row.state
      await disabled(row)
    },
    handleSizeChange (val) {
      this.page.pagesize = val
      this.getlist(this.page)
    },
    handleCurrentChange (val) {
      this.page.page = val
      this.getlist(this.page)
    }
  }
}
</script>

<style scoped lang='less'>
</style>
