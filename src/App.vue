<script setup>
import { ref, reactive } from 'vue'
import { onMounted } from 'vue'
const userLVL = ref(1)
const userEXP = ref(0)
const tarLVL = ref(60)
const res = ref('aaa')
const showres = ref(false)

const daysToTarget = ref('')
const expsToTarget = ref('')
const lvlsToTarget = ref('')

const jsonData = ref(null)
// var jdata = null

// const value = ref([30, 60])
const marks = reactive({
  1: '1',
  10: '10',
  20: '20',
  30: '30',
  40: '40',
  50: '50',
  // 60: '60',
  70: '70',
  79: '79',
  60: {
    style: {
      color: '#1989FA',
    },
    label: '60',
  },
})
const exp_marks = reactive({
  0: '0',
  10000: '10000',
  20000: '20000',
  30000: '30000',
  40000: '40000',
  47300: '47300',
})

const btnClear = () => {
  userLVL.value = 1
  userEXP.value = 0
  tarLVL.value = 60
  showres.value = false
}

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

  showres.value = true
}

// let jsonData = null
onMounted(async () => {
  try {
    // const response = await fetch('nextlevel.json')
    const response = await fetch(`${import.meta.env.BASE_URL}/nextlevel.json`)
    jsonData.value = await response.json()
  } catch (error) {
    console.error('Error fetching JSON:', error)
  }
})
</script>

<template>
  <div class="container">
    <li>
      索拉指南活耀度獎勵2000exp + 體力獎勵1800exp =
      3800exp(每日可穩定取得的聯覺經驗)
    </li>
    <li>
      <a
        href="https://www.wandoujia.com/apps/8334814/5413317407552284694.html"
        target="_blank"
        >聯覺經驗表來源</a
      >
    </li>
    <li>暫不支援手機端使用，有空再說</li>
    <div class="row">
      <div class="label">目前聯覺等級</div>
      <el-slider
        v-model="userLVL"
        :min="1"
        :max="79"
        show-input
        :marks="marks"
      />
    </div>
    <div class="row">
      <div class="label">目前聯覺經驗</div>
      <el-slider
        v-model="userEXP"
        :min="0"
        :max="47300"
        show-input
        :marks="exp_marks"
      />
    </div>
    <div class="row">
      <div class="label">目標聯覺等級</div>
      <el-slider
        v-model="tarLVL"
        :min="1"
        :max="79"
        show-input
        :marks="marks"
      />
    </div>
    <div class="row">
      <el-button type="primary" @click="btnCalculate">開始計算</el-button>
      <el-button type="warning" @click="btnClear">清空</el-button>
    </div>
    <div class="row">
      <el-card v-show="showres" style="max-width: 480px">
        <p>
          還差<span class="resText">{{ lvlsToTarget }}</span
          >等達到目標等級
        </p>
        <p>
          還差多少經驗到目標等級: <span class="resText">{{ expsToTarget }}</span
          >經驗值
        </p>
        <p>
          還差幾天可以達到目標等級:
          <span class="resText">{{ daysToTarget }}</span
          >天
        </p>
      </el-card>
    </div>
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
  padding: 50px 100px;
  /* background-color: rgb(233, 233, 233); */
}

.row {
  /* display: flex; */
  padding: 10px 0;
}
.row > .label {
  display: flex;
  align-items: center;
  margin-right: 5px;
}

.el-slider {
  width: 50%;
  padding-left: 10px;
}

/* 滑過的地方 */
:deep(.el-slider__bar) {
  /* background-color: rgb(0, 53, 83); */
}
/* 背景BAR顏色 */
:deep(.el-slider__runway.show-input) {
  /* background-color: rgb(61, 135, 179); */
  background-color: #bfc0c4;
}
/* 節點顏色 */
:deep(.el-slider__stop.el-slider__marks-stop) {
  /* background-color: rgb(0, 98, 155); */
  background-color: #ececec;
}

/* .row > .input {
  display: flex;
  align-items: center;
  margin-left: 5px;
} */

.resText {
  color: rgb(197, 56, 0);
}
</style>
