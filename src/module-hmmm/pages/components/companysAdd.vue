<template>
  <el-dialog
    :title="title"
    :visible="companysAdd"
    width="50%"
    @close="btnCancel"
  >
    <el-form
      ref="addform"
      label-width="180px"
      style="width: 100%"
      :model="addform"
      :rules="rules"
    >
      <el-form-item label="企业名称" prop="shortName">
        <el-input style="width: 65%" v-model="addform.shortName" />
      </el-form-item>
      <el-form-item label="所属公司" prop="company">
        <el-input style="width: 65%" v-model="addform.company" />
        <P>https://www.tianyancha.com （在此可查询所属公司全称及简称）</P>
      </el-form-item>
      <el-form-item label="城市区域" prop="province">
        <el-select
          placeholder="请选择"
          style="width: 31%; margin-right: 20px"
          v-model="addform.province"
        >
          <el-option
            v-for="item in provinces()"
            :key="item.id"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
        <el-select
          placeholder="请选择"
          style="width: 31%"
          v-model="addform.city"
        >
          <el-option
            v-for="item in citys(addform.province)"
            :key="item.id"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="方向(企业标签)" prop="tags">
        <el-input style="width: 65%" v-model="addform.tags" />
      </el-form-item>
      <el-form-item label="备注" prop="remarks">
        <el-input
          type="textarea"
          style="width: 65%"
          v-model="addform.remarks"
        />
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="btnCancel">取 消</el-button>
      <el-button type="primary" @click="btnOk">确 定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { provinces, citys } from '@/api/hmmm/citys.js'
import { add, update } from '@/api/hmmm/companys.js'
export default {
  props: {
    companysAdd: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      provinces,
      citys,
      rules: {
        shortName: [
          { required: true, message: '企业简称不能为空', trigger: 'blur' }
        ],
        company: [
          { required: true, message: '所属公司不能为空', trigger: 'blur' }
        ],
        province: [
          { required: true, message: '请选择省份', trigger: 'blur' }
        ],
        tags: [
          { required: true, message: '请请输标签', trigger: 'blur' }
        ],
        remarks: [
          { required: true, message: '备注不能为空', trigger: 'blur' }
        ]
      },
      addform: {
        shortName: '',
        company: '',
        province: '',
        city: '',
        tags: '',
        remarks: '',
        isFamous: true,
        id: '',
        number: '',
        addDate: '',
        creatorID: '',
        state: ''
      }
    }
  },
  created () {
    this.citys()
  },
  computed: {
    title () {
      return this.addform.id ? '编辑用户' : '创建用户'
    }
  },
  methods: {
    btnCancel () {
      this.addform = {
        shortName: '',
        company: '',
        province: '',
        city: '',
        tags: '',
        remarks: '',
        isFamous: true,
        id: '',
        number: '',
        addDate: '',
        creatorID: '',
        state: ''
      }
      this.$emit('update:companysAdd', false)
    },
    async btnOk () {
      try {
        await this.$refs.addform.validate()
        if (this.addform.id) {
          await update(this.addform)
          this.$message.success('修改成功')
          this.$emit('getlist')
        } else {
          const res = await add(this.addform)
          this.$message.success('新增成功')
          this.$refs.addform.resetFields()
          this.$emit('getlist')
          console.log(res)
        }
      } catch (error) {
        console.log(error)
      }
      this.btnCancel()
    }
  }
}
</script>

<style>
</style>
