# 2) PocketBase 表结构（可直接照着创建）

## users
使用 PocketBase 内置 users collection。

## assets（主表）
- `name` text required
- `category` select required (`computer`, `phone`, `server`, `network`, `other`)
- `brand` text
- `model` text
- `serial_no` text
- `purchase_date` date
- `purchase_price` number
- `currency` text (default `CNY`)
- `status` select required (`active`, `spare`, `repair`, `sold`, `scrapped`)
- `location` text
- `warranty_until` date
- `note` editor
- `images` file (multiple)
- `owner` relation -> `users` required

建议索引：
- `owner, status`
- `owner, category`
- `owner, purchase_date`

权限规则（建议）：
- List/View/Create/Update/Delete: `@request.auth.id != "" && owner = @request.auth.id`

## asset_param_defs（参数定义模板）
- `category` select required (`computer`, `phone`, `server`, `network`, `other`)
- `key` text required
- `label` text required
- `value_type` select required (`text`, `number`, `boolean`, `select`, `json`)
- `unit` text
- `options_json` json
- `group_name` text
- `sort_order` number
- `is_default_collapsed` bool (default true)
- `is_active` bool (default true)
- `owner` relation -> `users` required

唯一约束建议：
- `owner + category + key`

权限规则（建议）：
- `@request.auth.id != "" && owner = @request.auth.id`

## asset_param_values（参数值）
- `asset` relation -> `assets` required
- `param_def` relation -> `asset_param_defs` required
- `value_text` text
- `value_number` number
- `value_bool` bool
- `value_json` json
- `owner` relation -> `users` required

唯一约束建议：
- `asset + param_def`

权限规则（建议）：
- `@request.auth.id != "" && owner = @request.auth.id`

## asset_logs（可选）
- `asset` relation -> `assets` required
- `action` select (`create`, `update`, `transfer`, `repair`, `sell`)
- `content` text
- `operator` relation -> `users`
- `owner` relation -> `users` required
- `created` auto

---

## 计算字段（前端实时算）
- `days_owned = max(1, today - purchase_date + 1)`
- `daily_cost = purchase_price / days_owned`
