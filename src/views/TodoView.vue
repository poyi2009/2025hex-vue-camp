<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
const api = 'https://todolist-api.hexschool.io' //後端網址
const token = ref('')
// 註冊
const emailSignup = ref('')
const pwdSignup = ref('')
const nickname = ref('')
const signup = async () => {
  try {
    const res = await axios.post(`${api}/users/sign_up`, {
      email: emailSignup.value,
      password: pwdSignup.value,
      nickname: nickname.value,
    })
    alert(`${res.data.nickname} 註冊成功`)
    emailSignup.value = ''
    pwdSignup.value = ''
    nickname.value = ''
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//取得使用者資料
const fetchUserData = async () => {
  if (!token.value) {
    user.value = []
    return
  }
  try {
    const res = await axios.get(`${api}/users/checkout`, {
      headers: {
        Authorization: token.value,
      },
    })
    user.value = res.data
  } catch (error) {
    console.log(error.response.data.message)
  }
}
//取得使用者todos資料
const getTodos = async () => {
  try {
    const res = await axios.get(`${api}/todos/`, {
      headers: {
        Authorization: token.value,
      },
    })
    const newList = res.data.data.map((item) => {
      return {
        ...item,
        isEditing: false,
        tempContent: item.content,
      }
    })
    todos.value = newList
  } catch (error) {
    console.log(error.response.data.message)
  }
}
//登入
const emailSignin = ref('')
const pwdSignin = ref('')
const signin = async () => {
  try {
    const res = await axios.post(`${api}/users/sign_in`, {
      email: emailSignin.value,
      password: pwdSignin.value,
    })
    token.value = res.data.token
    document.cookie = `customTodoToken=${res.data.token};path=;` //放於cookie
    emailSignin.value = ''
    pwdSignin.value = ''
    alert(`登入成功`)
    await fetchUserData()
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//驗證
const user = ref({
  nickname: '',
  uid: '',
})
// 載入頁面到onMounted階段會檢查是否登入狀態
onMounted(async () => {
  try {
    token.value = document.cookie.replace(/(?:^|.*;\s*)customTodoToken\s*=\s*([^;]*).*$/i, '$1') //從cookie取出token
    if (token.value) {
      await fetchUserData() //驗證
      await getTodos() //取得todos
    }
  } catch (error) {
    console.log(error.response.data.message)
  }
})
//登出
const signout = async () => {
  try {
    const res = await axios.post(
      `${api}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    document.cookie = `customTodoToken=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=;` //清除cookie
    token.value = null
    alert(`${res.data.message}`)
    await fetchUserData()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
//TodoList
const todo = ref('') //單筆待辦
const todos = ref([]) //既有待辦
//新增待辦
const addTodo = async () => {
  try {
    const res = await axios.post(
      `${api}/todos/`,
      {
        content: todo.value,
      },
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    todo.value = ''
    alert(`新增成功：${res.data.newTodo.content}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message[0]}`)
  }
}
//編輯待辦
const editTodo = async (id) => {
  const index = todos.value.findIndex((item) => item.id === id)
  todos.value[index].isEditing = true //可編輯狀態
}
//儲存編輯待辦
const saveEdit = async (id, tempContent) => {
  try {
    const res = await axios.put(
      `${api}/todos/${id}`,
      {
        content: tempContent,
      },
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    const index = todos.value.findIndex((item) => item.id === id)
    todos.value[index].isEditing = false //不可編輯狀態
    alert(`${res.data.message}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message[0]}`)
  }
}
//取消編輯
const cancelEdit = async (id) => {
  const index = todos.value.findIndex((item) => item.id === id)
  todos.value[index].isEditing = false
}
//刪除待辦
const delTodo = async (id) => {
  try {
    const res = await axios.delete(`${api}/todos/${id}`, {
      headers: {
        Authorization: token.value,
      },
    })
    alert(`${res.data.message}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message[0]}`)
  }
}
//修改待辦狀態
const editStatus = async (id) => {
  try {
    const res = await axios.patch(
      `${api}/todos/${id}/toggle`,
      {},
      {
        headers: {
          Authorization: token.value,
        },
      },
    )
    alert(`${res.data.message}`)
    await getTodos()
  } catch (error) {
    alert(`${error.response.data.message[0]}`)
  }
}
</script>
<template>
  <div class="container d-flex flex-row justify-content-center">
    <div class="pe-3" style="width: 600px">
      <!-- 註冊區塊 -->
      <div class="d-flex flex-column align-items-center p-4 mb-3 border border-secondary rounded-3">
        <h2 class="pb-2">註冊</h2>
        <div>
          <label for="emailSignup" class="form-label">帳號</label>
          <input
            type="text"
            id="emailSignup"
            placeholder="請輸入有效信箱"
            v-model="emailSignup"
            class="form-control w-auto mx-auto mb-3"
          />
        </div>
        <div>
          <label for="pwdSignup" class="form-label">密碼</label>
          <input
            type="password"
            id="pwdSignup"
            placeholder="請輸入密碼"
            v-model="pwdSignup"
            class="form-control w-auto mx-auto mb-3"
          />
        </div>
        <div>
          <label for="nickname" class="form-label">暱稱</label>
          <input
            type="text"
            id="nickname"
            placeholder="請輸入暱稱"
            v-model="nickname"
            class="form-control w-auto mx-auto mb-3"
          />
        </div>
        <button type="button" @click="signup" class="btn btn-dark">註冊</button>
      </div>
      <!-- 登入區塊 -->
      <div class="d-flex flex-column align-items-center p-4 mb-3 border border-secondary rounded-3">
        <h2 class="pb-2">登入</h2>
        <div>
          <label for="emailSignin" class="form-label">帳號</label>
          <input
            type="text"
            id="emailSignin"
            placeholder="請輸入有效信箱"
            v-model="emailSignin"
            class="form-control w-auto mx-auto mb-3"
          />
        </div>
        <div>
          <label for="pwdSignin" class="form-label">密碼</label>
          <input
            type="password"
            id="pwdSignin"
            placeholder="請輸入密碼"
            v-model="pwdSignin"
            class="form-control w-auto mx-auto mb-3"
          />
        </div>
        <button type="button" @click="signin" class="btn btn-dark">登入</button>
      </div>
      <!-- 驗證區塊 -->
      <div class="d-flex flex-column align-items-center p-4 mb-3 border border-secondary rounded-3">
        <h2 class="pb-2">驗證</h2>
        <div v-if="user.uid" class="text-center">
          <p>{{ user.nickname }} 登入中</p>
        </div>
        <div v-else class="text-center">
          <p>尚未登入</p>
        </div>
      </div>
      <!-- 登出區塊 -->
      <div class="d-flex flex-column align-items-center p-4 mb-3 border border-secondary rounded-3">
        <h2 class="pb-2">登出</h2>
        <div v-if="user.uid" class="text-center">
          <button type="button" @click="signout" class="btn btn-dark">登出</button>
        </div>
      </div>
    </div>
    <!-- 清單區塊 -->
    <div class="d-flex flex-column text-center p-4 mb-3 border border-secondary rounded-3 w-75">
      <h2 class="pb-4">{{ user.nickname }} TODO LIST</h2>
      <!-- 新增清單 -->
      <div v-if="user.uid">
        <div class="input-group">
          <input type="text" v-model.trim="todo" class="form-control w-auto mx-auto mb-3" />
          <button type="button" @click="addTodo" class="btn btn-dark mb-3">新增</button>
        </div>
        <!-- 清單列表 -->
        <ul v-for="item in todos" :key="item.id" class="list-group">
          <li class="list-group-item">
            <div class="d-flex flex-row align-items-center justify-content-between">
              <!-- 待辦狀態勾選 -->
              <div v-if="item.status" class="form-check">
                <input
                  type="checkbox"
                  :id="item.id"
                  @click="editStatus(item.id)"
                  checked
                  class="form-check-input"
                />
              </div>
              <div v-else class="form-check">
                <input
                  type="checkbox"
                  :id="item.id"
                  @click="editStatus(item.id)"
                  class="form-check-input"
                />
              </div>
              <!-- 編輯待辦內容 -->
              <template v-if="item.isEditing">
                <div>
                  <input
                    type="text"
                    v-model.trim="item.tempContent"
                    class="form-control w-auto mx-auto"
                  />
                </div>
                <div>
                  <button
                    type="button"
                    @click="saveEdit(item.id, item.tempContent)"
                    class="btn btn-success me-2"
                  >
                    儲存
                  </button>
                  <button type="button" @click="cancelEdit(item.id)" class="btn btn-outline-dark">
                    取消
                  </button>
                </div>
              </template>
              <!--待辦狀態:已完成-->
              <template v-else-if="item.status">
                <div>
                  <del class="text-secondary">{{ item.content }}</del>
                </div>
                <div>
                  <button type="button" @click="editTodo(item.id)" class="btn btn-secondary me-2">
                    編輯
                  </button>
                  <button type="button" @click="delTodo(item.id)" class="btn btn-danger">
                    刪除
                  </button>
                </div>
              </template>
              <!--待辦狀態:未完成-->
              <template v-else>
                <div>
                  {{ item.content }}
                </div>
                <div>
                  <button type="button" @click="editTodo(item.id)" class="btn btn-secondary me-2">
                    編輯
                  </button>
                  <button type="button" @click="delTodo(item.id)" class="btn btn-danger">
                    刪除
                  </button>
                </div>
              </template>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@200;700&display=swap');
.container {
  font-family: 'Oswald';
}
</style>
