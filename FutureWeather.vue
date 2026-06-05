<template>
  <div class="page-card">
    <h3 class="page-title">昆明未来天气预测</h3>
    <p class="tip">点击日期卡片，查看下方详细气象信息</p>

    <div class="history-list">
      <div 
        class="history-item" 
        v-for="(item, i) in futureData" 
        :key="i"
        :class="{ active: selectedFutureIndex === i }"
        @click="selectFutureItem(i, item)"
      >
        <div class="date">{{ item.date }}</div>
        <div class="wea">{{ item.info }}</div>
        <div class="temp">{{ item.tempMin }}~{{ item.tempMax }}℃</div>
      </div>
    </div>

    <div v-if="selectedFutureItem" class="detail-section">
      <h4 class="detail-title">{{ selectedFutureItem.date }} 详细气象信息</h4>
      <div class="detail-grid">
        <div class="detail-item"><span class="label">天气状况：</span>{{ selectedFutureItem.info }}</div>
        <div class="detail-item"><span class="label">温度范围：</span>{{ selectedFutureItem.tempMin }}~{{ selectedFutureItem.tempMax }}℃</div>
        <div class="detail-item"><span class="label">平均气温：</span>{{ ((+selectedFutureItem.tempMin + +selectedFutureItem.tempMax)/2).toFixed(1) }}℃</div>
        <div class="detail-item"><span class="label">相对湿度：</span>{{ selectedFutureItem.humidity }}%</div>
        <div class="detail-item"><span class="label">风向风速：</span>{{ selectedFutureItem.wind }}</div>
        <div class="detail-item"><span class="label">气压：</span>{{ selectedFutureItem.pressure }}hPa</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FutureWeather',
  data() {
    return {
      futureData: [],
      selectedFutureIndex: -1,
      selectedFutureItem: null
    };
  },
  mounted() {
    this.generateFutureDataFromTomorrowMidnight();
  },
  methods: {
    generateFutureDataFromTomorrowMidnight() {
      const today = new Date();
      const tomorrow = new Date(today.getFullYear(), today.getMonth(), today.getDate()+1, 0,0,0);
      const futureList = [];
      for (let i = 0; i < 3; i++) {
        const d = new Date(tomorrow);
        d.setDate(tomorrow.getDate() + i);
        const dateStr = d.toLocaleDateString('zh-CN',{year:'numeric',month:'2-digit',day:'2-digit'}).replace(/\//g,'-');
        const tMin = 14 + Math.floor(Math.random() * 6);
        const tMax = 24 + Math.floor(Math.random() * 7);
        const weather = ['晴','多云','阴'][Math.floor(Math.random()*3)];
        const hum = 45 + Math.floor(Math.random()*25);
        const wind = ['南风2级','西南风2级','东北风2级'][Math.floor(Math.random()*3)];
        const pres = 810 + Math.floor(Math.random()*6);
        futureList.push({
          date: dateStr,
          info: weather,
          tempMin: String(tMin),
          tempMax: String(tMax),
          humidity: String(hum),
          wind: wind,
          pressure: String(pres)
        });
      }
      this.futureData = futureList;
    },
    selectFutureItem(index, item) {
      this.selectedFutureIndex = index;
      this.selectedFutureItem = item;
    }
  }
};
</script>

<style scoped>
.page-card {
  background: #fff;
  border-radius: 12px;
  padding: 25px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.08);
}
.page-title {
  text-align: center;
  margin: 0 0 10px 0;
  font-size: 18px;
  color: #333;
}
.tip {
  text-align: center;
  color: #999;
  margin: 0 0 20px 0;
  font-size: 14px;
}
.history-list {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 25px;
}
.history-item {
  width: 140px;
  padding: 15px 10px;
  background: #f5f7fa;
  border-radius: 10px;
  text-align: center;
  cursor: pointer;
  transition: all 0.2s;
}
.history-item:hover {
  background: #e6f2ff;
}
.history-item.active {
  background: #e6f2ff;
  border: 1px solid #409eff;
}
.date {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 4px;
}
.wea {
  font-size: 14px;
  color: #666;
  margin-bottom: 4px;
}
.temp {
  font-size: 14px;
  color: #409eff;
}
.detail-section {
  background: #f5f7fa;
  padding: 20px;
  border-radius: 10px;
  margin-top: 10px;
}
.detail-title {
  margin: 0 0 15px 0;
  font-size: 16px;
  color: #333;
  padding-bottom: 8px;
  border-bottom: 1px solid #eee;
}
.detail-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
}
.detail-item {
  font-size: 14px;
  color: #555;
}
.label {
  font-weight: 600;
  color: #333;
}
@media (max-width: 768px) {
  .detail-grid {
    grid-template-columns: repeat(2,1fr);
  }
}
</style>