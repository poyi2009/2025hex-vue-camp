<script setup>
import { ref, computed, onMounted } from 'vue'
const newName = ref('') //名稱
const newNumber = ref(0) //價格
//新增商品
const addProduct = () => {
  data.value.push({
    id: new Date().getTime(),
    name: newName.value,
    price: newNumber.value,
  })
  //表單初始化
  newName.value = ''
  newNumber.value = 0
}

const data = ref([]) //商品定義空陣列
//刪除品項
const delItem = (id) => {
  const index = data.value.findIndex((item) => item.id === id)
  data.value.splice(index, 1)
}
//computed計算價格總計
const sum = computed(() => {
  let tempSum = 0
  data.value.forEach((item) => {
    tempSum += Number(item.price)
  })
  return tempSum
})
//DOM掛載後執行
onMounted(() => {
  //載入初始數據 模擬AJAX請求
  setTimeout(() => {
    data.value = [
      { id: 1, name: '珍珠奶茶', price: 50 },
      { id: 2, name: '冬瓜檸檬', price: 45 },
      { id: 3, name: '翡翠檸檬', price: 55 },
      { id: 4, name: '四季春茶', price: 45 },
      { id: 5, name: '阿薩姆奶茶', price: 50 },
      { id: 6, name: '檸檬冰茶', price: 45 },
      { id: 7, name: '芒果綠茶', price: 55 },
      { id: 8, name: '抹茶拿鐵', price: 60 },
    ]
  }, 5000)
})
</script>
<template>
  <div class="container border border-secondary rounded-3">
    <div class="p-4">
      <div class="pb-3">
        <label for="newName" class="form-label">品名</label>
        <input type="text" id="newName" v-model.trim="newName" class="form-control w-auto" />
      </div>
      <div class="pb-3">
        <label for="newNumber" class="form-label">價格</label>
        <input type="number" id="newNumber" v-model.trim="newNumber" class="form-control w-auto" />
      </div>
      <button type="button" @click="addProduct" class="btn btn-dark">新增</button>
    </div>
    <div class="text-center p-4">
      <table class="table table-hover">
        <thead>
          <tr class="table-dark">
            <th class="col">品名</th>
            <th class="col">價格</th>
            <th class="col">調整價格</th>
            <th class="col">刪除</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in data" :key="item.id" class="text-center align-middle">
            <td>{{ item.name }}</td>
            <td>{{ item.price }}</td>
            <td class="text-center align-middle">
              <input type="number" v-model="item.price" class="form-control w-auto mx-auto" />
            </td>
            <td>
              <button type="button" @click="delItem(item.id)" class="btn btn-danger">刪除</button>
            </td>
          </tr>
        </tbody>
      </table>
      <h2>價格加總：{{ sum }}</h2>
    </div>
  </div>
</template>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@200;700&display=swap');
.container {
  font-family: 'Oswald';
}
</style>
