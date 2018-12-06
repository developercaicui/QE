<template>
  <div class="multTask">
    <p @click="setTitle">{{title}}</p>
    <ul>
      <li v-for="(item, index) in tableData" :key="index">
        <div class="title" @click="visible = true">{{item.title}}</div>
        <div class="choise">
          <el-radio v-model="radio" :label="col"  v-if = "setQdata.type == 3" v-for="col in setQdata.col">{{col.label}}</el-radio>
          <el-checkbox-group v-model="item.checkList" v-if ="setQdata.type == 6">
            <el-checkbox :label="item.id">{{item.checkList}}</el-checkbox>
          </el-checkbox-group>
          <el-input  v-if ="setQdata.type == 9" v-model="item.tableInput" placeholder="请输入内容"></el-input>
        </div>
      </li>
    </ul>
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
        <el-button type="primary" @click="saveData()">确 定</el-button>
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
      title: '点击设置标题',
      radio: -1,
      visible: false, // 设置标题弹出框
      // 矩形数据
      setQdata: {
        type: 3,
        num: 3,
        col: [
          {
            label: '标题',
            prop: 'title',
            id: '1',
            typeRadio: 10
          }
        ]
      },
      tableData: [{
            title: '点击设置标题',
            choise: '',
            num: 0
          }]
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
    setQus() {

    },
    saveData(){
      this.tableData = this.$refs.mtable.tableData
      this.setQdata.col = this.$refs.mtable.col.slice(2)
      this.visible = false
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
    flex: 1
  }
  ul li .choise{
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