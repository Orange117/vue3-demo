<template>
  <div class="table-box">
    <!-- 标题 -->
    <div class="title">
      <h2>CRUD Demo</h2>
    </div>
    <!-- query -->
    <div class="query-box">
      <el-input class="query-input" v-model="queryInput" placeholder="请输入姓名搜索" @change="handleQueryName"/>
      <div class="btn-lsit">
        <el-button type="primary" @click="handleAdd">增加</el-button>
        <el-button type="danger" @click="handleDelList" v-if="multipleSelection.length > 0">删除多选</el-button>
      </div>
      
    </div>
    <!-- table -->
    <el-table 
      border
      ref="multipleTableRef"
      :data="tableData"
      style="width: 100%"
      @selection-change="handleSelectionChange"
    >
    <el-table-column type="selection" width="55" />
    <el-table-column prop="name" label="姓名" width="120" />
    <el-table-column prop="email" label="邮箱" width="150" />
    <el-table-column prop="phone" label="电话" width="120" />
    <el-table-column prop="state" label="状态" width="120" />
    <el-table-column prop="address" label="地址" width="300" />
    <el-table-column fixed="right" label="操作" width="120">
      <template #default="scope">
        <el-button link type="primary" size="small" @click="handleRowDel(scope.row)" style="color: #F56C6C;">删除</el-button>
        <el-button link type="primary" size="small" @click="handleEdit(scope.row)">编辑</el-button>
      </template>
    </el-table-column>
    </el-table>

    <el-pagination 
      background 
      layout="prev, pager, next" 
      :total="total"
      style="display: flex;justify-content: center;margin-top: 10px;"
      v-model:current-page="curPage"
      @current-change="handleChangePage"
    />

    <!-- dialog -->
    <el-dialog v-model="dialogFormVisible" :title="dialogType === 'add' ? '新增' : '编辑'">
    <el-form :model="tableForm">
      <el-form-item label="姓名" :label-width="80">
        <el-input v-model="tableForm.name" autocomplete="off" />
      </el-form-item>
      <el-form-item label="邮箱" :label-width="80">
        <el-input v-model="tableForm.email" autocomplete="off" />
      </el-form-item>
      <el-form-item label="电话" :label-width="80">
        <el-input v-model="tableForm.phone" autocomplete="off" />
      </el-form-item>
      <el-form-item label="状态" :label-width="80">
        <el-input v-model="tableForm.state" autocomplete="off" />
      </el-form-item>
      <el-form-item label="地址" :label-width="80">
        <el-input v-model="tableForm.address" autocomplete="off" />
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button type="primary" @click="dialogConfirm">
          确认
        </el-button>
      </span>
    </template>
  </el-dialog>

  </div>
</template>
<script setup>

import { ref } from 'vue';
import request from "./utils/request.js"

/* 数据 */
let queryInput = $ref("")
let tableData = $ref([
  {
    id:"1",
    name: 'Tom1',
    email: "123@qq.com",
    phone: "15022797273",
    state: 'California',
    address: 'No. 189, Grove St, Los Angeles',
  },
  {
    id:"2",
    name: 'Tom2',
    email: "123@qq.com",
    phone: "15022797273",
    state: 'California',
    address: 'No. 189, Grove St, Los Angeles',
  },
  {
    id:"3",
    name: 'Tom3',
    email: "123@qq.com",
    phone: "15022797273",
    state: 'California',
    address: 'No. 189, Grove St, Los Angeles',
  },
  {
    id:"4",
    name: 'Tom4',
    email: "123@qq.com",
    phone: "15022797273",
    state: 'California',
    address: 'No. 189, Grove St, Los Angeles',
  },

])
let tableDataCopy = Object.assign(tableData)
let multipleSelection = $ref([])
let dialogFormVisible = $ref(false)
let tableForm = $ref({
  name:'张三',
  email:"123@qq.com",
  phone:"12345678",
  state:"在职",
  address:"天津市",
})
let dialogType = $ref('add')
let total = $ref(10)
let curPage = $ref(1)


/* 方法 */

const getTableData = async (cur = 1) => {
  let res = await request.get('/list', {
    pageSize:10,
    pageNum: cur
  })
  tableData = res.list
  total = res.total
  curPage = res.pageNum
}

getTableData(1)

// 请求分页
const handleChangePage = (val) => {
  getTableData(curPage)
}

// 删除一条
const handleRowDel = async ({ID}) => {
  // console.log(id)
  // // 1. 通过id获取到条目对应的 索引值
  // let index = tableData.findIndex(item => item.id === id)
  // // 2. 通过索引值进行删除对应条目
  // tableData.splice(index, 1)
  await request.delete(`/delete/${ID}`)
  await getTableData(curPage)
}

// 删除多条
const handleDelList = () => {
  multipleSelection.forEach(ID => {
    handleRowDel({ID})
  })
  multipleSelection = []
}

// 选中
const handleSelectionChange = (val) => {
  // multipleSelection = val
  // console.log(val)
  multipleSelection = []
  val.forEach(item => {
    multipleSelection.push(item.ID)
  })
  
}

// 新增
const handleAdd = () => {
  dialogFormVisible = true
  tableForm = {}
  dialogType = 'add'
}

// 确认
const dialogConfirm = async () => {
  dialogFormVisible = false


  // 判断新增还是编辑
  if(dialogType === 'add') {
    // 1.拿到数据
    // 2.添加到table
    // tableData.push({
    //   id: (tableData.length + 1).toString(),
    //   ...tableForm
    // })

    await request.post("/add",{
      ...tableForm
    })
    await getTableData(curPage)

  } else {
    // 获取的当前索引
    // let index = tableData.findIndex(item => item.id === tableForm.id)
    // console.log(index)
    // tableData[index] = tableForm
    // 替换当前索引值对应的数据

    // 修改 内容
    await request.put(`/update/${tableForm.ID}`, {
      ...tableForm
    })
    // 刷新数据
    await getTableData(curPage)

  }

}

// 编辑
const handleEdit = (row) => {
  dialogFormVisible = true
  dialogType = 'edit'
  console.log(row)
  tableForm = {...row}
}

// 搜索
const handleQueryName = async (val) => {
  // console.log(queryInput)
  // console.log(val)

  // if(val.length > 0){
  //   tableData = tableData.filter(item => (item.name).toLowerCase().match(val.toLowerCase()))
  // } else {
  //   tableData = tableDataCopy
  // }

  if (val.length > 0) {
    tableData = await request.get(`/list/${val}`)
  } else {
    await getTableData(curPage)
  }
}

</script>

<style scoped>
.table-box {
  margin: 200px auto;
  width: 800px;

}

.title {
  text-align: center;
}

.query-box {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}
.query-input {
  width:200px;
}

</style>
