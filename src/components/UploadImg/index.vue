<template>
  <div class="upload-container">
    <!-- action: 远程服务器接口上传地址 -->
    <el-upload
      v-loading="loading"
      class="uploadImg"
      action="#"
      list-type="picture-card"
      :file-list="fileList"
      :on-remove="onRemove"
      :on-change="onChange"
      :before-upload="boforeUpload"
      :http-request="onHttpRequest"
      :limit="1"
    >上传图片
      <i class="el-icon-circle-close" />
    </el-upload>

  </div>

</template>

<script>
export default {
  name: 'UploadImg',
  components: {},
  props: {},
  data () {
    return {
      fileList: [
        // { name: 'default', url: 'http://destiny001.gitee.io/image/cxk.gif' }
        // { name: 'default', url: 'https://hz-33-1314348602.cos.ap-shanghai.myqcloud.com/smile.png' }
      ],
      loading: false
    }
  },
  computed: {},
  watch: {},
  // 生命周期 - 创建完成（访问当前this实例）
  created () {},
  methods: {

    onRemove (file, fileList) {
      console.log(fileList)
      this.fileList = fileList
    },
    // 1. 本地选择上传，原来只有一个数据 + 选择的数据
    // 2. 请求上传 成功/失败 原来就有一个数据
    onChange (file, fileList) {
      console.log(fileList)
      this.fileList = fileList
    },
    boforeUpload (file) {
      //   限制上传文件格式
      const allowTypes = ['image/png', 'image/jpeg', 'image/gif']
      const ishas = allowTypes.includes(file.type)
      if (!ishas) {
        this.$message.error('亲，只能上传' + allowTypes.join('、') + '类型文件')
        return false
      }
      //   限制上传文件大小
      const maxSize = 5 * 1024 * 1024
      if (file.size > maxSize) {
        this.$message.error('上传的图片不能大于1mb')
        return false
      }
    },
    onHttpRequest ({ file }) {
      console.log(file)
    }
  }
}
</script>
<style lang="scss" >
.upload-container{
  position: relative;
  margin-left: 4px;

  .uploadImg {
  box-sizing: border-box;
    vertical-align: middle;
    width: 102px;
    height: 62px;
    overflow: hidden;
    .el-upload-list__item{
      width: 102px;
    height: 62px;
    line-height: 62px;
  }

  }
  .uploadImg .el-upload {
    width: 102px;
    height: 62px;
    line-height: 62px;
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    // position: relative;
    // overflow: hidden;
  }

  .uploadImg .el-upload:hover {
    border-color: #409eff;
  }

  .uploadImg i {
    position: absolute;
    right: 0;
    top: 0;
    -webkit-transform: translate(50%, -50%);
    transform: translate(50%, -50%);
    background: #fff;
    font-size: 18px;
    color: #999;
  }
}

</style>
