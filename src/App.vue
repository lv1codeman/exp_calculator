<script setup>
import { ref, reactive } from 'vue'
import { onMounted, onBeforeUnmount } from 'vue'
const userLVL = ref(1)
const userEXP = ref(0)
const tarLVL = ref(60)
const res = ref(0)
const showres = ref(false)
const showres2 = ref(false)
const showres3 = ref(false)

const daysToTarget = ref('')
const expsToTarget = ref('')
const lvlsToTarget = ref('')

const goldnum = ref(0)
const purplenum = ref(0)
const bluenum = ref(0)

const goldnum3 = ref(0)
const purplenum3 = ref(0)
const bluenum3 = ref(0)
const greennum3 = ref(0)
const bossitemnum3 = ref(0)
const moneynum3 = ref(0)

const jsonData = ref(null)
const jsonData2 = ref(null)
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

  let to_next = jsonData.value[user_level - 1].nextlvl - user_exp
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
  bluenum.value = Math.floor(form2.green / 3) + form2.blue
  purplenum.value = Math.floor(bluenum.value / 3) + form2.purple
  goldnum.value = Math.floor(purplenum.value / 3) + form2.gold
  showres2.value = true
}

const btnClear2 = () => {
  form2.gold = 0
  form2.purple = 0
  form2.blue = 0
  form2.green = 0
  showres2.value = false
}

const btnCalculate3 = () => {
  const lvlgap = form3.tarlvl - form3.curlvl
  goldnum3.value = 0
  purplenum3.value = 0
  bluenum3.value = 0
  greennum3.value = 0
  bossitemnum3.value = 0
  moneynum3.value = 0
  for (let i = form3.curlvl - 1; i < form3.tarlvl - 1; i++) {
    goldnum3.value += jsonData2.value[i].gold
    purplenum3.value += jsonData2.value[i].purple
    bluenum3.value += jsonData2.value[i].blue
    greennum3.value += jsonData2.value[i].green
    bossitemnum3.value += jsonData2.value[i].boss_item
    moneynum3.value += jsonData2.value[i].money
  }

  goldnum3.value = goldnum3.value * form3.skillnum
  purplenum3.value = purplenum3.value * form3.skillnum
  bluenum3.value = bluenum3.value * form3.skillnum
  greennum3.value = greennum3.value * form3.skillnum
  bossitemnum3.value = bossitemnum3.value * form3.skillnum
  moneynum3.value = moneynum3.value * form3.skillnum

  console.log(jsonData2.value[form3.curlvl - 1].money)
  console.log('所需金色材料: ' + goldnum3.value)
  console.log('所需紫色材料: ' + purplenum3.value)
  console.log('所需藍色材料: ' + bluenum3.value)
  console.log('所需綠色材料: ' + greennum3.value)
  console.log('所需王物: ' + bossitemnum3.value)
  console.log('所需貝幣: ' + moneynum3.value)
  console.log(lvlgap)

  showres3.value = true
}

const btnClear3 = () => {
  form3.curlvl = 1
  form3.tarlvl = 2
  form3.skillnum = 0
  showres3.value = false
}

onMounted(async () => {
  updateExpMarks() // 初始化時檢查寬度

  // 設置螢幕尺寸變化監聽器
  window.addEventListener('resize', updateExpMarks)
  try {
    const response = await fetch(`${import.meta.env.BASE_URL}/nextlevel.json`)
    jsonData.value = await response.json()

    const response2 = await fetch(`${import.meta.env.BASE_URL}/skilltable.json`)
    jsonData2.value = await response2.json()
  } catch (error) {
    console.error('Error fetching JSON:', error)
  }
})

