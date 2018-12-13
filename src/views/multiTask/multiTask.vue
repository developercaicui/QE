<template>
  <div class="multTask" ref="multTask">
    <p @click="setTitle" ref = "setTitle">{{title}}</p>
    <el-button style="float: left;margin-bottom: 10px" @click="visible = true">编辑题目</el-button>
    <el-table :data="tableData"
      ref="table"
      border
      align="left"
      >
     <el-table-column v-for="(item, index) in col"
        :key="`col_${index}`"
        :prop="dropCol[index].prop"
        :label="item.label"> 
          <template slot-scope="scope">
            <!-- 输入框 -->
            <p  v-if ="dropCol[index].id == '1'" placeholder="请输入内容">{{tableData[scope.$index].title}}</p>

          <!-- 类型判断 -->
            <!-- 单选 -->
            <el-radio-group v-model="saveData.typeRadio[scope.$index]" v-if ="dropCol[index].id == '2' && setQdata.type == 3" >
              <el-radio :label="tableData[scope.$index].typeRadio + item.id">{{tableInput}}</el-radio>
            </el-radio-group>
           <!-- 多选 -->
            <el-checkbox-group v-model="checkList" v-if ="dropCol[index].id == '2' && setQdata.type == 6">
                <el-checkbox :label="tableData[scope.$index].id">{{tableInput}}</el-checkbox>
            </el-checkbox-group>
            <!-- 填空 -->
            <el-input  v-if ="dropCol[index].id == '2' && setQdata.type == 9" v-model="tableData[scope.$index].tableInput[item.id]" placeholder="请输入内容"></el-input>

            <!-- 答案 -->
            <!-- 单选 -->
            <el-radio-group v-model="saveData.typeRadio[scope.$index]" v-if ="dropCol[index].id > '2' && setQdata.type == 3">
              <el-radio :label="tableData[scope.$index].typeRadio + item.id">{{tableInput}}</el-radio>
            </el-radio-group>
            <!-- 多选 -->
            <el-checkbox-group v-model="tableData[scope.$index].checkList" v-if ="dropCol[index].id > '2' && setQdata.type == 6">
                <el-checkbox :label="item.id">{{tableInput}}</el-checkbox>
            </el-checkbox-group>
            <el-input  v-if ="dropCol[index].id > '2' && setQdata.type == 9" v-model="tableData[scope.$index].tableInput[item.id]" placeholder="请输入内容"></el-input>
        </template>
      </el-table-column>
    </el-table>
<!-- 
  
  编辑题目

 -->
    <el-dialog title="编辑题目" :visible.sync="visible">
       <div class="setQ-title">
        <div class="setQ-title-title">矩形类型：</div>
        <el-radio-group v-model="setQdata.type">
          <el-radio :label="3">单选</el-radio>
          <el-radio :label="6">多选</el-radio>
          <el-radio :label="9">填空</el-radio>
          <el-radio :label="12">下拉</el-radio>
        </el-radio-group>
       </div>
       <div class="setQ-content">
        <mtable :type = "setQdata.type" ref="mtable"></mtable>
       </div>
       <span slot="footer" class="dialog-footer">
        <el-button @click="visible = false">取 消</el-button>
        <el-button type="primary" @click="saveDataZ()">确 定</el-button>
      </span>
    </el-dialog>
  
  </div>
