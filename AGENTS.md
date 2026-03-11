# AGENTS.md — CloudSIS 前端开发技能库

## 仓库概述

本仓库是 **AI 编码代理技能包**，不是可运行的前端项目。核心内容在 `skills/cloudsis-components/` 中，为使用 `create-maguscloud-app` (sis 脚手架) 的 CloudSIS 项目提供开发规范、参考文档和代码示例。

技能包的使用方式：将 `cloudsis-components` 复制到对应 AI 工具的 skills 目录下：
- VSCode Copilot → `.github/skills/`
- CodeBuddy → `.claude/skills/`
- Trae → `.trae/skills/`

## 仓库结构

```
skills/
└── cloudsis-components/
    ├── SKILL.md                    # 主规范文档（技术栈 + 编码规范 + 配置）
    ├── assets/                     # 案例与模板
    │   ├── api-examples.md         # API 接口定义案例
    │   ├── auth-examples.md        # 按钮权限配置案例
    │   ├── components-examples.md  # 组件使用案例（6 个典型页面模式）
    │   ├── dict-examples.md        # 字典配置案例
    │   ├── hooks-examples.md       # 自定义 Hooks 使用案例
    │   ├── i18n-examples.md        # 国际化配置案例
    │   ├── lint-config-examples.md # ESLint/Prettier 完整配置
    │   ├── router-examples.md      # 路由配置案例
    │   ├── types-exampls.md        # 类型定义案例
    │   └── typescript-standards.md  # TypeScript 编码标准
    └── references/                 # 框架 API 文档
        ├── Framework-Core.md       # @magustek/framework-core
        ├── Framework-ui.md         # @magustek/framework-ui
        ├── Framework-utils.md      # @magustek/framework-utils
        └── CloudSIS-Plus.md        # @cloudsis/cloudsis-plus
```

## 构建 / Lint / 测试命令

本仓库本身无构建命令（纯文档）。以下是目标 CloudSIS 项目的标准命令：

```bash
npx create-maguscloud-app@latest my-app --template sis   # 初始化项目
pnpm i            # 安装依赖（必须用 pnpm）
pnpm dev          # 开发
pnpm build        # 构建
pnpm lint         # Lint 检查（ESLint + Prettier）
pnpm lint:fix     # Lint 自动修复
```

> 目标项目无独立测试框架配置。如需要可自行集成 Vitest。

## Git 提交规范

提交信息**使用中文**，格式：`<type>(<scope>): <subject>`（Header 必填，Body/Footer 可选，每行 ≤72 字符）。
type 枚举：`feat` / `fix` / `docs` / `style` / `refactor` / `test` / `chore`，subject 用中文简述变更。

## 技术栈与自定义依赖

| 层级 | 技术 |
|------|------|
| 框架 | Vue 3 + TypeScript |
| 构建 | Vite |
| 路由 | Vue Router 4（通过 `@magustek/framework-core` 的 `createRouter` 创建） |
| 状态 | Pinia（Composition API 风格） |
| UI 库 | Element Plus + `@magustek/framework-ui`（MgLayout, MgSearch, MgTree 等） |
| 请求 | Axios（通过 `@magustek/framework-core` 的 `useAxios`） |
| 工具 | `@magustek/framework-utils` + lodash-es + Day.js |
| 样式 | Tailwind CSS |
| 包管理 | pnpm |
| SIS 封装 | `@cloudsis/cloudsis-plus`（组件 + Hooks） |
| 图标/类型 | `@magustek/icon-svg` / `@magustek/framework-types` |

## 代码风格指南

### Vue 组件

- **必须**使用 `<script setup lang="ts">`（Composition API）
- 组件文件名：**PascalCase.vue**（如 `CloudsisTable.vue`）
- 组件内必须声明 `defineOptions({ name: 'XxxPage' })`
- 模板顺序：`<template>` → `<script setup>` → `<style scoped>`
- script 内部顺序：imports → defineOptions → defineProps/defineEmits → hooks → 响应式状态 → 计算属性 → 生命周期 → 方法

