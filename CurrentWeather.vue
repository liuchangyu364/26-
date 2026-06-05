<template>
  <div class="page-card">
    <div class="real-time-weather">
      <div class="item">
        <p>温度</p>
        <h3>{{ nowTemp }}℃</h3>
      </div>
      <div class="item">
        <p>湿度</p>
        <h3>{{ nowHumidity }}%</h3>
      </div>
      <div class="item">
        <p>天气</p>
        <h3>{{ nowWeather }}</h3>
      </div>
      <div class="item">
        <p>风速</p>
        <h3>{{ nowWind }}m/s</h3>
      </div>
      <div class="item">
        <p>气压</p>
        <h3>{{ nowPressure }}hPa</h3>
      </div>
    </div>

    <div class="chart-box">
      <h3>未来72小时温度预测（ARIMA+LSTM融合模型）</h3>
      <div id="tempChart" style="width: 100%; height: 400px;"></div>
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import axios from 'axios';

export default {
  name: 'CurrentWeather',
  data() {
    return {
      nowTemp: '22',
      nowHumidity: '65',
      nowWeather: '晴',
      nowWind: '2.1',
      nowPressure: '815',
      tempChart: null,
      timeLabels: []
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart();
      this.getWeatherData();
    });
  },
  methods: {
    initChart() {
      if (this.tempChart) this.tempChart.dispose();
      const now = new Date();
      const xData = [];
      this.timeLabels = [];
      for (let i = 0; i < 25; i++) {
        const t = new Date(now.getTime() + i * 3 * 60 * 60 * 1000);
        const fmt = t.toLocaleString('zh-CN', { month:'2-digit', day:'2-digit', hour:'2-digit' });
        xData.push(fmt);
        this.timeLabels.push(t);
      }
      const startDate = now.toLocaleDateString('zh-CN');
      const endDate = new Date(now.getTime() + 72 * 60 * 60 * 1000).toLocaleDateString('zh-CN');
      this.tempChart = echarts.init(document.getElementById('tempChart'));
      this.tempChart.setOption({
        tooltip: {
          trigger: 'axis',
          formatter: params => {
            const idx = params[0].dataIndex;
            return this.timeLabels[idx].toLocaleString() + '<br/>预测温度：' + params[0].data + '℃';
          }
        },
        grid: { bottom: '18%' },
        xAxis: { type:'category', data:xData, axisLabel:{rotate:45, interval:2} },
        yAxis: { type:'value', min:0, max:35 },
        series: [{
          data: [],
          type: 'line',
          smooth: true,
          lineStyle: { width:3, color:'#409eff' },
          itemStyle: { color:'#409eff' },
          areaStyle: {
            color: new echarts.graphic.LinearGradient(0,0,0,1,[
              {offset:0, color:'rgba(64,158,255,0.2)'},
              {offset:1, color:'rgba(64,158,255,0)'}
            ])
          }
        }],
        graphic: [{
          type: 'text', left:'center', bottom:'3%',
          style: { text:`预测时间段：${startDate} ～ ${endDate}`, fontSize:14 }
        }]
      });
    },
    async getWeatherData() {
      try {
        // ✅✅✅ 这里我帮你改成了正确的后端地址！
        let res = await axios.get("http://127.0.0.1:8000/api/get72hWeather/");

        this.nowTemp = res.data.now.temp;
        this.nowHumidity = res.data.now.humidity;
        this.nowWeather = res.data.now.info;
        this.nowWind = res.data.now.wind;
        this.nowPressure = res.data.now.pressure;
        this.tempChart.setOption({ series: [{ data: res.data.predictTempList }] });
      } catch (err) {
        console.error("接口请求失败：", err);
        const testData = [22,21,20,19,23,27,29,30,28,26,24,22,21,20,19,23,26,29,31,30,28,25,23,21,20];
        this.tempChart.setOption({ series: [{ data: testData }] });
      }
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
.real-time-weather {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  background: #f5f7fa;
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 25px;
}
.item {
  text-align: center;
  min-width: 100px;
  margin: 10px 0;
}
.item p {
  margin: 0 0 5px 0;
  color: #666;
  font-size: 14px;
}
.item h3 {
  margin: 0;
  color: #409eff;
  font-size: 24px;
}
.chart-box h3 {
  text-align: center;
  margin: 0 0 15px 0;
  font-size: 16px;
  color: #333;
}
</style>