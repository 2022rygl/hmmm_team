<template>
  <el-dialog
    title="题目预览"
    :visible.sync="previewDialog"
    width="900px"
    :before-close="handleClose"
  >
    <el-row>
      <el-col :span="6">【题型】：{{ preQuestionType }}</el-col>
      <el-col :span="6">【编号】：{{ previewData.id }}</el-col>
      <el-col :span="6">【难度】：{{ preDifficulty }}</el-col>
      <el-col :span="6">【标签】：{{ previewData.tags }}</el-col>
      <el-col :span="6">【学科】：{{ previewData.subjectName }}</el-col>
      <el-col :span="6">【目录】：{{ previewData.directoryName }}</el-col>
      <el-col :span="6">【方向】：{{ previewData.direction }}</el-col>
      <el-col :span="6"></el-col>
    </el-row>
    <hr />

    <el-row>【题干】：</el-row>

    <div style="color: blue" v-html="previewData.question"></div>

    <div v-if="previewData.questionType === '1'">
      <div style="padding-bottom: 5px">
        单选题 选项：（以下选中的选项为正确答案）
      </div>

      <el-radio-group :value="radio">
        <div
          style="padding: 8px 0px"
          v-for="item in previewData.options"
          :key="item.id"
        >
          <el-radio
            :label="item.isRight"
            >{{ item.title }}</el-radio
          >
        </div>
      </el-radio-group>
    </div>

    <div v-if="previewData.questionType === '2'">
      <div style="padding-bottom: 5px">
        多选题 选项：（以下选中的选项为正确答案）
        <el-checkbox-group :value="checkList">
          <div
            style="padding: 8px 0px"
            v-for="item in previewData.options"
            :key="item.id"
          >
            <el-checkbox
              :label="item.isRight"
            > {{item.title}}</el-checkbox>
          </div>
        </el-checkbox-group>
      </div>
    </div>

    <div v-if="previewData.questionType === '3'" />
    <hr />
    <el-row
      >【参考答案】：<el-button
        type="danger"
        size="small"
        @click="videoShow = !videoShow"
        >视频答案预览</el-button
      ></el-row
    >
    <div class="video" v-show="videoShow">
      <video controls :src="previewData.videoURL" />
    </div>
    <hr />
    【答案解析】：
    <div style="display: inline-block" v-html="previewData.answer"></div>
    <hr />
    <el-row>【题目备注】：{{ previewData.remarks }}</el-row>

    <span slot="footer" class="dialog-footer">
      <el-button type="primary" @click="handleClose">关 闭</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { questionType, difficulty } from '@/api/hmmm/constants.js'

export default {
  name: 'previewDialog',
  components: {},
  props: {
    previewDialog: {
      type: Boolean,
      default: false
    },
    previewData: {
      type: Object,
      default: () => ({})
    }
  },
  data () {
    return {
      radio: 1,
      videoShow: false,
      checkList: [1]
    }
  },
  watch: {},
  computed: {
    preQuestionType () {
      const res = questionType.find(
        (item) => item.value === +this.previewData.questionType
      )
      return res ? res.label : ''
    },
    preDifficulty () {
      const res = difficulty.find(
        (item) => item.value === +this.previewData.difficulty
      )
      return res ? res.label : ''
    }
  },
  methods: {
    handleClose () {
      this.$emit('update:previewDialog', false)
    },
    changeStatus (val) {
      console.log(val)
      // this.checkList.push(val === 1)
      console.log(this.checkList)
    }
  },
  // 生命周期 - 创建完成（访问当前this实例）
  created () {}
}
</script>
<style lang="scss" >
.el-col {
  padding: 10px 0;
}
.el-button--danger {
  color: #fff;
  background-color: #f56c6c;
  border-color: #f56c6c;
}
.video {
  width: 400px;
  height: 300px;
  video {
    width: 100%;
    height: 100%;
  }
}
</style>
