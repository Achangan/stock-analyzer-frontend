<template>
  <div id="app">
    <el-container>
      <el-header>
        <h1>A股指数分析系统</h1>
      </el-header>
      <el-main>
        <el-tabs v-model="activeTab">
          <el-tab-pane label="指数列表" name="list">
            <el-table :data="indexList" v-loading="loadingList">
              <el-table-column prop="tsCode" label="指数代码" />
              <el-table-column prop="latestDate" label="最新日期" />
              <el-table-column prop="latestClose" label="最新收盘" />
              <el-table-column prop="latestPctChg" label="涨跌幅">
                <template #default="scope">
                  <span :style="{ color: scope.row.latestPctChg >= 0 ? 'red' : 'green' }">
                    {{ scope.row.latestPctChg }}%
                  </span>
                </template>
              </el-table-column>
              <el-table-column prop="ma5" label="MA5" />
              <el-table-column prop="ma20" label="MA20" />
              <el-table-column prop="ma60" label="MA60" />
              <el-table-column label="操作">
                <template #default="scope">
                  <el-button size="small" @click="showDetail(scope.row.tsCode)">详情</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="分析报告" name="report">
            <el-button type="primary" @click="generateReport" :loading="generating">生成报告</el-button>
            <pre v-if="reportContent" style="margin-top: 20px; white-space: pre-wrap;">{{ reportContent }}</pre>
          </el-tab-pane>
        </el-tabs>
      </el-main>
    </el-container>

    <el-dialog v-model="detailVisible" :title="currentCode + ' 分析详情'" width="60%">
      <el-descriptions :column="2" border>
        <el-descriptions-item label="指数代码">{{ detail.tsCode }}</el-descriptions-item>
        <el-descriptions-item label="最新日期">{{ detail.latestDate }}</el-descriptions-item>
        <el-descriptions-item label="最新收盘">{{ detail.latestClose }}</el-descriptions-item>
        <el-descriptions-item label="涨跌幅">
          <span :style="{ color: detail.latestPctChg >= 0 ? 'red' : 'green' }">{{ detail.latestPctChg }}%</span>
        </el-descriptions-item>
        <el-descriptions-item label="MA5">{{ detail.ma5 }}</el-descriptions-item>
        <el-descriptions-item label="MA20">{{ detail.ma20 }}</el-descriptions-item>
        <el-descriptions-item label="MA60">{{ detail.ma60 }}</el-descriptions-item>
        <el-descriptions-item label="20日涨幅">{{ detail.change20d }}%</el-descriptions-item>
        <el-descriptions-item label="60日涨幅">{{ detail.change60d }}%</el-descriptions-item>
        <el-descriptions-item label="20日波动率">{{ detail.volatility20d }}%</el-descriptions-item>
      </el-descriptions>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const activeTab = ref('list')
const indexList = ref([])
const loadingList = ref(false)
const detailVisible = ref(false)
const currentCode = ref('')
const detail = ref({})
const reportContent = ref('')
const generating = ref(false)

const api = axios.create({
  baseURL: '/api',
  timeout: 10000
})

onMounted(() => {
  loadIndexList()
})

async function loadIndexList() {
  loadingList.value = true
  try {
    const res = await api.get('/quote/index/list')
    const codes = res.data
    const summaries = await Promise.all(
      codes.map(code => api.get(`/analysis/index/${code}/summary`).then(r => r.data).catch(() => null))
    )
    indexList.value = summaries.filter(s => s !== null)
  } catch (e) {
    console.error('加载指数列表失败', e)
  } finally {
    loadingList.value = false
  }
}

async function showDetail(code) {
  currentCode.value = code
  try {
    const res = await api.get(`/analysis/index/${code}/summary`)
    detail.value = res.data
    detailVisible.value = true
  } catch (e) {
    console.error('加载详情失败', e)
  }
}

async function generateReport() {
  generating.value = true
  try {
    const res = await api.post('/report/daily')
    reportContent.value = res.data.report
  } catch (e) {
    console.error('生成报告失败', e)
    reportContent.value = '生成报告失败：' + e.message
  } finally {
    generating.value = false
  }
}
</script>

<style>
#app {
  font-family: 'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;
}

.el-header {
  background-color: #409eff;
  color: white;
  display: flex;
  align-items: center;
}

.el-header h1 {
  margin: 0;
  font-size: 20px;
}
</style>
