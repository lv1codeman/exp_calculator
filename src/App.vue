<script setup>
import { ref } from 'vue'
import { onMounted } from 'vue'
const userLVL = ref('')
const userEXP = ref('')
const tarLVL = ref('')
const res = ref('aaa')

const daysToTarget = ref('')
const expsToTarget = ref('')
const lvlsToTarget = ref('')

const jsonData = ref(null)
// var jdata = null
const btnCalculate = () => {
  var user_level = parseInt(userLVL.value)
  var user_exp = parseInt(userEXP.value)
  var target_level = parseInt(tarLVL.value)

  console.log('使用者等級: ', jsonData.value[user_level - 1].lvl)
  console.log('使用者目前等級滿條: ', jsonData.value[user_level - 1].nextlvl)
  let to_next = jsonData.value[user_level - 1].nextlvl - user_exp
  console.log('再多少升級: ', to_next)

  //先計算到60等還有幾等
  console.log('使用者還差幾等可以60: ', target_level - user_level)

  let needloop = target_level - user_level - 1
  let needexp = to_next
  for (let i = user_level; i < needloop + user_level; ++i) {
    needexp += jsonData.value[i].nextlvl
  }
  console.log('還差多少經驗到60等: ', needexp)
  console.log('還差幾天可以60等: ', needexp / 3800)

  expsToTarget.value = needexp
  daysToTarget.value = (needexp / 3800).toFixed(2)
  lvlsToTarget.value = needloop + 1
  res.value =
    parseInt(userLVL.value) + parseInt(userEXP.value) + parseInt(tarLVL.value)
}

// let jsonData = null
onMounted(async () => {
  try {
    const response = await fetch('nextlevel.json')
    jsonData.value = await response.json()
  } catch (error) {
    console.error('Error fetching JSON:', error)
  }
})
</script>

<template>
  <div class="container">
    按下按鈕後讀取json的資料運算
    <br />
    57 13835 60 <br />
    目前聯覺等級
    <el-input
      v-model="userLVL"
      style="width: 240px"
      placeholder="目前聯覺等級"
      clearable
    />
    <br />
    目前聯覺經驗
    <el-input
      v-model="userEXP"
      style="width: 240px"
      placeholder="目前聯覺經驗"
      clearable
    />
    <br />
    目標聯覺等級
    <el-input
      v-model="tarLVL"
      style="width: 240px"
      placeholder="目標聯覺等級"
      clearable
    />
    <br />
    <el-button type="primary" @click="btnCalculate">開始計算</el-button>

    <!-- <RouterView /> -->

    <el-card style="max-width: 480px">
      <p>還差{{ lvlsToTarget }}等達到目標等級</p>
      <p>還差多少經驗到目標等級: {{ expsToTarget }}經驗值</p>
      <p>還差幾天可以達到目標等級: {{ daysToTarget }}天</p>
    </el-card>
  </div>
</template>

<style>
body {
  margin: 0;
}
</style>

<style scoped>
body {
  margin: 10px;
}
.container {
  padding: 100px;
}
</style>
