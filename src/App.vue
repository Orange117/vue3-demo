<template>
  <div class="table-box">
    <!-- 标题 -->
    <div class="title">
      <h2>CRUD Demo</h2>
    </div>
    <!-- query -->
    <div class="query-box">
      <el-input class="query-input" v-model="queryInput" placeholder="请输入姓名搜索"/>
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


/* 方法 */
// 删除一条
const handleRowDel = ({id}) => {
  //console.log(id)
  let index = tableData.findIndex(item=>item.id === id)
  tableData.splice(index, 1)
}

// 删除多条
const handleDelList = () => {
  multipleSelection.forEach(id => {
    handleRowDel({id})
  })
  multipleSelection = []
}

// 选中
const handleSelectionChange = (val) => {
  // multipleSelection = val
  // console.log(val)
  multipleSelection = []
  val.forEach(item => {
    multipleSelection.push(item.id)
  })
  
}

// 新增
const handleAdd = () => {
  dialogFormVisible = true
  tableForm = {}
  dialogType = 'add'
}

// 确认
const dialogConfirm = () => {
  dialogFormVisible = false


  // 判断新增还是编辑
  if(dialogType === 'add') {
    // 1.拿到数据
    // 2.添加到table
    tableData.push({
      id: (tableData.length + 1).toString(),
      ...tableForm
    })
  } else {
    // 获取的当前索引
    let index = tableData.findIndex(item => item.id === tableForm.id)
    console.log(index)
    tableData[index] = tableForm
    // 替换当前索引值对应的数据
  }

  
  
}

// 编辑
const handleEdit = (row) => {
  dialogFormVisible = true
  dialogType = 'edit'
  console.log(row)
  tableForm = {...row}
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