// 在組件卸載前移除監聽器
onBeforeUnmount(() => {
  window.removeEventListener('resize', updateExpMarks)
})
// 自用數據
// const form = reactive({
//   green: 188,
//   blue: 155,
//   purple: 44,
//   gold: 3,
// })
const form2 = reactive({
  green: 0,
  blue: 0,
  purple: 0,
  gold: 0,
})
const form3 = reactive({
  curlvl: 1,
  tarlvl: 2,
  skillnum: 1,
})
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
      <el-tab-pane label="材料換算(武器、角色技能)">
        <li>計算以目前的材料資源，最多可產出幾個各階級材料</li>
        <li>材料資源指升級武器、角色技能的材料</li>
        <li>
          結果顯示<span class="blue">藍色材料</span>100個的話，代表我<span
            class="green"
            >綠色</span
          >全換成<span class="blue">藍色</span>可以有100個
        </li>
        <li>
          結果顯示<span class="purple">紫色材料</span>100個的話，代表我<span
            class="green"
            >綠色</span
          >全換<span class="blue">藍色</span>再全換成<span class="purple"
            >紫色</span
          >可以有100個，以此類推。
        </li>
        <li>貼心提醒:檢查一下有沒有物資箱</li>
        <hr />
        <div class="row">
          <el-form :model="form2" label-width="auto" style="max-width: 600px">
            <el-form-item>
              <template #label>
                <span class="labeltext gold">金色材料</span></template
              >
              <el-input-number v-model="form2.gold" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label>
                <span class="labeltext purple">紫色材料</span></template
              >
              <el-input-number v-model="form2.purple" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label
                ><span class="labeltext blue">藍色材料</span></template
              >
              <el-input-number v-model="form2.blue" :min="0" validate-event />
            </el-form-item>
            <el-form-item>
              <template #label
                ><span class="labeltext green">綠色材料</span></template
              >
              <el-input-number v-model="form2.green" :min="0" validate-event />
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
                <span class="resText">{{ form2.green }}</span
                >個
              </p>
            </div>
          </el-card>
        </div>
      </el-tab-pane>
      <el-tab-pane label="技能升級材料計算機">
        <li>例：有2個LV5技能想要都升到8級，就輸入 5 8 2</li>
        <hr />
        <div class="row">
          <el-form :model="form3" label-width="auto" style="max-width: 600px">
            <el-form-item>
              <template #label>
                <span class="labeltext white">當前等級</span></template
              >
              <el-input-number
                v-model="form3.curlvl"
                :min="1"
                :max="9"
                validate-event
              />
            </el-form-item>
            <el-form-item>
              <template #label
                ><span class="labeltext white">目標等級</span></template
              >
              <el-input-number
                v-model="form3.tarlvl"
                :min="1"
                :max="10"
                validate-event
              />
            </el-form-item>
            <el-form-item>
              <template #label>
                <span class="labeltext white">技能數</span></template
              >
              <el-input-number
                v-model="form3.skillnum"
                :min="1"
                validate-event
              />
            </el-form-item>
          </el-form>
          <el-button type="primary" @click="btnCalculate3">開始計算</el-button>
          <el-button type="warning" @click="btnClear3">清空</el-button>
        </div>
        <div class="row">
          <el-card v-show="showres3" style="max-width: 480px">
            <div>
              達成目標所需的材料<br />
              <p>
                <span class="labeltext2 gold">金色材料</span>
                <span class="resText">{{ goldnum3 }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 purple">紫色材料</span>
                <span class="resText">{{ purplenum3 }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 blue">藍色材料</span>
                <span class="resText">{{ bluenum3 }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 green">綠色材料</span>
                <span class="resText">{{ greennum3 }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 white">王物</span>
                <span class="resText">{{ bossitemnum3 }}</span
                >個
              </p>
              <p>
                <span class="labeltext2 white">貝幣</span>
                <span class="resText">{{ moneynum3 }}</span>
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
  padding: 50px 10px;
}

:deep(.el-tabs) {
  padding: 0px;
}

:deep(.el-tabs--border-card > .el-tabs__content) {
  padding: 25px;
}

.row {
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
  background-color: #bfc0c4;
}
/* 節點顏色 */
:deep(.el-slider__stop.el-slider__marks-stop) {
  background-color: #ececec;
}

.resText {
  color: rgb(197, 56, 0);
  /* line-height: 32px; */
  font-size: 20px;
  font-weight: bold;
}

.white {
  color: white;
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
  .container {
    padding: 20px 10px;
  }
  :deep(.el-tabs) {
    min-width: 800px;
  }
}
</style>
