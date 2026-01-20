---
title: auth-examples
description: cloudsis项目按钮权限配置
---
## 项目结构

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


## 定义授权编码

```ts
// src/common/authCode/index.auth.ts

// 运行指标编辑权限
export const indicatorUpsertAuth = 'framework:rtaCenter:indicator:upsert'

// 运行指标删除权限
export const indicatorDeleteAuth = 'framework:rtaCenter:indicator:delete'
```

## 页面使用

```vue
<el-button v-auth="[indicatorUpsertAuth]" type="primary" @click="addRunIndicators">
          {{ $t('global.action.add') }}
        </el-button>
<script lang="ts" setup>
import { indicatorUpsertAuth, indicatorDeleteAuth } from '@/auth/index.auth'
</script>
```