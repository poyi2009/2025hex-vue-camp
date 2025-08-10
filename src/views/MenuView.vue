<script setup>
import { ref } from 'vue'
const drinks = ref([
  {
    id: 1,
    name: '珍珠奶茶',
    description: '香濃奶茶搭配QQ珍珠',
    price: 50,
    stock: 20,
    edit: false,
  },
  {
    id: 2,
    name: '冬瓜檸檬',
    description: '清新冬瓜配上新鮮檸檬',
    price: 45,
    stock: 18,
    edit: false,
  },
  {
    id: 3,
    name: '翡翠檸檬',
    description: '綠茶與檸檬的完美結合',
    price: 55,
    stock: 34,
    edit: false,
  },
  {
    id: 4,
    name: '四季春茶',
    description: '香醇四季春茶，回甘無比',
    price: 45,
    stock: 10,
    edit: false,
  },
  {
    id: 5,
    name: '阿薩姆奶茶',
    description: '阿薩姆紅茶搭配香醇鮮奶',
    price: 50,
    stock: 25,
    edit: false,
  },
  {
    id: 6,
    name: '檸檬冰茶',
    description: '檸檬與冰茶的清新組合',
    price: 45,
    stock: 20,
    edit: false,
  },
  {
    id: 7,
    name: '芒果綠茶',
    description: '芒果與綠茶的獨特風味',
    price: 55,
    stock: 18,
    edit: false,
  },
  {
    id: 8,
    name: '抹茶拿鐵',
    description: '抹茶與鮮奶的絕配',
    price: 60,
    stock: 20,
    edit: false,
  },
])
function reduceNum(drink) {
  if (drink.stock > 0) {
    drink.stock -= 1
  }
}
function addNum(drink) {
  drink.stock += 1
}
function startEdit(id) {
  const drink = drinks.value.find((item) => item.id === id)
  drink.edit = true
}
function endEdit(id) {
  const drink = drinks.value.find((item) => item.id === id)
  drink.edit = false
}
</script>
<template>
  <div class="container">
    <div class="d-flex flex-column">
      <h2>WEEK 1 : 餐點管理工具</h2>
      <table class="table table-hover align-middle">
        <thead class="table-dark">
          <tr>
            <th scope="col">品項</th>
            <th scope="col">描述</th>
            <th scope="col">價格</th>
            <th scope="col">庫存</th>
            <th scope="col">操作</th>
            <th scope="col"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="drink in drinks" :key="drink.id">
            <td>
              {{ drink.name }}
            </td>
            <td>
              {{ drink.description }}
            </td>
            <td>{{ drink.price }} 元</td>
            <td class="d-flex justify-content-between align-items-center">
              <button v-on:click="reduceNum(drink)" class="btn btn-outline-secondary">－</button>
              <span>{{ drink.stock }}</span>
              <button v-on:click="addNum(drink)" class="btn btn-outline-secondary">＋</button>
            </td>
            <td v-if="drink.edit"></td>
            <td v-else>
              <button class="btn btn-outline-secondary" v-on:click="startEdit(drink.id)">
                編輯
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <div v-for="drink in drinks" :key="drink.id">
        <div v-if="drink.edit" class="input-group mb-5 w-25">
          <input type="text" class="form-control" v-model="drink.name" />
          <button v-on:click="endEdit(drink.id)" class="btn btn-outline-success">確認</button>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@200;700&display=swap');
.container {
  font-family: 'Oswald';
  text-align: center;
}
h2 {
  margin-top: 16px;
  margin-bottom: 16px;
}
</style>
