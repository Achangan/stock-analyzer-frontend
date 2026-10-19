# A股分析系统 - 前端

A股每日自动分析引擎前端，基于 Vue 3 微前端架构。

## 技术栈

- **Vue 3**（Composition API）
- **Vite 8**（构建工具）
- **Element Plus**（UI 组件库）
- **TypeScript**（类型安全）
- **Axios**（HTTP 请求）

## 功能特性

- 指数列表展示（沪深300、中证500、上证指数、创业板指）
- 指数详情弹窗（最新收盘、涨跌幅、MA5/MA20/MA60、20日涨幅、60日涨幅、20日波动率）
- 每日分析报告生成（Markdown 格式）
- 响应式布局，适配不同屏幕尺寸

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

访问 http://localhost:5173（或 5174，如果端口被占用）

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
├── public/              # 静态资源
├── src/
│   ├── components/      # 公共组件
│   ├── App.vue          # 主页面（指数列表 + 详情弹窗 + 报告生成）
│   ├── main.ts          # 入口文件
│   └── style.css        # 全局样式
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

## API 对接

| 接口 | 说明 |
|------|------|
| `GET /api/quote/index/list` | 获取指数列表 |
| `GET /api/analysis/index/{code}/summary` | 获取指数分析详情 |
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
