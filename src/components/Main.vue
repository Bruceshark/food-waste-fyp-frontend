<script setup>
import { onMounted, reactive, ref } from "vue";
import OneDayInfo from "@/components/OneDayInfo.vue";
import MultiDayInfo from "@/components/MultiDayInfo.vue";

import stomach from "@/assets/stomach.svg";
import lessrice from "@/assets/less_rice.svg";
import chat from "@/assets/chat.svg";
import takeaway from "@/assets/takeaway.svg";
// import mockList from "@/assets/summary_list.json";

import { get } from "@/request";

const dailyData = reactive({});
const monthlyData = reactive([]);
const isLoaded = ref(false);

const currPage = ref(0);
const currURL = window.location.search;

const urlParams = new URLSearchParams(currURL);
const slideSpeed = urlParams.has("speed") ? Number(urlParams.get("speed")) : 10000

setInterval(() => {
  currPage.value = (currPage.value + 1) % 2;
}, slideSpeed);

onMounted(async () => {
  let currDate = {year: 2021, month: 10, day: 1}
  let currDateObj = new Date(currDate.year, currDate.month - 1, currDate.day)
  let dailyRes = await get("fyp_dashboard_food_waste_percentage", currDate);

  monthlyData.push(dailyRes)
  for (let i = 0; i < 30; i ++) {
    currDateObj = new Date(currDateObj.setDate(currDateObj.getDate()-1))
    let dailyRes = await get("fyp_dashboard_food_waste_percentage", {year: 1900 + currDateObj.getYear(), month: currDateObj.getMonth() + 1, day: currDateObj.getDate()});
    monthlyData.push(dailyRes)
  }
  
  console.log(monthlyData)
  dailyData.value = dailyRes;
  // let monthlyRes = await get("poster/getMonthly", null);
  // monthlyData.value = monthlyList;
  isLoaded.value = true;
});
</script>

<template>
  <div>
    <div class="bg" v-if="isLoaded">
      <div class="header">
        <span>How much FOOD we WASTED @ LG1?</span>
        <span>{{ dailyData.value.date }}</span>
      </div>
      <div class="main">
        <one-day-info
          class="main-content"
          v-if="currPage === 0"
          :dailyData="dailyData.value"
        />
        <multi-day-info
          class="main-content"
          v-if="currPage === 1"
          :monthlyData="monthlyData"
        />
      </div>
      <div class="footer">
        <div
          style="
            width: 100%;
            margin: auto;
            height: 0.5vh;
            background: #a81e2d;
            margin: 1vh 0;
          "
        />
        <div class="footer-title">
          What you can do to help reduce our campus???s food waste:
        </div>
        <div class="footer-content">
          <div class="advice">
            <stomach />
            <div>consider your appetite before ordering</div>
          </div>
          <div class="advice">
            <lessrice />
            <div>choose the ???less rice??? option</div>
          </div>
          <div class="advice">
            <chat />
            <div>kindly ask the staff to give you less food</div>
          </div>
          <div class="advice">
            <takeaway />
            <div>bring a lunch box to pack for excess food</div>
          </div>
        </div>
        <div class="footer-contact">
          <!-- <img src="@/assets/ust_logo.svg" style="width: 2vw; margin: 0 1vw"/> -->
          <div>Food waste SSC project | contact us: xxx@ust.hk</div>
        </div>
      </div>
    </div>
    <div v-else>Food Waste FYP. loading resources... {{(monthlyData.length / 30 * 100).toFixed(0)}}%</div>
  </div>
</template>

<style scoped>
.bg {
  background: #f8e6ce;
}
.header {
  display: flex;
  justify-content: space-between;
  height: 15vh;
  line-height: 15vh;
  font-size: 8vh;
  padding: 0 2vw;
  /* box-sizing: border-box; */
  background: #a81e2d;
  color: white;
  font-family: "Neue-bold";
}
.main {
  box-sizing: border-box;
  height: 55vh;
  padding: 1vw 2vw;
}
.footer {
  height: 30vh;
  box-sizing: border-box;
  padding: 0 2vw;
}
.footer-title {
  color: #a81e2d;
  font-size: 1.5vw;
  font-family: "Neue-bold";
  margin-top: 1.5vh;
}
.footer-content {
  display: flex;
  justify-content: space-between;
  font-family: "Neue-regular";
}
.advice {
  text-align: center;
  font-size: 1.3vw;
}
.advice svg {
  height: 8vh;
  margin: 1vh;
}
.footer-contact {
  font-size: 1.3vw;
  /* line-height: 1vw; */
  font-family: "Neue-regular";
  position: absolute;
  bottom: 0;
  right: 2vw;
}
@keyframes fadeInLeft {
  from {
    opacity: 0;
    /* transform: translate3d(-100%, 0, 0); */
  }

  to {
    opacity: 1;
    transform: none;
  }
}
.main-content {
  animation-duration: 1s;
  animation-fill-mode: both;
  animation-name: fadeInLeft;
}
</style>
