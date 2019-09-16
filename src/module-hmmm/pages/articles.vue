<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button class="btn" @click="addBtn">新增面试技巧</el-button>
      <el-row :gutter="10" class="keys">
      <el-col :span="10">
        关键字：
        <el-input
          type="text"
          placeholder="请输入关键字"
          v-model="searchForm.keyword"
          style="width:190px;height:50px;"
        ></el-input>
      </el-col>
      <el-col :span="10" class="search">
        <el-button size="mini">清除</el-button>
        <el-button type="primary" size="mini" @click="getQuestionList()">搜索</el-button>
      </el-col>
    </el-row>
    <el-table :data="questionList" style="width:100%">
      <el-table-column label="序号" type="index"></el-table-column>
      <el-table-column label="试题编号" prop="number"></el-table-column>
      <el-table-column label="学科" prop="subject"></el-table-column>
      <el-table-column label="题型" :formatter="questionTypeFMT" prop="questionType"></el-table-column>
      <el-table-column label="题干" prop="question"></el-table-column>

      <el-table-column label="录入时间" width="170">
        <!--table表格column中获取数据信息，要使用 作用域插槽 形式-->
        <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
      </el-table-column>

      <el-table-column label="难度" prop="difficulty" :formatter="difficultyFMT"></el-table-column>
      <el-table-column label="录入人" prop="creator"></el-table-column>
      <el-table-column label="操作" width="200">
        <template slot-scope="stData">
          <a href="#">预览</a>
          <a href="#">修改</a>
          <!--prevent:阻止标签的默认动作-->
          <a href="#" @click.prevent="del(stData.row)">删除</a>
          <a href="#">加入精选</a>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination class="pagination" background layout="prev, pager, next" :total="90"></el-pagination>
    </div>
  </div>
</template>

<script>
import { simple as subjectSimple } from '@/api/hmmm/subjects'
import { simple as tagsSimple } from '@/api/hmmm/tags' // 获取标签信息方法导入
import { simple as usersSimple } from '@/api/base/users' // 获取录入人信息方法导入
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 获取二级目录信息方法导入
import { provinces, citys } from '@/api/hmmm/citys' // 获取 省份/城市 信息方法导入
import { list, remove } from '@/api/hmmm/questions' // 基础题库相关api导入
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants'
export default {
  name: 'QuestionsList',
  // 生命周期方法
  created() {
    // 获得学科信息
    this.getSubjectIDList()
    // 获得标签信息
    this.getTagsList()
    // 获得录入人信息
    this.getCreatorIDList()
    // 获得 二级目录 信息
    this.getCatalogIDList()
    // 获得 基础题库 列表信息
    this.getQuestionList()
  },
  methods: {
    addBtn() {
      this.$router.push('/articles/add')
    },
    // 题型数字转汉字
    questionTypeFMT(row, column, cellValue) {
      // cellValue就代表需要转换的目标信息
      // cellValue=2 则返回'多选'两字
      // console.log(cellValue)
      return this.questionTypeList[cellValue - 1]['label']
    },
    // 难度数字转汉字
    difficultyFMT(row, column, cellValue) {
      return this.difficultyList[cellValue - 1]['label']
    },

    // 获得学科列表数据
    async getSubjectIDList() {
      var rst = await subjectSimple()
      // 把获得好的学科信息赋予给subjectIDList成员
      this.subjectIDList = rst.data
    },
    // 获得 标签 列表数据
    async getTagsList() {
      var rst = await tagsSimple()
      this.tagsList = rst.data
    },
    // 获得 录入人 列表数据
    async getCreatorIDList() {
      var rst = await usersSimple()
      this.creatorIDList = rst.data
    },
    // 获得 录入人 列表数据
    async getCatalogIDList() {
      var rst = await directorysSimple()
      this.catalogIDList = rst.data
    },
    // 获得 省份 信息，简易成员赋值
    provinces, // provinces:provinces
    // 获得 城市 信息
    // getCitys方法在模板中声明的时候，没有设置()
    // 这个pname形参就代表被选中的省份信息
    getCitys(pname) {
      this.searchForm.city = '' // 清除之前选取好的城市
      this.cityList = citys(pname)
    },
    // 获得基础题库列表信息
    async getQuestionList() {
      var rst = await list(this.searchForm)
      // 把获得好的题库列表信息赋予给 questionList 成员
      this.questionList = rst.data.items
    },
    // 删除题库
    del(info) {
      // 确认框
      this.$confirm('确认要删除该题库么?', '删除', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          remove(info) // 删除数据
          this.getQuestionList() // 更新数据
        })
        .catch(() => {})
    }
  },
  data() {
    return {
      // 基础搜索数据列表部分
      subjectIDList: [],
      difficultyList,
      questionTypeList,
      tagsList: [], // 标签
      creatorIDList: [], // 录入人
      catalogIDList: [], // 二级目录
      directionList, // 方向(简易成员复制)
      cityList: [], // 城市信息
      questionList: [], // 基础题库列表信息

      searchForm: {
        keyword: '' // 关键字
      }
    }
  }
}
</script>

<style scoped>
.btn {
  margin-bottom: 10px;
}
.key {
  position: absolute;
  margin-bottom: 10px;
}
.search {
  margin-bottom: 10px;
  display: block;
  position: relative;
  left: 600px;
}
.pagination {
  margin-top: 10px;
  text-align: center;
}
</style>
