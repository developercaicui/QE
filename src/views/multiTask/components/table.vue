<template>
  <div style="width:100%;margin-top: 40px">
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
            <el-input  v-if ="dropCol[index].id == '1'" v-model="tableInput[scope.$index]" placeholder="请输入内容"></el-input>

          <!-- 类型判断 -->
            <!-- 单选 -->
            <el-radio-group v-model="typeRadio" v-if ="dropCol[index].id == '2' && type == 3" >
              <el-radio :label="tableData[scope.$index].id">选项</el-radio>
            </el-radio-group>
           <!-- 多选 -->
            <el-checkbox-group v-model="checkList" v-if ="dropCol[index].id == '2' && type == 6">
                <el-checkbox :label="tableData[scope.$index].id">选择</el-checkbox>
            </el-checkbox-group>
            <!-- 填空 -->
            <el-input  v-if ="dropCol[index].id == '2' && type == 9" v-model="tableInput" placeholder="请输入内容"></el-input>

          <!-- 答案 -->
            <!-- 单选 -->
            <el-radio-group v-model="typeRadio" v-if ="dropCol[index].id > '2' && type == 3">
              <el-radio :label="tableData[scope.$index].id">设置答案</el-radio>
            </el-radio-group>
            <!-- 多选 -->
            <el-checkbox-group v-model="checkList[scope.$index]" v-if ="dropCol[index].id > '2' && type == 6">
                <el-checkbox :label="tableData[scope.$index].id">设置答案</el-checkbox>
            </el-checkbox-group>
            <el-input  v-if ="dropCol[index].id == '3' && type == 9" v-model="tableInput" placeholder="请输入内容"></el-input>
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
      tableInput: '',
      typeRadio: 0,
      checkList: [],
      data: {
          id: '',
          title: '',
          choise: '',
          anwes: '',
          typeRadio: 0
      },
      addChoiseData: {
          label: '答案',
          prop: '',
          id: '4',
          typeRadio: 10
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
        },
        {
          label: '答案',
          prop: 'anwes',
          id: '3',
          typeRadio: 30
        }
      ],
      dropCol: [
        {
          label: '标题',
          prop: 'title',
          id: '1'
        },
        {
          label: '选项',
          prop: 'choise',
          id: '2'
        },
        {
          label: '答案',
          prop: 'anwes',
          id: '3'
        }
      ],
      tableData: [
        {
          id: 1,
          title: '2016-05-02',
          choise: '王小虎1',
          anwes: '上海市普陀区金沙江路 100 弄',
          typeRadio: 10
        },
        {
          id: 2,
          title: '2016-05-02',
          choise: '王小虎1',
          anwes: '上海市普陀区金沙江路 100 弄',
          typeRadio: 20
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
      const tbody = document.querySelector('.el-table__body-wrapper tbody')
      const _this = this
      Sortable.create(tbody, {
        onEnd({ newIndex, oldIndex }) {
          console.log(newIndex)
          const currRow = _this.tableData.splice(oldIndex, 1)[0]
          _this.tableData.splice(newIndex, 0, currRow)
        }
      })
    },
    //列拖拽
    columnDrop() {
      const wrapperTr = document.querySelector('.el-table__header-wrapper tr')
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
    changeData (data) {
      data[data.length - 1].id = data.length + 1
      data[data.length - 1].typeRadio = 10*data.length + 10
    },
    // 添加一行数据
    addData () {
       this.tableData.push(this.data)
       this.changeData(this.tableData)
    },
    // 添加一列数据
    addChoise () {
      this.dropCol.push(this.addChoiseData)
      this.col.push(this.addChoiseData)
      this.changeData(this.dropCol)
      this.changeData(this.col)
      console.log(this.col)
    }
  }
}
</script>
<style scoped>
</style>