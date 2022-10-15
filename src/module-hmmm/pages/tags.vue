<template>
  <div class="container">
    <div class="dashboard-container">
      <div class="app-container">
        <el-card class="box-card">
          <!-- 搜索信息表单  -->
          <div class="searchfrom">
            <el-form
              :inline="true"
              :model="pages"
              class="demo-form-inline"
              size="small"
            >
              <el-form-item label="标签名称" style="margin-left: 12px">
                <el-input v-model="pages.tagName"></el-input>
              </el-form-item>
              <el-form-item label="状态" style="margin-left: 40px">
                <el-select v-model="pages.state" placeholder="请选择">
                  <el-option label="已启用" value="1"></el-option>
                  <el-option label="已禁用" value="0"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item>
                <el-button @click="purgx" size="small">清除</el-button>
                <el-button type="primary" @click="onSubmit" size="small"
                  >查询</el-button
                >
              </el-form-item>
            </el-form>
            <!-- 新增标签按钮 -->
            <el-button
              type="success"
              icon="el-icon-edit"
              class="newlabel"
              size="small"
              @click="dialogVisible = true"
              >新增标签</el-button
            >
          </div>
          <!-- 数据统计 -->
          <div class="statistics">
            <i class="el-icon-info"></i
            ><span>数据一共 {{ labellist.length }} 条</span>
          </div>
          <el-table
            ref="singleTable"
            :data="labellist"
            @current-change="handleCurrentChange"
            style="width: 100%"
          >
            <el-table-column label="序号" type="index" width="80">
            </el-table-column>
            <el-table-column prop="subjectName" label="所属学科" width="285">
            </el-table-column>
            <el-table-column prop="tagName" label="标签名称" width="280">
            </el-table-column>
            <el-table-column prop="username" label="创建者" width="280">
            </el-table-column>
            <el-table-column prop="addDate" label="创建日期" width="280">
            </el-table-column>
            <el-table-column label="状态" width="280">
              <template slot-scope="scope">
                {{ scope.row.state !== 1 ? '已禁用' : '已启用' }}
              </template>
            </el-table-column>
            <el-table-column label="操作" width="155">
              <template slot-scope="scope">
                <el-button @click="deleteRow(scope.row)" type="text">
                  {{ scope.row.state ? '禁用' : '启用' }}
                </el-button>
                <el-button
                  @click="modifylabel(scope.row)"
                  type="text"
                  :disabled="scope.row.state == 1"
                >
                  修改
                </el-button>
                <el-button
                  @click="deletelabel(scope.row)"
                  type="text"
                  :disabled="scope.row.state == 1"
                >
                  删除
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <!-- 分页 -->
          <template>
            <div class="pagination">
              <div>
                <el-pagination
                  background
                  layout="prev, pager, next"
                  :total="20"
                  @prev-click="pageleft()"
                  @next-click="pageright()"
                >
                </el-pagination>
              </div>
              <div class="block">
                <el-pagination
                  @size-change="handleSizeChange"
                  @current-change="handleCurrentChange"
                  :pages="pages.page"
                  :page-sizes="[5, 10, 20, 30]"
                  :page-size="10"
                  layout="total, sizes, jumper"
                >
                </el-pagination>
              </div>
            </div>
          </template>
        </el-card>
      </div>
    </div>
    <!-- 新增/更改 弹出框 -->
    <el-dialog title="新增/修改目录" :visible.sync="dialogVisible" width="22%">
      <span>
        <el-form
          :model="addlabels"
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="活动区域" prop="region">
            <el-select
              v-model="addlabels.subjectID"
              placeholder="请选择活动区域"
            >
              <el-option
                v-for="(item, index) in subjectlist"
                :key="index"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="活动名称" prop="name">
            <el-input v-model="addlabels.tagName"></el-input>
          </el-form-item>
        </el-form>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addlabel">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { GETlabellist, simple, remove, add, updatelabel, changeState } from '@/api/hmmm/tags.js'
