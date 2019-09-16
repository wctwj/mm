<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form ref="addFormRef" :v-model="addForm">
        <el-form-item label="学科：">
          <el-select v-model="addForm.subjectID">
            <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录：">
          <el-select v-model="addForm.catalogID">
            <el-option
              v-for="item in catalogIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="企业：">
          <el-select v-model="addForm.enterpriseID">
            <el-option
              v-for="item in enterpriseIDList"
              :key="item.id"
              :label="item.company"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市：">
          <el-select v-model="addForm.province" @change="getCitys">
            <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
          </el-select>
          <el-select v-model="addForm.city">
            <el-option v-for="item in cityList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>

          <el-form-item label="方向：">
          <el-select v-model="addForm.direction">
            <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
          </el-form-item>

           <el-form-item label="题型：">
          <el-radio-group v-model="addForm.questionType" @change="controlShow">
            <el-radio v-for="item in questionTypeList" :key="item.value" :label="item.value+''">{{item.label}}</el-radio>
          </el-radio-group>
          </el-form-item>

          <el-form-item label="难度：">
            <el-radio-group v-model="addForm.difficulty">
              <el-radio v-for="item in difficultyList" :key="item.value" :label="item.value+''">{{item.lable}}</el-radio>
            </el-radio-group>
          </el-form-item>
     <el-form-item label="题干：">
          <el-input type="textarea" v-model="addForm.question"></el-input>
        </el-form-item>

        <el-form-item label="选项：" v-if="allShow">
          <template v-if="radioShow">
            <el-radio v-model="singleSelect" :label="0">
              A: <el-input type="text" v-model="addForm.options[0]['title']"></el-input>
            </el-radio><br />
            <el-radio v-model="singleSelect" :label="1">
              B: <el-input type="text" v-model="addForm.options[1]['title']"></el-input>
            </el-radio><br />
            <el-radio v-model="singleSelect" :label="2">
              C: <el-input type="text" v-model="addForm.options[2]['title']"></el-input>
            </el-radio><br />
            <el-radio v-model="singleSelect" :label="3">
              D: <el-input type="text" v-model="addForm.options[3]['title']"></el-input>
            </el-radio>
          </template>
          <template v-else>
            <el-checkbox v-model="addForm.options[0]['isRight']"> 
              A: <el-input type="text" v-model="addForm.options[0]['title']"></el-input>
            </el-checkbox><br />
            <el-checkbox v-model="addForm.options[1]['isRight']"> 
              B: <el-input type="text" v-model="addForm.options[1]['title']"></el-input>
            </el-checkbox><br />
            <el-checkbox v-model="addForm.options[2]['isRight']"> 
              C: <el-input type="text" v-model="addForm.options[2]['title']"></el-input>
            </el-checkbox><br />
            <el-checkbox v-model="addForm.options[3]['isRight']"> 
              D: <el-input type="text" v-model="addForm.options[3]['title']"></el-input>
            </el-checkbox>
          </template>

        </el-form-item>

        <el-form-item label="答案：">
          <el-input type="textarea" v-model="addForm.answer"></el-input>
        </el-form-item>
        <el-form-item label="备注：">
          <el-input type="textarea" v-model="addForm.remarks"></el-input>
        </el-form-item>
        <el-form-item label="标签：">
          <el-input type="text" v-model="addForm.tags"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="addQuestion()">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { simple as subjectsSimple } from '@/api/hmmm/subjects'
import { simple as directorysSimple } from '@/api/hmmm/directorys'
import { list as companysList } from '@/api/hmmm/companys'
import { provinces, citys } from '@/api/hmmm/citys'
import { add } from '@/api/hmmm/questions'
import {
   direction as directionList,
   questionType as questionTypeList,
   difficulty as difficultyList
} from '@/api/hmmm/constants'

export default {
  name: 'QuestionsNew',
  created() {
    this.getSubjectIDList() // 获得学科
    this.getCatalogIDList() // 获得目录
    this.getEnterpriseIDList() // 获得企业
  },
  methods: {
    // 根据选中的题型，判断单选、多选表单域组是否显示
    controlShow(type) {
      if (type === '1') {
        // 单选
        this.allShow = true
        this.radioShow = true
      } else if (type === '2') {
        // 多选
        this.allShow = true
        this.radioShow = false
      } else {
        // 简单(没有单选或多选了)
        this.allShow = false
      }
    },
    // 收集表单数据，完成添加操作
    async addQuestion() {
      // 注意：要使得数据完成添加后，再做列表页面重定向操作
      await add(this.addForm) // 添加数据
      this.$message.success('添加数据成功')
      this.$router.push('/questions/list')// 路由重定向跳转
    },
    // 获得学科列表信息
    async getSubjectIDList() {
      var rst = await subjectsSimple()
      this.subjectIDList = rst.data
    },
    // 获得目录列表信息
    async getCatalogIDList() {
      var rst = await directorysSimple()
      this.catalogIDList = rst.data
    },
    // 获得企业列表信息
    async getEnterpriseIDList() {
      var rst = await companysList()
      this.enterpriseIDList = rst.data.items
    },
    // 简易成员方式设置provinces的方法
    provinces,
    // 根据省份获得城市信息
    getCitys(pname) {
      this.addForm.city = '' // 清除旧城市
      this.cityList = citys(pname)
    }
  },
  // watch监听器
  watch: {
    // 监听选项选中要把isRight的值做设置操作
    singleSelect(newval) {
      // 要使得全部的isRight变为false
      // newval代表的当前项目的isRight变为true
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newval].isRight = true
    }
  },
  data() {
    return {
      radioShow: true,
      allShow: true,
      subjectIDList: [],
      catalogIDList: [],
      enterpriseIDList: [],
      cityList: [],
      directionList,
      questionTypeList,
      difficultyList,
      singleSelect: '',

      addForm: {
        // 表单数据
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        city: '',
        province: '',
        direction: '',
        questionType: '',
        difficulty: '',
        question: '',
        answer: '',
        remarks: '',
        tags: '',
        videoURL: 'http://127.0.0.1/ok.mp4',
        options: [
          { code: 'A', title: '', img: '', isRight: true },
          { code: 'B', title: '', img: '', isRight: true },
          { code: 'C', title: '', img: '', isRight: true },
          { code: 'D', title: '', img: '', isRight: true }
        ]
      }
    }
  }
}
</script>

<style scoped>
</style>
