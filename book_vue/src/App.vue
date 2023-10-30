<script setup>
import {reactive, onMounted} from 'vue'
import axios from 'axios'
import {ref} from 'vue'
import {ElMessageBox} from "element-plus";

const add_dialog_visible = ref(false)
const books = reactive([])
const book_form = reactive({
  book_number: '',
  book_name: '',
  book_type: '',
  book_price: '',
  author: '',
  book_publisher: '',
})
const getStudents = async () => {
  try {
    const res = await axios.get('http://127.0.0.1:5000/books');
    const data = res.data.data;
    books.splice(0, books.length)
    books.push(...data);
    // console.log(data);
  } catch (error) {
    console.error(error);
  }
}

onMounted(() => {
  getStudents();
});


const handleDelete = async (index, row) => {
  const book_id = row.id;
  try {
    const res = await axios.get(`http://127.0.0.1:5000/books/del?book_id=${book_id}`);
    await getStudents()
  } catch (error) {
    console.error(error);
  }
};

const handleEdit = (index, row) => {
  console.log(index, row);
};


const handleClose = (done  => {
  ElMessageBox.confirm('Are you sure to close this dialog?')
      .then(() => {
        done()
      })
      .catch(() => {
        // catch error
      })
})

const submitData = async ()=>{
  try {
    const res = await axios.post(`http://127.0.0.1:5000/books`,book_form);
    reWrite()
    await getStudents()
    add_dialog_visible.value = false
  } catch (error) {
    console.error(error);
  }
}

const reWrite = ()=>{
  book_form.book_number = ''
  book_form.book_name = ''
  book_form.book_type = ''
  book_form.book_price = ''
  book_form.author = ''
  book_form.book_publisher = ''
}

</script>



<template>
  <!--  <div>-->
  <!--    <h1>图书管理系统</h1>-->
  <!--  </div>-->
  <div style="margin:0 auto;width:75%;">
    <h1 style="text-align:center">图书管理系统</h1>
    <!--    添加图书按钮-->
    <el-button type="primary" @click="add_dialog_visible = true" size="small">添加图书
    </el-button>
    <!--数据表格-->
    <el-table :data="books" style="margin:20px auto;">
      <el-table-column label="编号" prop="book_number"/>
      <el-table-column label="书名" prop="book_name"/>
      <el-table-column label="类型" prop="book_type"/>
      <el-table-column label="价格" prop="book_price"/>
      <el-table-column label="作者" prop="author"/>
      <el-table-column label="出版社" prop="book_publisher"/>
      <el-table-column align="right" label="操作" width="150px">
        <template #default="scope">
          <el-button size="small" @click="handleEdit(scope.$index,scope.row)">
            编辑
          </el-button>
          <el-button
              size="small"
              type="danger"
              @click="handleDelete(scope.$index,scope.row)"
          >
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>

  <el-dialog
      v-model="add_dialog_visible"
      title="Tips"
      width="30%">
<!--      :before-close="handleClose"-->
    <el-form
        v-model="book_form"
        status-icon
        label-width="120px"
        class="demo-ruleForm"
    >
      <el-form-item label="编号" prop="book_number">
        <el-input v-model="book_form.book_number" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="书名" prop="book_name">
        <el-input v-model="book_form.book_name" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="类型" prop="book_type">
        <el-input v-model="book_form.book_type" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="价格" prop="book_price">
        <el-input v-model="book_form.book_price" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="作者" prop="author">
        <el-input v-model="book_form.author" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="出版社" prop="book_publisher">
        <el-input v-model="book_form.book_publisher" autocomplete="off"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitData">提交</el-button>
        <el-button @click="reWrite">重置</el-button>
      </el-form-item>

    </el-form>
    <!--    <span>This is a message</span>-->
    <!--    <template #footer>-->
    <!--      <span class="dialog-footer">-->
    <!--        <el-button @click="add_dialog_visible = false">Cancel</el-button>-->
    <!--        <el-button type="primary" @click="add_dialog_visible = false">-->
    <!--          Confirm-->
    <!--        </el-button>-->
    <!--      </span>-->
    <!--    </template>-->
  </el-dialog>


</template>

<style scoped>

</style>