### TypeScript

- **严格模式**（`strict: true`），**禁止 `any`**——用 `unknown` + 类型守卫替代
- 函数**必须声明显式返回类型**
- 类型导入使用 `import type { Xxx }`，值与类型导入分开写
- 接口命名不加前缀（`User` 而非 `IUser`）
- 善用工具类型：`Partial<T>`, `Pick<T, K>`, `Omit<T, K>`, `Record<K, V>`
- 处理 null/undefined 用可选链 `?.` 和空值合并 `??`

### 文件命名约定

| 类型 | 约定 | 示例 |
|------|------|------|
| 组件 | PascalCase.vue | `CloudsisTable.vue` |
| Composable | useXxx.ts | `useCloudsisData.ts` |
| Store | useXxxStore.ts | `useCloudsisStore.ts` |
| API | xxx.api.ts | `cloudsis.api.ts` |
| 类型 | xxx.type.ts | `cloudsis.type.ts` |
| 字典 | index.dicts.ts | 权限：`index.auth.ts` |

### 导入规范

- 路径别名 `@/` 指向 `src/`
- 类型统一从 `@/types/index.type.ts` 桶文件导出，API 统一从 `@/api/index.api.ts` 桶文件导出

```ts
import { ref, computed } from 'vue'
import type { User } from '@/types/index.type'
import { getUser } from '@/api/index.api'
```

### API 定义

使用 `useAxios()` 的 `get`/`post`/`request`，**必须**用泛型标注返回类型。DELETE/PUT 用 `request` 并指定 `method`：

```ts
import { useAxios } from '@magustek/framework-core'
const { get, request } = useAxios()
export const getUsers = (params: QueryType) => get<UserListResponse>(`${api}/users`, params)
export const deleteUser = (id: string) => request(`${api}/users/${id}`, { method: 'DELETE' })
```

### 交互状态处理

**每个页面必须处理 5 种状态**：loading / empty / error / disabled / submitting

### 常用 Hooks（`@cloudsis/cloudsis-plus/hooks`）

| Hook | 用途 |
|------|------|
| `useTablePaging` | 分页表格（查询、翻页、排序、删除） |
| `useForm` | 表单引用 + 验证 |
| `useDialog` | 弹窗状态管理 |
| `useTree` | 左侧树 + 节点选中 |
| `useMessageConfig` | 成功 / 警告提示 |
| `useCommon` | 表单回填、字典格式化、数值保留 |
| `useApi` | 文件下载 |
| `useSelected` | 表格多选 |
| `useDateConfig` | 时间快捷项和格式化 |
| `useFileCallback` | 文件导入回调 |

### ESLint + Prettier

- ESLint：`eslint-plugin-vue`(flat/recommended) + `@stylistic/eslint-plugin` + Prettier 集成
- Prettier：单引号、分号、尾逗号 all、100 字符宽度、2 空格缩进、`arrowParens: 'always'`

## UI 风格约束

- 主色：`#30429B`
- **禁止**：蓝紫霓虹渐变、发光描边、玻璃拟态、赛博/暗黑科技风、emoji
- 使用 Tailwind CSS class 统一样式，使用框架默认主题，动效克制

## 常见页面模式

1. **分页查询** — MgLayout + MgSearch + el-table + MgPaging + `useTablePaging`
2. **无分页查询** — MgLayout + MgSearch + el-table
3. **左侧树 + 列表** — MgLayout(aside+main) + MgTree + `useTree` + router-view
4. **新增/编辑** — MgAddTemplate + el-form + `useForm` + `useMessageConfig`
5. **弹窗表单** — MgDialog + el-form + `useDialog` + `useForm`
6. **Handsontable 报表** — MgLayout + `useHandsontable`

> 详细代码示例见 `skills/cloudsis-components/assets/components-examples.md`