export default {
  data () {
    return {
      // 新增标签弹框
      dialogVisible: false,
      // 页数
      pages: {
        page: 1,
        pagesize: '10',
        tagName: '',
        state: ''
      },
      // 学科列表
      subjectName: '',
      subjectlist: [],
      // 标签列表
      labellist: [],
      // 删除 当前项
      labelrow: {
      },
      // 新建 / 修改
      addlabels: {
        id: '',
        subjectID: '',
        tagName: '',
        state: ''
      },
      ruleForm: {
        name: '',
        region: ''
      },
      rules: {
        name: [
          { required: true, message: '请输入活动名称', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.labellistapi() // 列表
    this.simpleapi() // 详情
  },
  methods: {
    // 列表
    async labellistapi () {
      const data = await GETlabellist(this.pages)
      this.labellist = data.data.items
      this.purgx()
      // console.log(this.labellist)
    },
    // 学科列表
    async simpleapi () {
      const data = await simple(this.subjectName)
      this.subjectlist = data.data
      // console.log(this.subjectlist)
    },
    // 删除
    async removeapi () {
      const data = await remove(this.labelrow)
      if (data.data.success === true) {
        this.labellistapi()
        this.$message({
          message: '删除成功',
          type: 'success'
        })
      } else {
        this.$message.error('删除失败')
      }
    },
    // 新增
    async addlabelapi () {
      const data = await add(this.addlabels)
      if (data.data.id !== '') {
        this.$message({
          message: '创建成功',
          type: 'success'
        })
      } else {
        this.$message.error('创建失败')
      }
      this.addlabels.id = ''
      this.addlabels.subjectID = ''
      this.addlabels.tagName = ''
    },

    // 修改
    async updatelabelapi () {
      const data = await updatelabel(this.addlabels)
      if (data.data.id !== '') {
        this.$message({
          message: '修改成功',
          type: 'success'
        })
      } else {
        this.$message.error('修改失败')
      }
      this.addlabels.id = ''
      this.addlabels.subjectID = ''
      this.addlabels.tagName = ''
    },
    // 状态
    async changeStateapi () {
      const data = await changeState(this.addlabels)
      if (data.data.success === true) {
        this.$message({
          message: '状态更新成功',
          type: 'success'
        })
      }
    },
    onSubmit () {
      // console.log(this.pages)
      this.labellistapi()
    },
    purgx () { // 清除搜索框内容
      this.pages.tagName = ''
      this.pages.state = ''
    },
    // 新增标签
    addlabel () {
      if (this.addlabels.id === '') {
        this.addlabelapi()
        this.labellistapi()
        this.dialogVisible = false
      } else if (this.addlabels.id !== '') {
        this.updatelabelapi()
        this.dialogVisible = false
      }
    },
    // 修改
    modifylabel (row) {
      console.log(row)
      this.addlabels.id = row.id
      this.addlabels.subjectID = row.subjectID
      this.addlabels.tagName = row.tagName
      this.dialogVisible = true
    },
    // 删除
    deletelabel (row) {
      this.labelrow = row
      this.removeapi()
    },
    // 列表序号
    handleCurrentChange (val) {
      this.currentRow = val
    },
    deleteRow (row) {
      this.addlabels.id = row.id
      if (row.state === 1) {
        row.state = 0
        this.addlabels.state = 0
      } else {
        row.state = 1
        this.addlabels.state = 0
      }
      console.log()
      this.changeStateapi()
    },
    // 分页
    handleSizeChange (val) {
      console.log(`每页 ${val} 条`)
    },
    pageleft () {
      console.log('上一页')
      if (this.pages.page !== 0) {
        this.pages.page = this.pages.page - 1
        this.labellistapi()
      }
    },
    pageright () {
      console.log('下一页')
      this.pages.page = this.pages.page + 1
      this.labellistapi()
    }
  }
}
</script>
<style scoped lang='less'>
// 限制主框大小的内边距
.app-container {
  padding: 10px;
}
// 搜索表单
.searchfrom {
  display: flex;
  position: relative;
  .newlabel {
    position: absolute;
    right: 0px;
  }
}
// 数据统计
.statistics {
  width: 100%;
  height: 35px;
  font-size: 14px;
  color: #909399;
  border-radius: 5px;
  margin-bottom: 15px;
  padding: 8px 0px 10px 15px;
  background-color: #f4f4f5;
  i {
    font-size: 16px;
    margin-right: 9px;
  }
}
.pagination {
  display: flex;
  margin-top: 20px;
  justify-content: flex-end;
}
</style>
