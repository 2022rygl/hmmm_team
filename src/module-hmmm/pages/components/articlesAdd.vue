<template>
  <el-dialog title="新增文章" :visible="articlesAdd" @close="btnCancel">
    <el-form ref="addform" label-width="80px" :model="addform" :rules="rules">
      <el-form-item label="文章标题:" prop="title">
        <el-input v-model="addform.title" />
      </el-form-item>
      <el-form-item label="文章内容:" prop="articleBody">
        <quill-editor
          v-model="addform.articleBody"
          ref="myQuillEditor"
          :options="editorOption"
          @blur="onEditorBlur($event)"
          @focus="onEditorFocus($event)"
          @ready="onEditorReady($event)"
        >
        </quill-editor>
      </el-form-item>
      <el-form-item label="视频地址:" prop="videoURL">
        <el-input v-model="addform.videoURL" />
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="btnCancel">取 消</el-button>
      <el-button type="primary" @click="btnOk">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
import { add, update } from '@/api/hmmm/articles.js'
export default {
  name: 'ArticlesAdd',
  props: {
    articlesAdd: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      content: '',
      editorOption: {},
      addform: {
        title: '',
        articleBody: '',
        videoURL: ''
      },
      rules: {
        title: [
          { required: true, message: '请输入文章标题', trigger: 'blur' }
        ],
        articleBody: [
          { required: true, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    btnCancel () {
      this.$emit('update:articlesAdd', false)
      this.addform = {
        title: '',
        articleBody: '',
        videoURL: '',
        id: ''
      }
    },
    onEditorBlur () {
      console.log('blur', this.addform.articleBody)
    },

    onEditorFocus () {
      console.log('focus', this.addform.articleBody)
    },

    onEditorReady () {
      console.log('ready', this.addform.articleBody)
    },
    async btnOk () {
      try {
        await this.$refs.addform.validate()
        if (this.addform.id) {
          await update(this.addform)
          this.$message.success('编辑成功')
          this.$emit('getlist')
        } else {
          await add(this.addform)
          this.$message.success('新增成功')
          this.$emit('getlist')
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
<style lang="scss">
.wrapper {
  width: 100%;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;

  .input-wrapper {
    display: flex;
    flex-direction: row;
    width: 100%;
    margin: px2rem(5) 0;
    box-sizing: border-box;

    .editor {
      width: 100%;
      height: 200px;
    }
  }
}

div.ql-container.ql-snow {
  height: 200px;
}

div.ql-editor.ql-blank {
  height: 200px;
}
.el-dialog {
  .el-form-item__label {
    padding: 0px 6px 0 0;
  }
}
</style>
