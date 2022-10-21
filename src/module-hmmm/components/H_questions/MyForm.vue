<template>
  <div>
    <el-form class="form" ref="formData" :model="formData" label-width="80px">
      <el-row>
        <el-col style="height:51px;" v-for="item in formlist" :key="item.label" :span="6">
          <el-form-item v-if="item.children" :label="item.label">
                <el-select
                  v-model="formData[(item.module)]"
                  placeholder="请选择"
                >
                <el-option
                  v-for="value in item.children"
                  :key="value.lable"
                  :label="value.label || value"
                  :value="value.value || value"
                >
                </el-option>
            </el-select>
          </el-form-item>
          <el-form-item v-else :label="item.label">
                <el-input :placeholder="item.placeholder" v-model="formData[item.module]"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6" style="height:51px;">
          <el-form-item label="城市">
                <el-row>
                  <el-col :span="11">
                    <el-select
                    v-model="formData.province"
                    placeholder="请选择"
                    >
                    <el-option
                    v-for="item in provinces"
                    :key="item"
                    :label="item"
                    :value="item"
                    ></el-option>
                    </el-select>
                  </el-col>
                  <el-col :offset="1" :span="12">
                    <el-select
                    v-model="formData.city"
                    placeholder="请选择"
                    >
                    <el-option
                    v-for="item in city"
                    :key="item"
                    :label="item"
                    :value="item"
                    ></el-option>
                    </el-select>
                  </el-col>
                </el-row>
          </el-form-item>
        </el-col>
        <el-col :span="6" style="height:51px;">
          <div style="float:right;">
              <el-button size="small" @click="del">清除</el-button>
              <el-button size="small" type="primary" @click="$emit('search',formData)">搜索</el-button>
          </div>
        </el-col>
      </el-row>
    </el-form>
  </div>
</template>

<script>
import * as data from '@/api/hmmm/constants'
import { provinces, citys } from '@/api/hmmm/citys'
import { simple as subjectssimple } from '@/api/hmmm/subjects'
import { simple as tagssimple } from '@/api/hmmm/tags'
import { simple as userssimple } from '@/api/base/users'
import { simple } from '@/api/hmmm/directorys'

export default {
  data () {
    return {
      formData: {
        'subjectID': '',
        'catalogID': '',
        'tags': '',
        'keyword': '',
        'questionType': '',
        'difficulty': '',
        'direction': '',
        'creatorID': '',
        'remarks': '',
        'shortName': '',
        province: '',
        city: ''
      },
      city: [],
      provinces: provinces(),
      formlist: [
        { label: '学科', module: 'subjectID', children: [] },
        { label: '二级目录', module: 'catalogID', children: [] },
        { label: '标签', module: 'tags', children: [] },
        { label: '关键字', module: 'keyword', placeholder: '根据题干搜索' },
        {
          label: '试题类型',
          module: 'questionType',
          children: data.questionType
        },
        { label: '难度', module: 'difficulty', children: data.difficulty },
        { label: '方向', module: 'direction', children: data.direction },
        { label: '录入人', module: 'creatorID', children: [] },
        { label: '题目备注', module: 'remarks' },
        { label: '企业简称', module: 'shortName' }
      ]
    }
  },
  methods: {
    async subject () {
      const { data } = await subjectssimple()
      this.formlist[0].children = data
    },
    async users () {
      const { data } = await userssimple()
      this.formlist[7].children = data.map((item) => {
        return { value: item.id, label: item.username }
      })
    },
    async catalogID () {
      this.formData['catalogID'] = ''
      const query = {
        page: 1,
        pagesize: 10000,
        subjectID: this.formData['subjectID']
      }
      const { data } = await simple(query)
      this.formlist[1].children = data
    },
    async tags () {
      const query = {
        page: 1,
        pagesize: 10000,
        subjectID: this.formData['subjectID']
      }
      this.formData['tags'] = ''
      const { data } = await tagssimple(query)
      console.log(data)
      this.formlist[2].children = data
    },
    citylist () {
      this.formData.city = ''
      this.city = citys(this.formData.province)
    },
    del () {
      this.formData = {
        'subjectID': '',
        'catalogID': '',
        'tags': '',
        'keyword': '',
        'questionType': '',
        'difficulty': '',
        'direction': '',
        'creatorID': '',
        'remarks': '',
        'shortName': '',
        province: '',
        city: ''
      }
    }
  },
  watch: {
    formcatalogID: 'catalogID',
    tagsId: 'tags',
    citys: 'citylist'
  },
  computed: {
    formcatalogID () {
      return this.formData['subjectID']
    },
    tagsId () {
      return this.formData['subjectID']
    },
    citys () {
      return this.formData.province
    }
  },
  created () {
    this.subject()
    this.users()
  }
}
</script>

<style lang="less">
.form{
    .el-select{
        width: 100%;
    }
    .el-input__inner{
          line-height: 32px;
          height: 32px;
        }
}

</style>
