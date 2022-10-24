<script setup>
import { ref } from "vue";
import { onMounted } from "vue";

const playerMove = ref(325); // 玩家移動
const player = ref(null); // 玩家
const playerBottom = ref(50);
const playerPos = ref({});

const timeCounter = ref(playerFalling);
const playerFall = ref(0);
const fall = ref(setInterval(timeCounter.value, 30));

const create = ref(null);
const stairCreate = ref(null);
const stair = ref([]);
const stairTop = ref(280);

const scroll = ref(null);
const bgPos = ref(0);

const bgTop = ref(0);
const bgBottom = ref(800);

const bgBlock = ref(null);
const img = ref("");
const fail = ref(null);

// 玩家落下設定
function playerFalling() {
  playerFall.value += 10;
  playerBottom.value += 10;
  fallingDetect();
  gameOver();
}

// 玩家落下條件偵測
function fallingDetect() {
  playerPos.value.bottom = playerBottom.value;
  playerPos.value.left = playerMove.value;
  playerPos.value.right = playerMove.value + 50;

  for (let i = 0; i < stair.value.length; i++) {
    if (
      playerPos.value.bottom === stair.value[i].top &&
      playerPos.value.left < stair.value[i].right &&
      playerPos.value.right > stair.value[i].left
    ) {
      window.clearInterval(fall.value);
      fall.value = "No Counter";
      return;
    } else if (
      playerPos.value.bottom < stair.value[i].top &&
      fall.value === "No Counter"
    ) {
      fall.value = setInterval(timeCounter.value, 30);
      return;
    }
  }
}
// 左右移動
function playerMoving(e) {
  // console.log(e);

  // 左鍵
  if (e.keyCode === 37 && playerMove.value > 0) {
    playerMove.value -= 10;
    fallingDetect();
  }

  // 右鍵
  if (e.keyCode === 39 && playerMove.value < 650) {
    playerMove.value += 10;
    fallingDetect();
  }
}

// 創造樓梯
function stairCreating() {
  if (800 + bgBottom.value > stairTop.value) {
    let div = document.createElement("div"); //做出階梯
    div.classList.add("stair");

    // 如果沒有上一個階梯(失敗品，但可以參考)
    // if (stair.value.length === 0) {
    //   stairBottom.value = 70;
    // } else {
    // stairBottom.value = stair.value[stair.value.length - 1].offsetTop;
    // console.log(stair.value[stair.value.length - 1].style.transform);
    // }

    stairTop.value += 70;

    const divXPos = Math.floor(Math.random() * 600);
    div.style.transform = `translate(${divXPos}px,${stairTop.value}px)`;

    const divPos = {};
    divPos.top = stairTop.value + 50 + stair.value.length * 20;
    divPos.left = divXPos;
    divPos.right = divXPos + 100;

    // 做好的階梯放進畫面
    stairCreate.value.appendChild(div);

    // 丟進陣列
    stair.value.push(divPos);
  }
}

// 畫面捲動
function bgScrolling() {
  bgTop.value += 10;
  bgBottom.value += 10;
  bgPos.value -= 10;
  stairCreate.value.style.height = `${bgBottom.value}px`;
  stairCreate.value.style.transform = `translate(0px,${bgPos.value}px)`;
  gameOver();
}

// 失敗偵測
function gameOver() {
  if (
    bgTop.value >= playerPos.value.bottom - 50 ||
    bgBottom.value <= playerPos.value.bottom
  ) {
    window.clearInterval(create.value);
    window.clearInterval(scroll.value);
    window.clearInterval(fall.value);
    window.removeEventListener("keydown", playerMoving);
    // img.value.src = "/src/assets/PlayerFail.jpg"; // 開發時用
    img.value.src = "/down-stairs/assets/PlayerFail.jpg"; // Build時用
    fail.value.style.display = "flex";
    //   alert("Game Over");
  }
}

// 重新開始(之後再研究)
// function reStart() {
//   playerMove.value = 325;
//   playerBottom.value = 50;
//   playerPos.value = {};
//   playerFall.value = 0;
//   stair.value = [];
//   stairTop.value = 0;
//   bgPos.value = 0;
//   bgBottom.value = 800;
//   img.value.src = "/src/assets/Player.jpg";
//   fail.value.style.display = "none";
//   // stairCreate.value.childNodes.forEach((element) => {
//   //   if (element.className === "stair") {
//   //     stairCreate.value.removeChild(element);
//   //   }
//   // });
//   // stairCreate.value.childNodes.forEach((element) => {
//   //   if (element.className === "stair") {
//   //     stairCreate.value.removeChild(element);
//   //   }
//   // });
//   console.log(stairCreate.value.childNodes);
//   console.log(stairCreate.value.nodeList);

//   // create.value = setInterval(stairCreating, 100);
//   // scroll.value = setInterval(bgScrolling, 100);
//   // fall.value = setInterval(timeCounter.value, 30);
//   // window.addEventListener("keydown", playerMoving);
// }

function reStart() {
  window.location.reload();
}

// 按下按鈕呼叫玩家移動的函式
window.addEventListener("keydown", playerMoving);

onMounted(() => {
  // 生成樓梯
  create.value = setInterval(stairCreating, 100);

  // 畫面捲動
  scroll.value = setInterval(bgScrolling, 100);
});
</script>

<template>
  <div class="bgBlock" ref="bgBlock">
    <div class="bg" ref="stairCreate">
      <div
        class="player"
        ref="player"
        :style="`transform: translate(${playerMove}px,${playerFall}px)`"
      >
        <!-- 開發時用 -->
        <!-- <img src="../assets/Player.jpg" ref="img" /> -->
        <!-- Build時用 -->
        <img src="/down-stairs/assets/Player.jpg" ref="img" />
      </div>
    </div>
    <div class="fail" ref="fail">
      <div>Game Over</div>
      <div @click="reStart">Restart</div>
    </div>
  </div>
</template>

<style scoped>
.bgBlock {
  width: 700px;
  height: 800px;
  overflow: hidden;
  position: relative;
}
.bg {
  width: 700px;
  height: 800px;
  background-color: black;
}

.player {
  width: 50px;
  height: 50px;
}

img {
  width: 100%;
  height: 100%;
}
</style>
