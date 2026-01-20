---
name: cloudsis-components
description: Cloudsis 项目前端开发规范（Vue 3 + TypeScript + Element Plus + 麦杰公司 Framework 5.x），适用于 sis 脚手架项目的组件与编码实践。
paths:
  - "**/*.css"
  - "**/*.js"
  - "**/*.jsx"
  - "**/*.less"
  - "**/*.md"
  - "**/*.scss"
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.vue"
  - "**/package.json"
  - "**/vite.config.*"
version: 5.2.x
---
# Cloudsis项目前端开发规范

> 参考来源: Vue 官方风格指南、Element Plus 最佳实践
> 主要作用：基于 Vue 3 + TypeScript + Element Plus + 麦杰公司框架的技术栈，配合 `create-maguscloud-app` 的 sis 脚手架进行 cloudsis 项目开发。
---
## 适用范围

- 适用项目：使用 `create-maguscloud-app` 的 sis 脚手架、集成麦杰公司 Framework 5.x 的 cloudsis 项目。
- 主要文件类型：`.vue` / `.ts` / `.tsx` / `.js` / `.css` / `.scss` / `.less` / `package.json` / `vite.config.*`。

## 🚀 项目初始化

### 使用 create-maguscloud-app 脚手架

```bash
# 使用 npx 创建 cloudsis 项目
npx create-maguscloud-app@latest my-cloudsis-app --template sis

# 或使用特定版本
npx create-maguscloud-app@1.1.6 my-cloudsis-app --template sis

# 进入项目目录
cd my-cloudsis-app

# 安装依赖
pnpm i
```

### CloudSis依赖自定义插件一览

| 名称                        | 备注                      |
| ------------------------- | ----------------------- |
| @magustek/framework-core  | framework基础包            |
| @magustek/framework-ui    | framework组件包            |
| @magustek/framework-utils | framework方法包            |
| @magustek/icon-svg        | framework图标包            |
| @magustek/framework-types | framework typescript类型包 |
| @cloudsis/cloudsis-plus   | 基于framwork的sis项目自定义封装组件 |




## 📁 项目结构 (sis 脚手架)

```
src/
├── api/                 # API 请求
├── components/          # 通用组件
├── common
│   ├── authCode         # 授权编码对照表
│   ├── charts           # echarts Json
│   ├── dicts            # 系统字典对照表
│   ├── pinia            # Pinia stores
│   └── websocket        # 实时请求
├── lang                 # 国际化
│   ├── index.ts
│   └── locale           # 国际化配置文件
├── composables/         # 组合式函数
├── router/              # 路由配置
├── utils/               # 自定义工具函数
├── types/               # TypeScript 类型
├── utils/               # 工具函数
├── mock/                # 本地测试数据
├── views/               # 页面组件
├── App.vue
└── main.ts
```

>[!tip] 提示
>项目中使用的@代表的是src文件夹，在vite.config.ts有配置
    resolve: {
      alias: {
        '@': resolve('./src'),
      },
    },
## UI 风格约束

### 严格禁止（常见 AI 风格）

- ❌ 蓝紫色霓虹渐变、发光描边、玻璃拟态
- ❌ 大面积渐变、过多装饰性几何图形
- ❌ 赛博风、暗黑科技风、AI 风格 UI
- ❌ UI 文案中使用 emoji

### 后台系统（默认风格）

| 要素   | 要求                 |
| ---- | ------------------ |
| 主题   | 使用组件库默认主题          |
| 配色   | #30429B 颜色为主       |
| 动效   | 克制，仅保留必要交互反馈       |
| 组件样式 | 使用tailwind统一class名 |

## 技术栈

| 层级   | Vue（首选）                               |
| ---- | ------------------------------------- |
| 框架   | Vue 3 + TypeScript                    |
| 构建   | Vite                                  |
| 路由   | Vue Router 4                          |
| 状态   | Pinia                                 |
| UI 库 | Element Plus + @magustek/framework-ui |
| 时间库  | Day.js                                |
| 请求库  | Axios， @magustek/framework-core       |
| 方法库  | @magustek/framework-utils + lodash-es |
| 包管理器 | pnpm                                  |
| 样式   | tailwind css                          |


