<template>
  <div style="width:100%;margin-top: 40px" id="table">
    <div style="margin:10px">
      <el-button @click="addData()">添加一条数据</el-button>
      <el-button @click="addChoise()">添加选择</el-button>
    </div>
    <el-table :data="tableData"
      ref="multipleTable"
      border
      align="left"
      >
     <el-table-column v-for="(item, index) in col"
        :key="`col_${index}`"
        :prop="dropCol[index].prop"
        :label="item.label"> 
          <template slot-scope="scope">
            <!-- 输入框 -->
            <el-input  v-if ="dropCol[index].id == '1'" v-model="tableData[scope.$index].title" placeholder="请输入内容"></el-input>

          <!-- 类型判断 -->
            <!-- 单选 -->
            <el-radio-group v-model="tableData[scope.$index].typeRadio[scope.$index]" v-if ="dropCol[index].id == '2' && type == 3" >
              <el-radio :label="tableData[scope.$index].typeRadio + parseInt(item.id)">{{tableInput}}</el-radio>
            </el-radio-group>
           <!-- 多选 -->
            <el-checkbox-group v-model="tableData[scope.$index].checkList" v-if ="dropCol[index].id == '2' && type == 6">
                <el-checkbox :label="tableData[scope.$index].id">{{tableInput}}</el-checkbox>
            </el-checkbox-group>
            <!-- 填空 -->
            <el-input  v-if ="dropCol[index].id == '2' && type == 9" v-model="tableData[scope.$index].tableInput[item.id]" placeholder="请输入内容"></el-input>
<!-- -->
          <!-- 答案 -->
            <!-- 单选 -->
            <el-radio-group v-model="tableData[scope.$index].typeRadio[scope.$index]" v-if ="dropCol[index].id > '2' && type == 3">
              <el-radio :label="tableData[scope.$index].typeRadio + parseInt(item.id)">{{tableInput}}</el-radio>
            </el-radio-group>
            <!-- 多选 -->
            <el-checkbox-group v-model="tableData[scope.$index].checkList" v-if ="dropCol[index].id > '2' && type == 6">
                <el-checkbox :label="item.id">{{tableInput}}</el-checkbox>
            </el-checkbox-group>
            <el-input  v-if ="dropCol[index].id > '2' && type == 9" v-model="tableData[scope.$index].tableInput[item.id]" placeholder="请输入内容"></el-input>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
import Sortable from 'sortablejs'
export default {
  props: {
    type: Number
  },
  data() {
    return {
      saveData:{
        tableInput: {},
        typeRadio: {},
        checkList: {}
      },
      tableInput: '',
      typeRadio: 0,
      checkList: [],
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
          prop: '',
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
  mounted() {
    this.rowDrop()
    this.columnDrop()
  },
  methods: {
    //行拖拽
    rowDrop() {
      const tbody = document.querySelector('#table .el-table__body-wrapper tbody')
      const _this = this
      Sortable.create(tbody, {
        onEnd({ newIndex, oldIndex }) {
          const currRow = _this.tableData.splice(oldIndex, 1)[0]
          _this.tableData.splice(newIndex, 0, currRow)
        }
      })
    },
    //列拖拽
    columnDrop() {
      const wrapperTr = document.querySelector('#table .el-table__header-wrapper tr')
      this.sortable = Sortable.create(wrapperTr, {
        animation: 180,
        delay: 0,
        onEnd: evt => {
          const oldItem = this.dropCol[evt.oldIndex]
          this.dropCol.splice(evt.oldIndex, 1)
          this.dropCol.splice(evt.newIndex, 0, oldItem)
        }
      })
    },
    changeData (tableData) {
      let addChoiseData= {
          label: '答案',
          prop: '',
          id: '',
          typeRadio: {},
          checkList: [],
          tableInput: {}
      }
      this.$prompt('请输入标题', '', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputPattern: /\S/,
          inputErrorMessage: '标题不能为空'
        }).then(({ value }) => {
          addChoiseData.label = value
          addChoiseData.id = tableData.length + 1
          addChoiseData.typeRadio = 1*tableData.length + 10
          this.dropCol.push(addChoiseData)
          this.col.push(addChoiseData)
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消输入'
          });
      });
    },
    // 添加一行数据
    addData () {
      let addData = {
          id: '',
          title: '',
          choise: '',
          anwes: '',
          typeRadio: {},
          checkList: [],
          tableInput: {}
      }
      addData.id = this.tableData.length + 1
      this.tableData.push(addData)
    },
    // 添加一列数据
    addChoise () {
      this.changeData(this.col)
    }
  }
}
</script>
<style scoped>
</style>