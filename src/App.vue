<script setup>
import { ref, reactive } from 'vue'
import { onMounted, onBeforeUnmount } from 'vue'
const userLVL = ref(1)
const userEXP = ref(0)
const tarLVL = ref(60)
const res = ref('aaa')
const showres = ref(false)
const showres2 = ref(false)

const daysToTarget = ref('')
const expsToTarget = ref('')
const lvlsToTarget = ref('')

const goldnum = ref(0)
const purplenum = ref(0)
const bluenum = ref(0)

const jsonData = ref(null)
// var jdata = null

// const value = ref([30, 60])
const marks_large = reactive({
  1: '1',
  10: '10',
  20: '20',
  30: '30',
  40: '40',
  50: '50',
  70: '70',
  79: '79',
  60: {
    style: {
      color: '#1989FA',
    },
    label: '60',
  },
})

const marks_small = reactive({
  1: '1',
  20: '20',
  40: '40',
  79: '79',
  60: {
    style: {
      color: '#1989FA',
    },
    label: '60',
  },
})
const exp_marks_large = reactive({
  0: '0',
  10000: '10000',
  20000: '20000',
  30000: '30000',
  40000: '40000',
  47300: '47300',
})

const exp_marks_small = reactive({
  0: '0',
  15000: '15000',
  30000: '30000',
  47300: '47300',
})

const marks = ref(marks_large) // 初始設置為桌面版
const exp_marks = ref(exp_marks_large) // 初始設置為桌面版

// 監控螢幕寬度變化，根據寬度切換 exp_marks
const updateExpMarks = () => {
  if (window.innerWidth < 768) {
    marks.value = marks_small
    exp_marks.value = exp_marks_small
  } else {
    marks.value = marks_large
    exp_marks.value = exp_marks_large
  }
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

const btnClear = () => {
  userLVL.value = 1
  userEXP.value = 0
  tarLVL.value = 60
  showres.value = false
}

const btnCalculate2 = () => {
  bluenum.value = Math.floor(form.green / 3) + form.blue
  purplenum.value = Math.floor(bluenum.value / 3) + form.purple
  goldnum.value = Math.floor(purplenum.value / 3) + form.gold
  showres2.value = true
}

const btnClear2 = () => {
  form.gold = 0
  form.purple = 0
  form.blue = 0
  form.green = 0
  showres2.value = false
}
// let jsonData = null
onMounted(async () => {
  updateExpMarks() // 初始化時檢查寬度

  // 設置螢幕尺寸變化監聽器
  window.addEventListener('resize', updateExpMarks)
  try {
    // const response = await fetch('nextlevel.json')
    const response = await fetch(`${import.meta.env.BASE_URL}/nextlevel.json`)
    jsonData.value = await response.json()
  } catch (error) {
    console.error('Error fetching JSON:', error)
  }
})

// 在組件卸載前移除監聽器
onBeforeUnmount(() => {
  window.removeEventListener('resize', updateExpMarks)
})

const form = reactive({
  green: 188,
  blue: 155,
  purple: 44,
  gold: 3,
})
// const form = reactive({
//   green: 0,
//   blue: 0,
//   purple: 0,
//   gold: 0,
// })
</script>

<template>
  <div class="container">
    <el-tabs type="border-card">
      <el-tab-pane label="聯覺經驗計算機">
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
        <li>手機端可使用</li>
        <hr />
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
              還差多少經驗到目標等級:
              <span class="resText">{{ expsToTarget }}</span
              >經驗值
            </p>
            <p>
              還差幾天可以達到目標等級:
              <span class="resText">{{ daysToTarget }}</span
              >天
            </p>
          </el-card>
        </div>
      </el-tab-pane>
      <el-tab-pane label="武器、角色技能材料換算">
        <li>計算以目前的材料資源，最多可產出幾個各階級材料</li>
        <li>材料資源指升級武器、角色技能的材料</li>
        <hr />
        <div class="row">
          <el-form :model="form" label-width="auto" style="max-width: 600px">
            <el-form-item>
              <template #label>
                <span class="labeltext gold">金色材料</span></template
              >
              <el-input-number v-model="form.gold" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label>
                <span class="labeltext purple">紫色材料</span></template
              >
              <el-input-number v-model="form.purple" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label
                ><span class="labeltext blue">藍色材料</span></template
              >
              <el-input-number v-model="form.blue" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label
                ><span class="labeltext green">綠色材料</span></template
              >
              <el-input-number v-model="form.green" :min="0" validate-event />
            </el-form-item>
          </el-form>
          <el-button type="primary" @click="btnCalculate2">開始計算</el-button>
          <el-button type="warning" @click="btnClear2">清空</el-button>
        </div>
        <div class="row">
          <el-card v-show="showres2" style="max-width: 480px">
            <div>
              每種材料最多可產出<br />
              <p>
                <span class="labeltext2 gold">金色材料</span>
                <span class="resText">{{ goldnum }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 purple">紫色材料</span>
                <span class="resText">{{ purplenum }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 blue">藍色材料</span>
                <span class="resText">{{ bluenum }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 green">綠色材料</span>
                <span class="resText">{{ form.green }}</span
                >個
              </p>
            </div>
          </el-card>
        </div>
      </el-tab-pane>
    </el-tabs>
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
  display: flex;
  justify-content: center;
  padding: 100px 10px;
}

:deep(.el-tabs) {
  padding: 0px;
}

:deep(.el-tabs--border-card > .el-tabs__content) {
  padding: 25px;
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
  width: 95%;
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
  /* line-height: 32px; */
  font-size: 20px;
  font-weight: bold;
}

.gold {
  color: #ffe65a;
  font-weight: bold;
}
.purple {
  color: #ca6dff;
  font-weight: bold;
}
.blue {
  color: #59b4d3;
  font-weight: bold;
}
.green {
  color: #5cc35e;
  font-weight: bold;
}

.mr-5 {
  margin-right: 5px;
}
.mr-10 {
  margin-right: 10px;
}
.labeltext {
  font-size: 20px;
  background-color: #232629;
  padding: 0 5px;
  border-radius: 5px;
}
.labeltext2 {
  font-size: 20px;
  font-weight: bold;
  background-color: #232629;
  padding: 4px 5px;
  border-radius: 5px;
  margin-right: 2px;
}
@media (min-width: 768px) {
  :deep(.el-tabs) {
    min-width: 800px;
  }
}
</style>
