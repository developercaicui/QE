<template>
  <div class="multTask">
    <p @click="setTitle">{{title}}</p>
    <ul>
      <li v-for="(item, index) in tableData" :key="index">
        <div class="title" @click="visible = true">{{item.title}}</div>
        <div class="choise">
          <el-radio v-model="radio" :label="index">备选项</el-radio>
        </div>
      </li>
    </ul>
<!-- 
  
  编辑题目

 -->
    <el-dialog title="编辑题目" :visible.sync="visible">
       <div class="setQ-title">
        <div class="setQ-title-title">矩形类型：</div>
        <el-radio-group v-model="setQdata.radio">
          <el-radio :label="3">单选</el-radio>
          <el-radio :label="6">多选</el-radio>
          <el-radio :label="9">填空</el-radio>
          <el-radio :label="12">下拉</el-radio>
        </el-radio-group>
       </div>
       <div class="setQ-content">
        <mtable :type = "setQdata.radio"></mtable>
       </div>
       <span slot="footer" class="dialog-footer">
        <el-button @click="visible = false">取 消</el-button>
        <el-button type="primary" @click="">确 定</el-button>
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
      radio: 0,
      visible: false, // 设置标题弹出框
      setQdata: {
        radio: 3
      },
      tableData: [{
            title: '2016-05-02',
            choise: '王小虎',
            num: 0
          }, {
            title: '2016-05-04',
            choise: '王小虎',
            num: 1
          }, {
            title: '2016-05-01',
            choise: '王小虎',
            num: 2
          }, {
            title: '2016-05-03',
            choise: '王小虎',
            num: 3
          }]
    }
  },
  methods: {
    setTitle() {
      // 设置标题
      this.$prompt('请输入标题', '', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputPattern: '',
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
    width: 200px;
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