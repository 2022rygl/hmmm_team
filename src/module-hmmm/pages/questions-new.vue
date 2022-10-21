<template>
  <div class="app-container questions-new">
    <el-card shadow="always">
      <div slot="header">
        <span>试题录入</span>
      </div>
      <el-form
        ref="addQuestionsForm"
        :model="newFormData"
        :rules="rules"
        label-width="120px"
        size="medium"
      >
        <el-form-item label="学科：" prop="subjectID">
          <el-select
            v-model="newFormData.subjectID"
            placeholder="请选择"
            size="medium"
            autocomplete
            @change="onSubjectChange"
          >
            <el-option
              v-for="item in subjectOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="目录：" prop="catalogID">
          <el-select
            v-model="newFormData.catalogID"
            placeholder="请选择"
            size="medium"
            autocomplete
          >
            <el-option
              v-for="item in catalogOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="企业：" prop="enterpriseID">
          <el-select
            v-model="newFormData.enterpriseID"
            placeholder="请选择"
            size="medium"
            autocomplete
          >
            <el-option
              v-for="item in companyOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="城市：" prop="province" ref="province">
          <el-select
            class="province"
            v-model="newFormData.province"
            placeholder="请选择"
            size="medium"
            autocomplete
            @change="onProvinceChange"
          >
            <el-option
              v-for="(item, index) in provinceOptions"
              :key="index"
              :label="item"
              :value="item"
            >
            </el-option>
          </el-select>
          <el-select
            class="city"
            v-model="newFormData.city"
            placeholder="请选择"
            size="medium"
            autocomplete
            @change="onCityChange"
          >
            <el-option
              v-for="(item, index) in cityOptions"
              :key="index"
              :label="item"
              :value="item"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="方向：" prop="direction">
          <el-select
            v-model="newFormData.direction"
            placeholder="请选择"
            size="medium"
            autocomplete
          >
            <el-option
              v-for="item in direction"
              :key="item"
              :label="item"
              :value="item"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="题型：" prop="questionType">
          <el-radio
            v-model="newFormData.questionType"
            v-for="item in questionTypeLabel"
            :key="item.value"
            :label="(item.value).toString()"
            >{{ item.label }}</el-radio
          >
        </el-form-item>

        <el-form-item label="难度：" prop="difficulty">
          <el-radio
            v-model="newFormData.difficulty"
            v-for="item in difficulty"
            :key="item.value"
            :label="(item.value).toString()"
            >{{ item.label }}</el-radio
          >
        </el-form-item>

        <el-form-item label="题干：" class="editor" prop="question">
          <quill-editor
            v-model="newFormData.question"
            ref="questionEditor"
            :options="editorOption"
          >
          </quill-editor>
        </el-form-item>
        <!--!选项 ------------------>
        <el-form-item
          label="选项："
          prop="options"
          v-if="newFormData.questionType !== '3'"
        >
          <el-row
            class="option_item"
            type="flex"
            align="middle"
            v-for="item in checkOptions"
            :key="item.code"
          >
            <div class="radio" v-if="newFormData.questionType === '1'">
              <el-radio-group v-model="radio">
                <el-radio class="optionRadio" :label="item.code"
                  >{{ item.code }}：</el-radio
                >
              </el-radio-group>
            </div>
            <div class="checkbox" v-if="newFormData.questionType === '2'">
              <el-checkbox-group v-model="checkList">
                <el-checkbox class="optionCheckbox" :label="item.code"
                  >{{ item.code }}：</el-checkbox
                >
              </el-checkbox-group>
            </div>
            <el-input class="optionIpt" v-model="item.title" type="text" />
            <UploadImg />
          </el-row>

          <el-button
            type="danger"
            size="small"
            :disabled="newFormData.questionType !== '2'"
            @click="addOptions"
            >+增加选项与答案</el-button
          >
        </el-form-item>

        <el-form-item label="解析视频：" prop="videoURL">
          <el-input
            class="videoIpt"
            type="text"
            v-model="newFormData.videoURL"
            size="medium"
            autocomplete
          />
        </el-form-item>

        <el-form-item label="答案解析：" class="editor" prop="answer">
          <quill-editor
            v-model="newFormData.answer"
            ref="answerEditor"
            :options="editorOption"
          >
          </quill-editor>
        </el-form-item>
        <el-form-item label="题目备注：" prop="remarks">
          <el-input
            class="remarksIpt"
            :rows="4"
            type="textarea"
            v-model="newFormData.remarks"
            size="medium"
            autocomplete
          />
        </el-form-item>

        <el-form-item label="试题标签：" prop="tags">
          <el-select
            v-model="newFormData.tags"
            placeholder="请选择试题标签"
            size="medium"
            autocomplete
            clearable
            multiple
          >
            <el-option
              v-for="item in tagOptions"
              :key="item.label"
              :label="item.label"
              :value="item.label"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="onSubmit">确认提交</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { direction, difficulty, questionType } from '@/api/hmmm/constants.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { list } from '@/api/hmmm/companys.js'
