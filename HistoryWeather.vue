<template>
  <div class="page-card">
    <h3 class="page-title">昆明历史天气查询</h3>
    <p class="tip">点击日期卡片，查看下方详细气象信息</p>

    <div class="history-nav">
      <button @click="prevPage" :disabled="!canPrev">← 往前</button>
      <div class="history-list">
        <div 
          class="history-item" 
          v-for="(item, i) in showHistoryList" 
          :key="i"
          :class="{ active: selectedIndex === i }"
          @click="selectItem(i, item)"
        >
          <div class="date">{{ item.date }}</div>
          <div class="wea">{{ item.info }}</div>
          <div class="temp">{{ item.tempMin }}~{{ item.tempMax }}℃</div>
        </div>
      </div>
      <button @click="nextPage" :disabled="!canNext">往后 →</button>
    </div>

    <div class="detail-section" v-if="selectedItem">
      <h4 class="detail-title">{{ selectedItem.date }} 详细气象信息</h4>
      <div class="detail-grid">
        <div class="detail-item"><span class="label">天气状况：</span>{{ selectedItem.info }}</div>
        <div class="detail-item"><span class="label">温度范围：</span>{{ selectedItem.tempMin }}~{{ selectedItem.tempMax }}℃</div>
        <div class="detail-item"><span class="label">平均气温：</span>{{ ((+selectedItem.tempMin + +selectedItem.tempMax) / 2).toFixed(1) }}℃</div>
        <div class="detail-item"><span class="label">相对湿度：</span>{{ selectedItem.humidity }}%</div>
        <div class="detail-item"><span class="label">风向风速：</span>{{ selectedItem.wind }}</div>
        <div class="detail-item"><span class="label">气压：</span>{{ selectedItem.pressure }}hPa</div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HistoryWeather',
  data() {
    return {
      historyData: [],
      showHistoryList: [],
      currentPage: 0,
      pageSize: 5,
      selectedIndex: -1,
      selectedItem: null
    }
  },
  computed: {
    totalPages() {
      return Math.ceil(this.historyData.length / this.pageSize)
    },
    canPrev() {
      return this.currentPage > 0
    },
    canNext() {
      return this.currentPage < this.totalPages - 1
    }
  },
  mounted() {
    // 页面加载时，先调用补全接口，确保数据完整
    this.fillContinuousDays()
      .then(() => {
        // 补全完成后，再加载历史数据
        this.loadHistoryData()
      })
      .catch(err => {
        console.error('数据补全失败，继续加载历史数据', err)
        this.loadHistoryData()
      })
  },
  methods: {
    // 新增：调用后端补全接口，清理未来数据并补全缺失日期
    async fillContinuousDays() {
      await axios.get('http://127.0.0.1:8000/weather/fill-continuous/')
    },
    async loadHistoryData() {
      try {
        const res = await axios.get('http://127.0.0.1:8000/weather/history/')

        let list = res.data.data.map(item => ({
          date: item.date,
          info: item.weather_condition,
          tempMin: (item.temperature - 2).toFixed(0),
          tempMax: (item.temperature + 2).toFixed(0),
          humidity: item.humidity,
          wind: item.wind_speed + '级',
          pressure: item.pressure
        }))

        // 按日期从旧到新排序
        this.historyData = list.sort((a, b) => new Date(a.date) - new Date(b.date))
        this.currentPage = 0
        this.updateShowList()
      } catch (e) {
        console.error('加载失败', e)
      }
    },
    updateShowList() {
      const start = this.currentPage * this.pageSize
      const end = start + this.pageSize
      this.showHistoryList = this.historyData.slice(start, end)
    },
    prevPage() {
      this.currentPage--
      this.updateShowList()
    },
    nextPage() {
      this.currentPage++
      this.updateShowList()
    },
    selectItem(index, item) {
      this.selectedIndex = index
      this.selectedItem = item
    }
  }
}
</script>

<style scoped>
.page-card{background:#fff;border-radius:12px;padding:25px;box-shadow:0 2px 12px rgba(0,0,0,.08)}
.page-title{text-align:center;margin:0 0 10px;font-size:18px;color:#333}
.tip{text-align:center;color:#999;margin:0 0 20px;font-size:14px}
.history-nav{display:flex;align-items:center;justify-content:center;gap:15px;margin-bottom:25px}
.history-nav button{padding:8px 16px;border:none;border-radius:6px;background:#409eff;color:#fff;cursor:pointer}
.history-nav button:disabled{background:#ccc;cursor:not-allowed}
.history-list{display:flex;gap:12px;justify-content:center;flex-wrap:wrap}
.history-item{width:140px;padding:15px 10px;background:#f5f7fa;border-radius:10px;text-align:center;cursor:pointer;transition:all .2s}
.history-item:hover{background:#e6f2ff}
.history-item.active{background:#e6f2ff;border:1px solid #409eff}
.date{font-size:16px;font-weight:600;margin-bottom:4px}
.wea{font-size:14px;color:#666;margin-bottom:4px}
.temp{font-size:14px;color:#409eff}
.detail-section{background:#f5f7fa;padding:20px;border-radius:10px;margin-top:10px}
.detail-title{margin:0 0 15px;font-size:16px;color:#333;padding-bottom:8px;border-bottom:1px solid #eee}
.detail-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px}
.detail-item{font-size:14px;color:#555}
.label{font-weight:600;color:#333}
@media (max-width:768px){.detail-grid{grid-template-columns:repeat(2,1fr)}}
</style>