# 资产记录 App 技术路线（定制版）

> 技术栈：**Next.js + Tailwind + PocketBase + Docker Compose + PWA**

## 1) 目标确认

你的核心需求是：

- 设备数量多（电脑、手机、服务器、网络设备等）。
- 既要有统一字段（型号、价格、日期），又要有类型差异化字段（CPU/内存/摄像头等）。
- 详情字段希望是**二级信息，默认折叠**，主列表保持简洁。
- 想要自动计算：
  - **陪伴天数**（已持有多少天）
  - **日均成本**（已花费金额 ÷ 陪伴天数）

这个需求非常适合先用 PocketBase 做 MVP，后续再平滑升级。

---

## 2) 架构与部署

```text
iPhone Safari / 桌面浏览器
        |
      HTTPS
        |
      Nginx
   /api  |   /
PocketBase   Next.js(PWA)
   |            |
SQLite       SSR + 静态资源
```

服务：

- `web`: Next.js（App Router）+ Tailwind + PWA
- `pb`: PocketBase（数据库 + 鉴权 + 后台）
- `nginx`: 统一入口、HTTPS、反向代理

部署：Docker Compose 即可，PocketBase 数据目录用 volume 持久化。

---

## 3) 数据库设计（重点：通用 + 可自定义参数）

为了兼顾“通用字段”和“设备类型细节”，建议用 **主表 + 参数模板 + 参数值** 模式。

### 3.1 主资产表 `assets`

通用字段（列表和检索主要依赖这些）：

- `name`：资产名称（如 MacBook Pro 14）
- `category`：类型（computer / phone / server / network / other）
- `brand`：品牌
- `model`：型号（主列表重点展示）
- `serial_no`：序列号
- `purchase_date`：购入日期
- `purchase_price`：购入价格
- `currency`：币种（默认 CNY）
- `status`：active / spare / repair / sold / scrapped
- `location`：位置
- `warranty_until`：保修日期
- `owner`：关联用户
- `note`：备注
- `images`：附件图片

> 说明：主列表只显示 `name / model / status / purchase_price / purchase_date / 陪伴天数 / 日均成本`。

### 3.2 参数定义表 `asset_param_defs`（参数模板）

用于定义“某类设备可有哪些扩展参数”：

- `category`：归属类型（computer/phone/...）
- `key`：参数键（如 `cpu`, `ram`, `camera_main`）
- `label`：展示名（如 处理器、内存、主摄）
- `value_type`：text / number / boolean / select / json
- `unit`：单位（GB、MHz、MP，可空）
- `options_json`：select 的可选项（JSON，可空）
- `group_name`：分组名（如“性能参数”“影像参数”）
- `sort_order`：排序
- `is_default_collapsed`：是否默认折叠（true）
- `is_active`：是否启用

> 你后续可在后台新增模板参数，不用改代码就能扩展字段。

### 3.3 参数值表 `asset_param_values`（每台设备的具体参数值）

- `asset`：关联 `assets`
- `param_def`：关联 `asset_param_defs`
- `value_text`
- `value_number`
- `value_bool`
- `value_json`

> 根据 `value_type` 只填对应字段。前端读取时统一渲染成“二级参数面板”。

### 3.4 可选日志表 `asset_logs`

保留变更历史：创建、维修、转移、出售等。

---

## 4) “二级参数默认折叠”交互方案

### 列表页（简洁）

每个卡片只显示：

- 名称 + 型号
- 状态
- 购入价
- 陪伴天数
- 日均成本

### 详情页（分层）

- 第一层：核心信息（不折叠）
- 第二层：**详细参数（折叠面板 Accordion，默认收起）**
  - 分组展示（如性能、显示、影像、网络）

这样你日常看起来清爽，需要时再展开细节。

---

## 5) 两个核心计算字段

> 建议前端实时计算（无需落库），也可后续做后端冗余字段。

### 5.1 陪伴天数 `days_owned`

公式：

`days_owned = max(1, 今日日期 - purchase_date + 1)`

说明：

- 当天购入也算第 1 天，避免出现 0。
- 若 `purchase_date` 为空，则返回 `null` 或显示 `-`。

### 5.2 日均成本 `daily_cost`

公式：

`daily_cost = purchase_price / days_owned`

说明：

- 结果保留 2 位小数。
- 若价格或日期缺失，则显示 `-`。
- 可加币种前缀（如 `¥12.30/天`）。

---

## 6) 第一版页面清单（按你需求重排）

1. 登录页
2. 资产列表页（移动优先）
   - 搜索/筛选
   - 显示陪伴天数、日均成本
3. 新增/编辑资产页
   - 先填通用字段
   - 再填可折叠的二级参数
4. 资产详情页
   - 主信息 + 折叠参数 + 图片
5. 参数模板管理页（给你自己配置“电脑参数/手机参数”）

---

## 7) 迁移路线（避免被锁死）

- 当前：PocketBase（快）
- 后续：导出数据到 Postgres，后端切 NestJS/FastAPI（前端页面结构基本不变）
- 参数模型已采用“定义 + 值”设计，迁移时最稳

---

## 8) 下一步我可以直接给你的内容

如果你确认这个定制版设计，我下一条可以直接给你：

1. **项目目录结构**（Next.js App Router + 分层目录）
2. **PocketBase collection 详细字段清单**（可直接照着建）
3. **docker-compose.yml + nginx.conf**（可部署版）
4. **第一版页面原型清单**（移动端优先，含折叠参数交互）
