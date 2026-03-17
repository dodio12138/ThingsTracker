# ThingsTracker

资产记录应用（iPhone 可用 PWA）：Next.js + Tailwind + PocketBase + Docker Compose。

## 现在已补齐的“可让 Agent 直接开工”文件

- `TECH_ROUTE.md`：整体技术路线与产品目标
- `docs/01-project-structure.md`：项目目录结构
- `docs/02-pocketbase-schema.md`：PocketBase 表结构（含可扩展参数模型）
- `docs/03-page-list.md`：第一版页面清单与交互说明
- `docker-compose.yml`：三服务编排（nginx/web/pocketbase）
- `nginx/nginx.conf`：反向代理配置（`/` 到 web，`/api/` 到 PB）
- `.env.example`：部署所需环境变量模板
- `web/Dockerfile`：Next.js 容器构建模板
- `pb/Dockerfile`：PocketBase 容器构建模板

## 启动（草案）

1. 复制环境变量：
   ```bash
   cp .env.example .env
   ```
2. 修改 `.env` 中域名与版本号。
3. 启动：
   ```bash
   docker compose up -d --build
   ```

> 注意：当前仓库还没有 Next.js 源码，本次是先把“工程蓝图 + 部署骨架”补齐，下一步即可让 agent 直接生成前端与 API 对接代码。
