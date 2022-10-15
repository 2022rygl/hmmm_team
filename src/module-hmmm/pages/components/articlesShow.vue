<template>
  <div>
    <el-dialog
      title="预览文章"
      :visible="articlesShow"
      width="60%"
      @close="btnCancel"
    >
      <div class="hander">
        <h2>{{ list.title }}</h2>
        <div class="main">
          <span>{{ list.createTime }}</span>
          <span>{{ list.username }}</span>
          <i class="el-icon-view"></i>
          <span>{{ list.visits }}</span>
        </div>
        <el-alert
          type="info"
          :closable="false"
          style="border-top: dashed 2px #ccc"
        >
          <div v-html="list.articleBody"></div
        >
      </el-alert>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  props: {
    articlesShow: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      list: {}
    }
  },
  methods: {
    btnCancel () {
      console.log(1)
      this.$emit('update:articlesShow', false)
    }
  }
}
</script>

<style scoped lang="scss" >
.hander {
  margin-top: -20px;
}
.main {
  span {
    margin-left: 20px;
  }
  i {
    margin-left: 10px;
    margin-right: -10px;
  }
  margin-bottom: 10px;
}
</style>>
