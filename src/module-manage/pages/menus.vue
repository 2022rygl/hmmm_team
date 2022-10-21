<template>
  <div class="container">
    <div class="dashboard-container">
      <div class="app-container">
        <el-card class="box-card">
          <div class="searchfrom">
            <!-- 新增标签按钮 -->
            <el-button
              type="success"
              icon="el-icon-edit"
              class="newlabel"
              size="small"
              @click="dialogVisible = true"
              >添加菜单</el-button
            >
          </div>
          <!-- 内容 -->
          <TreeTable
            :columns="columns"
            :data="menulist"
            @handleUpdate="handleUpdate($event)"
            @handleDelete="handleDelete($event)"
          ></TreeTable>
        </el-card>
      </div>
    </div>
    <!-- 弹出框 -->
    <el-dialog title="创建菜单" :visible.sync="dialogVisible" width="50%">
      <span>
        <el-form
          :model="ruleForm"
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="权限组名称" prop="pid">
            <el-radio-group v-model="ruleForm.pid">
              <el-radio label="0"></el-radio>
              <el-radio label="1"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="权限组名称" prop="is_point">
            <el-select v-model="ruleForm.region" placeholder="请选择活动区域">
              <el-option
                v-for="item in menulist"
                :key="item.id"
                :label="item.title"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="权限代码" prop="code">
            <el-input v-model="ruleForm.code"></el-input>
          </el-form-item>
          <el-form-item label="权限标题" prop="title">
            <el-input v-model="ruleForm.title"></el-input>
          </el-form-item>
        </el-form>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false"> 取 消 </el-button>
        <el-button type="primary" @click="addmenu"> 确 定 </el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
//  update,datail
import { list, add, remove, update } from '@/api/base/menus.js'
import TreeTable from '../../components/TreeTable/'
export default {
  components: {
    TreeTable
  },
  data () {
    return {
      menulist: [], // 数据列表
      dialogVisible: false, // 弹出框,
      columns: [ // 定义表头
        { text: '标题', prop: 'title', value: 'title', width: 220 },
        { text: '权限点代码', prop: 'code', value: 'code' }
      ],
      ruleForm: {
        code: '',
        id: '',
        is_point: false,
        pid: '',
        title: ''
      },
      rules: {
        code: [
          { required: true, message: '代码不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.menulistapi()
  },

  methods: {
    // 菜单列表
    async menulistapi () {
      const data = await list()
      this.menulist = data.data
      // console.log(this.menulist)
    },
    // 添加菜单
    async addapi () {
      const data = await add(this.ruleForm)
      console.log(data)
      this.ruleForm.code = ''
      this.ruleForm.id = ''
      this.ruleForm.pid = ''
      this.ruleForm.title = ''
      this.menulistapi()
    },
    // 删除菜单
    async removeapi () {
      const data = await remove(this.ruleForm)
      console.log(data)
      this.menulistapi()
    },
    // 修改菜单
    async updateapi () {
      const data = await update(this.ruleForm)
      console.log(data)
    },
    deleteRow (index, rows) {
      rows.splice(index, 1)
    },
    load (tree, treeNode, resolve) {
      setTimeout(() => {
        resolve([
        ])
      }, 1000)
    },
    // 添加/更改事件
    addmenu () {
      if (this.ruleForm.id === '') {
        this.addapi()
        this.dialogVisible = false
        // console.log('添加')
      } else {
        this.updateapi()
        this.dialogVisible = false
        // console.log('更改')
      }
    },
    handleUpdate (row) {
      console.log(row)
      this.ruleForm.code = row.code
      this.ruleForm.id = row.id
      this.ruleForm.pid = row.pid
      this.ruleForm.title = row.title
      this.dialogVisible = true
    },
    handleDelete (user) {
      this.ruleForm.id = user
      this.removeapi()
      // console.log(user)
    }
  }
}
</script>

<style scoped lang='less'>
.searchfrom {
  display: flex;
  justify-content: flex-end;
}
</style>
