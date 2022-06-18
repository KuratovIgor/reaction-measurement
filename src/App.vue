<template>
  <div class="main">
    <div>Измерение {{ currentMeas }} / 5</div>
    <button
      class="main__btn"
      :class="{
        'main__btn--green': isCanClick,
        'main__btn--yellow': isClicked,
      }"
      :disabled="isDisabled"
      @click="handleClick"
    >
      {{ result }} sec
    </button>
    <div v-for="result in results" :key="result">
      {{ result ? result : "" }}
    </div>
    <div>Результат: {{ result }}</div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, reactive, toRefs, watch } from "vue";

export default defineComponent({
  name: "App",

  setup() {
    const data = reactive({
      isDisabled: true,
      isCanClick: false,
      isClicked: false,
      reactionTime: 0.0,
      currentMeas: 1,
      results: [0],
      result: 0.0,
    });

    let beginTime: number;
    let endTime: number;

    const wait = (): void => {
      data.isDisabled = true;
      data.reactionTime = 0.0;
      data.isClicked = false;
      const disableTime = Math.random() * (11 - 1) + 1;

      setTimeout((): void => {
        data.isDisabled = false;
        data.isCanClick = true;
        beginTime = new Date().getTime();
      }, disableTime * 1000);
    };

    const handleClick = (): void => {
      data.isCanClick = false;
      data.isClicked = true;
      endTime = new Date().getTime();
      data.reactionTime = (endTime - beginTime) / 1000;
      data.results.push(data.reactionTime);

      if (data.currentMeas === 5) {
        for (const res of data.results) {
          data.result += res;
        }
        data.result /= 5;
      } else {
        data.currentMeas++;
        wait();
      }
    };

    watch(
      () => data.isCanClick,
      (newValue, oldValue) => {
        if (newValue == true) {
          window.addEventListener("keyup", handleClick);
        } else {
          window.removeEventListener("keyup", handleClick);
        }
      }
    );

    onMounted((): void => {
      wait();
    });

    return {
      ...toRefs(data),
      handleClick,
    };
  },
});
</script>

<style>
.main {
  height: 100vh;
  display: flex;
  flex-direction: column;
  font-size: 25px;
  align-items: center;
  justify-content: center;
}

.main__btn {
  width: 400px;
  height: 100px;
  border-radius: 50px;
  background-color: red;
  color: white;
  font-size: 30px;
}

.main__btn--green {
  background-color: lightgreen;
  color: black;
}

.main__btn--yellow {
  background-color: yellow;
  color: black;
}
</style>
