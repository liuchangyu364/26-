<template>
  <div class="weather-container">
    <!-- 这里放主标题，只出现一次 -->
    <h2>昆明市天气智能预测系统</h2>

    <div class="tab-buttons">
      <button @click="showTab = 'history'" :class="{ active: showTab === 'history' }">
        历史天气
      </button>
      <button @click="showTab = 'now'" :class="{ active: showTab === 'now' }">
        当前天气
      </button>
      <button @click="showTab = 'future'" :class="{ active: showTab === 'future' }">
        未来天气
      </button>
    </div>

    <div class="content-wrap">
      <CurrentWeather v-if="showTab === 'now'" />
      <FutureWeather v-else-if="showTab === 'future'" />
      <HistoryWeather v-else />
    </div>
  </div>
</template>

<script>
import HistoryWeather from './HistoryWeather.vue';
import CurrentWeather from './CurrentWeather.vue';
import FutureWeather from './FutureWeather.vue';

export default {
  name: 'HelloWorld',
  components: {
    HistoryWeather,
    CurrentWeather,
    FutureWeather
  },
  data() {
    return {
      showTab: 'now'
    };
  }
};
</script>

<style scoped>
.weather-container {
  width: 100%;
  min-height: 100vh;
  background: #f5f7fa;
  padding: 30px 20px;
  box-sizing: border-box;
}
h2 {
  text-align: center;
  color: #333;
  margin: 0 0 25px 0;
  font-weight: 600;
}
.tab-buttons {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 30px;
}
.tab-buttons button {
  padding: 10px 28px;
  border: none;
  border-radius: 6px;
  background: #409eff;
  color: #fff;
  font-size: 15px;
  cursor: pointer;
  transition: all 0.2s;
}
.tab-buttons button.active {
  background: #286cbb;
}
.tab-buttons button:hover {
  opacity: 0.9;
}
.content-wrap {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}
</style>