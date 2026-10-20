# A股分析系统 - 前端

A股每日自动分析引擎前端，基于 Vue 3 微前端架构。

## 技术栈

- **Vue 3**（Composition API）
- **Vite 8**（构建工具）
- **Element Plus**（UI 组件库）
- **TypeScript**（类型安全）
- **Axios**（HTTP 请求）
- **ECharts**（K 线图 / 归一化对比图）

## 功能特性

| 模块 | 说明 |
|------|------|
| 市场概览 | 4 张统计卡片（最新交易日、当日平均涨跌幅、均线趋势分布、20日年化波动率均值）+ 指数表格（Sparkline 走势、当日/20日/60日涨幅、MA20 乖离、趋势标签，支持搜索与排序） |
| 指数详情 | 抽屉式详情，ECharts 蜡烛图（A股红涨绿跌）+ MA5/MA20/MA60 + 成交量副图，区间可切 60/120/250/全部；附技术信号解读（均线排列、短期/中期强弱、MA20 乖离、20日/60日动量、20日波动） |
| 指数对比 | 多指数区间归一化对比（基准 100）+ 区间涨幅排行（区间涨幅、最高、最低、振幅） |
| 每日报告 | 一键生成 Markdown 报告，支持复制与下载 .md |
| 数据覆盖 | 展示各指数回填区间、交易日数与完整性进度条 |
| 主题 | 深色金融风为主，可切换浅色并 localStorage 持久化；涨跌配色遵循 A 股红涨绿跌 |
| 响应式 | ≥1200px 三列 / 768–1200px 两列 / <768px 单列 |

## 快速启动

### 前置条件

- Node.js 20+
- 后端服务已启动（Gateway 8080）

### 安装依赖

```bash
npm install
```

### 开发模式

```bash
npm run dev
```

访问 http://localhost:5174

### 构建

```bash
npm run build
```

产物输出到 `dist/` 目录。

## 项目配置

### 代理配置

开发环境下，Vite 代理自动将 `/api` 请求转发到后端 Gateway：

```typescript
// vite.config.ts
server: {
  port: 5174,
  proxy: {
    '/api': {
      target: 'http://localhost:8080',
      changeOrigin: true
    }
  }
}
```

生产环境需要配置 Nginx 或直接将前端打包到 Gateway 静态资源目录。

## 项目结构

```
stock-analyzer-frontend/
├── public/                    # 静态资源
├── src/
│   ├── api/index.ts           # 接口封装（axios 实例 + 类型定义）
│   ├── components/            # 业务组件
│   │   ├── AppHeader.vue      # 顶部栏（数据日期 / 刷新 / 主题切换）
│   │   ├── OverviewCards.vue  # 市场概览统计卡片
│   │   ├── IndexTable.vue     # 指数表格
│   │   ├── Sparkline.vue      # 迷你走势图
│   │   ├── IndexDetailDrawer.vue
│   │   ├── KlineChart.vue     # ECharts 蜡烛图
│   │   ├── ComparePanel.vue   # 多指数归一化对比
│   │   ├── ReportPanel.vue    # 每日报告
│   │   └── CoveragePanel.vue  # 数据覆盖度
│   ├── composables/           # useOverview / useKline
│   ├── styles/                # theme.css / element-override.css
│   ├── utils/format.ts        # 数值、日期、涨跌方向格式化
│   ├── App.vue                # 布局骨架 + Tab 导航
│   └── main.ts                # 入口文件
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

## API 对接

| 接口 | 说明 |
|------|------|
| `GET /api/quote/index/list` | 获取指数列表 |
| `GET /api/quote/index/{code}/recent` | 最近 N 个交易日日线 |
| `GET /api/quote/index/{code}/range` | 指定区间日线（K 线数据源） |
| `GET /api/quote/coverage` | 数据覆盖度 |
| `GET /api/analysis/index/{code}/summary` | 单指数分析摘要 |
| `GET /api/analysis/index/summaries` | 批量指数分析摘要 |
| `GET /api/report/latest` | 获取最新报告 |
| `POST /api/report/daily` | 生成当日报告 |

## 与后端联调

确保后端服务已启动：

| 服务 | 端口 | 状态检查 |
|------|------|----------|
| Nacos | 8848 | http://localhost:8848/nacos |
| Gateway | 8080 | http://localhost:8080/api/quote/index/list |

## 风险提示

本项目仅供学习和技术研究，不构成任何投资建议。股市有风险，投资需谨慎。