</template>
<script>
import Mtable from './components/table.vue'
export default {
  name: 'multTask',
  data() {
    return {
      // setTitleMode: false, //控制设置标题弹框
      // setContentMode: false
      saveData:{
        tableInput: {},
        typeRadio: {},
        checkList: {}
      },
      title: '点击设置标题',
      radio: -1,
      visible: false, // 设置标题弹出框
      radioText: '',
      tableInput: '',
      checkList: [],
      // jsonData
      jsonData: [{
        context:[{
          data: [{
            items: []
          }]
        }]
      }],
      // 矩形数据
      setQdata: {
        type: 3,
        num: 3,
        col: [
          {
            label: '标题',
            prop: 'title',
            id: '1',
            typeRadio: 10,
            tableInput:{}
          }
        ],
        ans: []  // 单选答案
      },
      col: [
        {
          label: '标题',
          prop: 'title',
          id: '1',
          typeRadio: 10
        },
        {
          label: '选项',
          prop: 'choise',
          id: '2',
          typeRadio: 20
        }
      ],
      dropCol: [
        {
          label: '标题',
          prop: 'title',
          id: '1',
          typeRadio: 10
        },
        {
          label: '选项',
          prop: 'choise',
          id: '2',
          typeRadio: 20
        }
      ],
      tableData: [
        {
          id: 1,
          title: '',
          choise: '',
          anwes: '',
          typeRadio: {},
          checkList: [],
          tableInput: {}
        }
      ]
    }
  },
  methods: {
    setTitle() {
      // 设置标题
      this.$prompt('请输入标题', '', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputPattern: /\S/,
          inputErrorMessage: '标题不能为空'
        }).then(({ value }) => {
          this.title = value
          this.$message({
            type: 'success',
            message: '标题: ' + value
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消输入'
          });
        });
    },
    // 设置json数据传入vuex 
    saveStoreData() {
      context.data[0].item = []
      let context = this.jsonData[0].context[0]
      let data = context.data[0]
      let title = "<p>" + this.title + "</p>"  // 矩阵题型标题
      let type;    // 矩阵题型类型
      switch (this.setQdata.type) {
        case 3:
          type = "matrixRadio"
          break;
        case 6:
          type = "matrixBlank"
          break;
        case 9:
          type = "matrix"
          break;
        case 12:
          type = "matrix"
          break
      }
      context.type = type
      context.title = title
      data.rows = this.tableData.length + 1    // 条数
      data.cols = this.$refs.mtable.dropCol.length    // 列shu
      this.jsonData[0].questionTypes = type  // 问题类型
      let contextJSON = JSON.stringify(this.jsonData[0].context[0])  // 转换json字符串
      this.dropCol.map((it, ins) => {
          let y = {
              x:0,
              y:ins,
              title:it.label,
              isChecked: false,
              isLable: true,
              blank: false
            }
          data.items.push(y)
        })
      this.$refs.mtable.tableData.map((item, index) => {
        for (var i = 0; i<this.dropCol.length; i ++){
          let y = {
              x:index + 1,
              y:i,
              title: this.dropCol[i].label == '标题' ? item.title : '',
              isChecked: (this.setQdata.type == 3 || this.setQdata.type == 6) && this.dropCol[i].label !== '标题' && (item.typeRadio[index] !== undefined && item.typeRadio[index].substr(-1) == i + 1) ? true : false,
              isLable: this.dropCol[i].label == '标题' ||  this.setQdata.type == 9 ? true : false,
              blank: this.setQdata.type == 9  && this.dropCol[i].label !== '标题' ? item.tableInput[index] : ''
            }
          data.items.push(y)
        }
      })
      // console.log(this.setQdata.col.length)
      console.log(this.$refs.mtable.tableData)
      this.$store.commit('setData', this.jsonData)
      console.log(this.$store.state)
    },
    // 保存按钮 取子组件数据
    saveDataZ(){
      // this.tableData = this.$refs.mtable.tableData    // 题目数据
      this.tableData = []
      this.$refs.mtable.tableData.map((item, index) => {
        let y = {
          id: item.id,
          title: item.title,
          typeRadio: {},
          checkList: [],
          tableInput: {}
        }
        this.tableData.push(y)
      })
      this.col = this.$refs.mtable.dropCol  // table头部
      this.dropCol = this.$refs.mtable.dropCol  // toubu 
      this.visible = false
      // 保存数据到vuex
      this.saveStoreData()
    }
  },
  mounted() {
  },
  components: {
    Mtable
  }
}
</script>
<style scoped>
  ul{
    width: 80%;
    min-width: 900px;
  }
  ul li {
    list-style: none;
    display: flex;
    padding: 10px;
  }
  ul li .title{
    flex: 1;
    margin-right: 20px;
    text-align: left;
  }
  ul li .choise{
    padding-right: 10px;
  }
  p{
    font-size: 16px;
  }
  .setQ-title{
    position: relative
  }
  .setQ-title .setQ-title-title{
    float: left;
    margin-left: 40px;
    margin-top: -2px;
  }
</style>