import { simple as tagSimple } from '@/api/hmmm/tags.js'
import { simple as catalogSimple } from '@/api/hmmm/directorys.js'
import { simple as subjectSimple } from '@/api/hmmm/subjects.js'
import { add } from '@/api/hmmm/questions.js'
import UploadImg from '../../components/UploadImg/index.vue'
const toolbarOptions = [
  // 加粗 斜体 下划线 删除线 -----['bold', 'italic', 'underline', 'strike']
  ['bold', 'italic', 'underline', 'strike'],
  // 引用  代码块-----['blockquote', 'code-block']
  ['blockquote', 'code-block'],
  // 1、2 级标题-----[{ header: 1 }, { header: 2 }]
  [{ header: 1 }, { header: 2 }],
  // 有序、无序列表-----[{ list: 'ordered' }, { list: 'bullet' }]
  [{ list: 'ordered' }, { list: 'bullet' }],
  // 上标/下标-----[{ script: 'sub' }, { script: 'super' }]
  [{ script: 'sub' }, { script: 'super' }],
  // 缩进-----[{ indent: '-1' }, { indent: '+1' }]
  [{ indent: '-1' }, { indent: '+1' }],
  // 文本方向-----[{'direction': 'rtl'}]
  [{ direction: 'rtl' }],
  // 字体大小-----[{ size: ['small', false, 'large', 'huge'] }]
  [{ size: ['small', false, 'large', 'huge'] }],
  // 标题-----[{ header: [1, 2, 3, 4, 5, 6, false] }]
  [{ header: [1, 2, 3, 4, 5, 6, false] }],
  // 字体颜色、字体背景颜色-----[{ color: [] }, { background: [] }]
  [{ color: [] }, { background: [] }],
  // 字体种类-----[{ font: [] }]
  [{ font: [] }],
  // 对齐方式-----[{ align: [] }]
  [{ align: [] }],
  // 清除文本格式-----['clean']
  ['clean'],
  // 链接、图片、视频-----['link', 'image', 'video']
  ['image', 'video']
]
export default {
  name: 'QuestionsNew',
  components: { UploadImg },
  props: {},
  data () {
    const provinceValid = (rules, value, callback) => {
      value && this.newFormData.city === ''
        ? callback(new Error('请选择地区'))
        : callback()
    }
    return {
      direction: direction,
      difficulty: difficulty,
      questionTypeLabel: questionType,
      options: [],
      subjectOptions: [],
      companyOptions: [],
      catalogOptions: [],
      provinceOptions: provinces(),
      cityOptions: [],
      tagOptions: [],
      newFormData: {
        subjectID: '',
        catalogID: '',
        enterpriseID: '', // 企业
        province: '',
        city: '',
        direction: '',
        questionType: '1',
        difficulty: '1',
        question: '',
        options: [],
        videoURL: '',
        answer: '',
        remarks: '',
        tags: null
      },
      rules: {
        subjectID: [{ required: true, message: '请选择学科' }],
        catalogID: [{ required: true, message: '请选择目录' }],
        enterpriseID: [{ required: true, message: '请选择企业' }],
        province: [
          { required: true, message: '请选择地区' },
          { validator: provinceValid }
        ],
        direction: [{ required: true, message: '请选择方向' }],
        questionType: [{ required: true }],
        difficulty: [{ required: true }],
        question: [{ required: true, message: '请输入题干' }],
        answer: [{ required: true, message: '请输入题干' }]
      },
      editorOption: {
        modules: {
          toolbar: toolbarOptions
        },
        theme: 'snow',
        placeholder: '请输入正文'
      },
      checkOptions: [
        { code: 'A', isRight: false, title: '', img: '' },
        { code: 'B', isRight: false, title: '', img: '' },
        { code: 'C', isRight: false, title: '', img: '' },
        { code: 'D', isRight: false, title: '', img: '' }
      ],
      radio: '',
      imageUrl: '',
      checkList: [],
      codeNumber: 69
    }
  },
  watch: {
    radio: {
      immediate: true,
      handler (newVal) {
        // console.log(newVal)
        this.newFormData.options = this.checkOptions.reduce((pre, item) => {
          item.isRight = item.code === newVal
          pre.push(item)
          return pre
        }, [])
        // this.radioWatcher(newVal)
      }
    },
    checkList: {
      immediate: true,
      deep: true,
      handler (newVal) {
        // console.log(newVal)
        this.checkOptions.forEach((item) => {
          const res = newVal.find((val) => val === item.code)
          if (res) item.isRight = true
        })
        this.newFormData.options = this.checkOptions
      }
    },
    questionType: {
      // immediate: true,
      handler () {
        this.radio = ''
        this.checkList = []
        this.checkOptions.forEach((item) => { item.isRight = false })
        // console.log('questionType变化了')
      }
    }
  },
  computed: {
    questionType () {
      return this.newFormData.questionType
    }
  },
  methods: {
    async getAllSubject () {
      const { data } = await subjectSimple()
      this.subjectOptions = data
    },
    async getAllCompany () {
      const {
        data: { items }
      } = await list({ pagesize: 10000 })
      this.companyOptions = items.reduce((arr, item) => {
        arr.push({ value: item.id, label: item.shortName })
        return arr
      }, [])
    },
    async onSubjectChange (val) {
      // console.log(val)
      const { data: catalogData } = await catalogSimple(val)
      this.catalogOptions = catalogData

      const { data: tagData } = await tagSimple(val)
      this.tagOptions = tagData
    },
    onProvinceChange (val) {
      this.cityOptions = citys(val)
    },
    onCityChange (val) {
      this.newFormData.city = val
    },
    // 增加选项与答案
    addOptions () {
      const addCode = String.fromCharCode(this.codeNumber)
      this.codeNumber++
      this.checkOptions.push({
        code: addCode,
        isRight: false,
        title: '',
        img: ''
      })
    },
    async onSubmit () {
      try {
        await this.$refs.addQuestionsForm.validate()
        const res = await add(this.newFormData)
        console.log(res)
        this.$message.success('添加成功')
      } catch (error) {
        console.log(error)
      }
    },
    // 上传图片
    handleAvatarSuccess (res, file) {
      this.imageUrl = URL.createObjectURL(file.raw)
    },
    beforeAvatarUpload (file) {
      const isJPG = file.type === 'image/jpeg'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    }
  },

  // 生命周期 - 创建完成（访问当前this实例）
  created () {
    this.getAllSubject()
    this.getAllCompany()
  }
}
</script>
<style lang="scss"  >
.questions-new {
  .el-form-item.editor {
    margin-bottom: 60px;
    .el-form-item__content {
      height: 290px;
      .quill-editor {
        height: 200px;
        width: 100%;
      }
      .ql-container.ql-snow {
        height: 100%;
        width: 100%;
      }
    }
  }
  .el-form-item {
    margin-bottom: 22px;
  }
  .el-select {
    width: 400px;
  }
  .el-select.province {
    width: 198px;
  }
  .el-select.city {
    width: 198px;
    margin-left: 4px;
  }
  .el-input.videoIpt,
  .el-textarea.remarksIpt {
    width: 400px;
  }
  .option_item {
    padding-bottom: 20px;
  }
  .el-input.optionIpt {
    width: 240px;
  }
  .el-radio.optionRadio {
    margin-right: 0;
  }
}
</style>
