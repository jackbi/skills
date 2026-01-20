---
title: cloudsis项目字典使用规范
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


## 定义字典编码

``` ts
//src/common/index.dicts.ts

// 规章
export const guizhang = 'RTACENTER_RULE'
// 状态
export const zhuangtai = 'RTACENTER_PROCESS_STATUS'
```

## 字典使用

### 字典下拉组件

```vue
<template>
	<MgDict
		v-model="formData.entryCode"
		class="!w-full"
		:dict-code="guizhang"
		:placeholder="$t('equipment_center.table.guizhang')"
		clearable>
	</MgDict>
</template>


<script>
import { guizhang } from '@/rtaCenter/config/index.dicts'
</script>
```

### 表格通过字典编码渲染

```vue
 <el-table-column
	  prop="assessType"
	  :label="$t('equipment_center.table.kaoheduixiang')"
	  show-overflow-tooltip
	  min-width="80"
	  :formatter="$dictFormat('assessType', kaoheduixiang)"
></el-table-column>
        
<script>
import { kaoheduixiang } from '@/rtaCenter/config/index.dicts'
</script>
```


### js/ts中获取字典数据

```ts
import { useDicts } from '@magustek/framework-core'
import { kaoheduixiang } from '@/rtaCenter/config/index.dicts'

AssessTypeData.value = useDicts(kaoheduixiang)
```