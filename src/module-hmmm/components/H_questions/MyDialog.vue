<template>
  <div>
<el-dialog
  title="题目预览"
  :visible="dialogVisible"
  width="47%"
  @close="handleClose">
  <el-row class="row">
  <el-col :span="6">【题型】：{{data.questionType | questionType}}</el-col>
  <el-col :span="6">【编号】：{{data.id}}</el-col>
  <el-col :span="6">【难度】：{{data.difficulty | difficulty}}</el-col>
  <el-col :span="6">【标签】：{{data.tags}}</el-col>
  <el-col :span="6">【学科】：{{data.subjectName}}</el-col>
  <el-col :span="6">【目录】：{{data.directoryName}}</el-col>
  <el-col :span="6">【方向】：{{data.direction}}</el-col>
</el-row>
<hr />
【题干】：
<div v-html="data.question" style="color: blue;" />
<div v-if="data.questionType  !== '3'">
    <div>{{data.questionType | questionType}} 选项：（以下选中的选项为正确答案）</div>
    <div style="padding: 8px 0px;" v-for="item in data.options" :key="item.id">
        <el-checkbox v-if="data.questionType==='2'" :value="!!item.isRight">{{item.title}}</el-checkbox>
          <el-radio-group v-else :value="1">
              <el-radio :label="item.isRight">{{item.title}}</el-radio>
          </el-radio-group>
    </div>
</div>
<hr>
 【参考答案】：<el-button @click="isShow=true" type="danger" style="font-size:12px;">视频答案预览</el-button>
 <div style="width: 400px;height: 300px;" v-show="isShow">
     <video style="width: 100%;height: 100%;" :src="data.videoURL" controls="controls"></video>
 </div>
 <hr>
 【答案解析】：
 <div style="display: inline-block;" v-html="data.answer"></div>
 <hr>
 【题目备注】：{{data.remarks}}
  <span slot="footer" class="dialog-footer">
    <el-button type="primary" @click="handleClose">关闭</el-button>
  </span>
</el-dialog>

  </div>
</template>

<script>
import { difficulty, questionType } from '../../../api/hmmm/constants'
export default {
  props: {
    dialogVisible: {
      type: Boolean,
      default: false
    },
    data: {
      type: Object,
      default: () => ({})
    }
  },
  data () {
    return {
      isShow: false,
      radio: 1
    }
  },
  methods: {
    handleClose () {
      this.isShow = false
      this.$emit('update:dialogVisible', false)
    }
  },
  filters: {
    questionType (value) {
      questionType.forEach(element => {
        if (+value === element.value) return value = element.label
      })
      return value
    },
    difficulty (value) {
      difficulty.forEach(element => {
        if (+value === element.value) return value = element.label
      })
      return value
    }
  }
}
</script>

<style lang="less">
.row{
    .el-col{
        padding: 10px 0;
    }
}
</style>
