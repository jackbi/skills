---
title: 国际化案例
description: cloudsis项目国际化配置方案
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

## 国际化配置

```ts
// src/lang/index.ts
import { createI18n, useLocale } from '@magustek/framework-core'
import { Locale } from '@cloudsis/cloudsis-plus'
import LocaleLanguage from './locale'
import { zhCn as bizUiCn, en as bizUiEn } from '@magustek/framework-biz-ui'
import { zhCn as mgUiCn, en as mgUiEn } from '@magustek/framework-ui'
import ElCn from 'element-plus/es/locale/lang/zh-cn'
import ElEn from 'element-plus/es/locale/lang/en'
function scanModelI18n() {
  const messages: Any = {
    cn: {
      ...Locale['cn'],
      ...LocaleLanguage.cn,
      ...ElCn,
      ...bizUiCn,
      ...mgUiCn,
    },
    en: {
      ...Locale['en'],
      ...LocaleLanguage.en,
      ...ElEn,
      ...bizUiEn,
      ...mgUiEn,
    },
  }
  return messages
}

const i18n = createI18n({ locale: useLocale().locale.value })

i18n.withGlobalLocale()

const { cn, en } = scanModelI18n()
i18n.mergeLocalMessages({ cn, en })

export default i18n
```

```ts
// src/lang/locale/index.ts
import cn from './cn'
import en from './en'

export default {
  zh_CN: cn,
  zh: cn,
  'zh-CN': cn,
  cn: cn,
  en: en,
}

```

```ts
// src/lang/locale/cn.ts
export default {
  equipment_center: {
    facilityInventory: {
      titles: '请先选择对应设备',
      jibenxinxi: '基本信息',
      yunyingzhibiaopeizhi: '运营指标配置',
      tourulvzhibiao: '投入率指标',
    }
  }
}
```

```ts
// src/lang/locale/en.ts
export default {
  equipment_center: {
    facilityInventory: {
      titles: 'Please select the corresponding device first',
      jibenxinxi: 'Basic information',
      yunyingzhibiaopeizhi: 'Operational indicator configuration',
      tourulvzhibiao: 'Input rate indicator',
    }
  }
}
```

## 入口文件引入

```ts
// src/main.ts
import i18n from './lang'

app.use(i18n)
```

## 组件使用

``` vue
<el-form-item required :label="$t('equipment_center.table.yue')">
	<el-date-picker
	  v-model="query.time"
	  value-format="YYYY-MM"
	  type="month"
	  :clearable="false"
	  :placeholder="$t('equipment_center.table.yue')"
	  class="!w-full"
	  @change="changeDate"
	/>
  </el-form-item>
```

## setup或者ts文件中使用

``` vue
<script lang="ts" setup>
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const formRuleData = ref({
  positionId: { required: true, message: t('equipment_center.table.kaohegangwei') },
  assessType: { required: true, message: t('equipment_center.table.kaoheduixiang') },
})
</script>
```

## 通过i18n Ally插件展示国际化信息配置

```json
// .vscode/settings.json
{
  "i18n-ally.extract.ignored": [
    "url('${\n      commonUtils.getImage(props.systemInfo.background) ||\n      import.meta.env.BASE_URL + 'image/loginBg.png'\n    }')"
  ],
  "i18n-ally.localesPaths": ["node_modules/@cloudsis/cloudsis-plus/locale", "src/lang/locale"],
  "i18n-ally.displayLanguage": "cn",
  "i18n-ally.sourceLanguage": "cn",
  "i18n-ally.enabledFrameworks": ["vue"],
  "i18n-ally.enabledParsers": ["ts", "js"],
  "i18n-ally.annotationInPlace": false,
  "i18n-ally.ignoredLocales": [],
  "i18n-ally.keystyle": "nested",
}
```