# 1) 项目目录结构（建议）

```text
ThingsTracker/
├─ TECH_ROUTE.md
├─ README.md
├─ .env.example
├─ docker-compose.yml
├─ nginx/
│  └─ nginx.conf
├─ pb/
│  ├─ Dockerfile
│  └─ pb_data/                 # volume 挂载目录（运行时）
├─ web/
│  ├─ Dockerfile
│  ├─ package.json             # 下一步由 agent 生成
│  ├─ next.config.mjs
│  ├─ tailwind.config.ts
│  ├─ postcss.config.js
│  ├─ public/
│  │  ├─ manifest.json
│  │  └─ icons/
│  └─ src/
│     ├─ app/
│     │  ├─ (auth)/login/page.tsx
│     │  ├─ assets/page.tsx
│     │  ├─ assets/new/page.tsx
│     │  ├─ assets/[id]/page.tsx
│     │  ├─ settings/params/page.tsx
│     │  └─ layout.tsx
│     ├─ components/
│     │  ├─ asset/AssetCard.tsx
│     │  ├─ asset/AssetForm.tsx
│     │  └─ ui/Accordion.tsx
│     ├─ lib/
│     │  ├─ pocketbase.ts
│     │  └─ metrics.ts          # days_owned / daily_cost
│     └─ types/
│        └─ asset.ts
└─ docs/
   ├─ 01-project-structure.md
   ├─ 02-pocketbase-schema.md
   └─ 03-page-list.md
```

说明：
- `web/src/lib/metrics.ts` 专门放“陪伴天数/日均成本”计算。
- 资产扩展参数统一由 `asset_param_defs` + `asset_param_values` 驱动。