---

## Vue 编码规范

### 组件基础

```vue
<template>
  <div class="user-card">
    <h3>{{ displayName }}</h3>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { User } from '@/types'

// Props & Emits
const props = defineProps<{ userId: number }>()
const emit = defineEmits<{ (e: 'update', value: string): void }>()

// 响应式状态
const loading = ref(false)
const user = ref<User | null>(null)

// 计算属性
const displayName = computed(() => user.value?.name ?? '未知用户')

// 生命周期
onMounted(async () => { await fetchUser() })

// 方法
async function fetchUser() {
  loading.value = true
  try {
    user.value = await api.getUser(props.userId)
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.user-card { padding: 16px; }
</style>
```

### 命名约定
| 类型          | 约定             | 示例                    |
| ----------- | -------------- | --------------------- |
| 组件文件        | PascalCase.vue | `CloudsisTable.vue`   |
| Composables | useXxx.ts      | `useCloudsisData.ts`  |
| Store       | useXxxStore.ts | `useCloudsisStore.ts` |
| API 模块      | xxx.api.ts     | `cloudsis.api.ts`     |
| 类型定义        | xxx.types.ts   | `cloudsis.types.ts`   |

---

## Vue  Router路由规范

```typescript
// src/router/index.ts
import { createRouter } from '@magustek/framework-core'
import { createMainRouter } from '@magustek/framework-wujie'

const routes = [
	{
		path: '/',
	    component: () => import('@/***/views/index.vue'),
	    name: 'SisCommonIndex',
	    children: []
	}
]


const router = createRouter({
  base: import.meta.env.BASE_URL,
  routes: routes,
})

const mainRouter = createMainRouter(router)

export { router, mainRouter }
```

```ts
// src/main.ts
import { router, mainRouter } from './router/index'
app.use(router).use(mainRouter)
```


>通过@magustek/framework-core创建路由，路由文件使用异步引入



----


## 状态管理（Pinia）

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  const token = ref<string>('')

  const isLoggedIn = computed(() => !!token.value)

  async function login(username: string, password: string) {
    const res = await api.login(username, password)
    token.value = res.token
    user.value = res.user
  }

  return { user, token, isLoggedIn, login }
})
```

---

## 交互状态处理

**必须处理的状态**: loading、empty、error、disabled、submitting

```vue
<template>
  <el-skeleton v-if="loading" :rows="5" animated />
  <el-result v-else-if="error" icon="error" :title="error">
    <template #extra>
      <el-button @click="fetchData">重试</el-button>
    </template>
  </el-result>
  <el-empty v-else-if="list.length === 0" description="暂无数据" />
  <template v-else>
    <!-- 正常内容 -->
  </template>
</template>
```

---

## TypeScript 规范

```typescript
// types/user.ts
export interface User {
  id: number
  username: string
  role: 'admin' | 'user'
}

export interface ApiResponse<T = unknown> {
  code: number
  message: string
  data: T
}
```

---

## 性能优化

| 场景  | 方案                   |
| --- | -------------------- |
| 大列表 | 虚拟滚动                 |
| 路由  | 懒加载 `() => import()` |
| 计算  | 使用 `computed` 缓存     |
| 大数据 | 使用 `shallowRef`      |

```typescript
// 路由懒加载
const routes = [
  { path: '/dashboard', component: () => import('@/views/Dashboard.vue') }
]

// 请求防抖
import { useDebounceFn } from '@vueuse/core'
const debouncedSearch = useDebounceFn((keyword) => api.search(keyword), 300)
```

---

## 🔍 代码质量检查

### ESLint 配置建议
```ts
//eslint.config.js
import stylistic from '@stylistic/eslint-plugin';
import parserTs from '@typescript-eslint/parser';
import vueParser from 'vue-eslint-parser';
import eslintVue from 'eslint-plugin-vue';
import eslintPluginPrettierRecommended from 'eslint-plugin-prettier/recommended';

export default [
  ...eslintVue.configs['flat/recommended'],
  eslintPluginPrettierRecommended,
  {
    plugins: {
      '@stylistic': stylistic,
    },
    files: ['**/*.ts', '**/*.tsx', '**/*.vue'],
    languageOptions: {
      parser: vueParser,
      parserOptions: {
        extraFileExtensions: ['.vue'],
        ecmaFeatures: {
          jsx: true,
          tsx: true,
        },
        ecmaVersion: 2020,
        useJSXTextNode: true,
        sourceType: 'module',
        parser: parserTs,
      },
    },
    ignores: [
      'build',
      'config/*.js',
      'test/*.js',
      ' static/dist',
      'router',
      ' *.cjs',
      'auto-imports.d.ts',
      'components.d.ts',
      'docs/**/*',
    ],
    rules: {
      'prettier/prettier': ['error', {}, { usePrettierrc: true }],
    },
  },
];
```

### Prettier 配置
```js
// prettier.config.cjs
module.exports = {
  singleQuote: true,
  semi: true,
  trailingComma: 'all',
  printWidth: 100,
  tabWidth: 2,
  proseWrap: 'never',
  endOfLine: 'auto',
  // 使用空格缩进行,而不是制表符
  useTabs: false,
  // 更改引用对象属性的时间 可选值"<as-needed|consistent|preserve>"
  quoteProps: 'as-needed',
  // 在JSX中使用单引号而不是双引号
  jsxSingleQuote: false,
  // 在对象文字中的括号之间打印空格
  bracketSpacing: true,
  // 在单独的箭头函数参数周围包括括号 always：(x) => x \ avoid：x => x
  arrowParens: 'always',
  // 这两个选项可用于格式化以给定字符偏移量（分别包括和不包括）开始和结束的代码
  rangeStart: 0,
  rangeEnd: Infinity,
  // 指定要使用的解析器，不需要写文件开头的 @prettier
  requirePragma: false,
  // 不需要自动在文件开头插入 @prettier
  insertPragma: false,
  // 指定HTML文件的全局空格敏感度 css\strict\ignore
  htmlWhitespaceSensitivity: 'ignore',
  // Vue文件脚本和样式标签缩进
  vueIndentScriptAndStyle: false,
  overrides: [
    {
      files: '*.ejs',
      options: {
        parser: 'html',
      },
    },
    {
      files: '*.json',
      options: {
        parser: 'json',
      },
    },
  ],
};
```

## 📚 相关文档

### reference/（按需加载）

- [Framework-Core 文档](./reference/Framework-Core.md)
- [Framework-UI 文档](./reference/Framework-ui.md)
- [Framework-Utils 文档](./reference/Framework-utils.md)
- [CloudSIS 插件库](./reference/CloudSIS-Plus.md)

### assets/（案例与模板）

- [API 接口定义案例](./assets/api-examples.md)
- [按钮权限配置案例](./assets/auth-examples.md)
- [组件使用案例](./assets/components-examples.md)
- [字典配置案例](./assets/dict-examples.md)
- [国际化配置案例](./assets/i18n-examples.md)
- [路由配置案例](./assets/router-examples.md)
- [类型配置案例](./assets/types-exampls.md)
- [自定义Hooks使用案例](./assets/hooks-examples.md)
- [TypeScript 标准](./assets/typescript-standards.md)

### 外部文档

- [Vue 3 官方文档](https://vuejs.org/)
- [Element Plus 文档](https://element-plus.org/)
- [TypeScript 文档](https://www.typescriptlang.org/)

---

> 📋 本 Skill 遵循：`cloudsis-components` - 基于麦杰公司 Framework 5.x 的 cloudsis 项目开发规范
>
> **适用场景**：
> - 使用 `create-maguscloud-app` 的 sis 脚手架初始化项目
> - 集成麦杰公司框架进行 cloudsis 项目开发
> - 遵循统一的编码规范和最佳实践
