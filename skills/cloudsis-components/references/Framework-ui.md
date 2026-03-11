---
title: framework-ui | 帮助中心
url: https://doc.magustek.com/%E5%B7%A5%E4%B8%9A%E6%95%B0%E6%8D%AE%E7%AE%A1%E7%90%86%E5%B9%B3%E5%8F%B0/API%E5%B8%AE%E5%8A%A9/%E5%89%8D%E7%AB%AFAPI/framework-ui.html#mgtree
publishedTime: undefined
---

## framework-ui [​](#framework-ui)

基于 ElementPlus 封装的 vue3 组件库

## 安装 [​](#安装)

bash

```
pnpm add @magustek/framework-ui
```

## 使用 [​](#使用)

ts

```javascript
// main.ts
import MgUi from "@magustek/framework-ui";

import "@magustek/framework-ui/dist/style.css";

app.use(MgUi);
```

## useSvg [​](#usesvg)

一个方法，将 svg 字符串转化为一个 vue svg 组件

- 使用示例

vue

```typescript
<template>
  <MySvg></MySvg>
</template>

<script lang="ts" setup>
import { useSvg } from '@magustek/framework-ui'
import svg from './mysvg.svg?raw'

const MySvg = useSvg(svg)
</script>
```

## useMgIcon [​](#usemgicon)

一个方法，返回一个 MgIcon 组件

- 使用示例

| 参数   | 说明                        |
| ------ | --------------------------- | -------- | ------ | --------- |
| icon   | svg-xxx                     | font-xxx | string | Component |
| config | {color: string, size:string | number } |

vue

```typescript
<template>
	<el-button :icon="useMgIcon('icon-xxx')" />
	<el-button :icon="useMgIcon('svg-xxx')" />
	<el-button :icon="useMgIcon(Edit), { color:red, size: 24 }" />
</template>

<script lang="ts" setup>
import { useMgIcon } from '@magustek/framework-ui'
import { Edit } from @magustek/icon-svg
</script>
```

## MgIcon [​](#mgicon)

图标组件

- 属性

| 属性名 | 类型    | 说明      | 默认值               |
| ------ | ------- | --------- | -------------------- | --- |
| size   | number  | string    | 图标大小             | 16  |
| color  | string  | 图标颜色  |                      |
| name   | svg-xxx | font-xxx  | svg 图标或者字体图标 |     |
| icon   | string  | Component | icon 组件            |     |

- 插槽

| 插槽名  | 说明                                              | 默认值 |
| ------- | ------------------------------------------------- | ------ |
| default | 默认插槽，传递默认插槽时，name 和 icon 属性不生效 | 16     |

- 使用示例

vue

```html
<emplate>
  <MgIcon name="svg-edit" color="red"></MgIcon>
  <MgIcon :icon="Edit"></MgIcon>
  <MgIcon size="24">
    <Edit></Edit>
  </MgIcon>
</emplate>
```

## MgBackWrap 已弃用 [​](#mgbackwrap-已弃用)

返回页面容器, 参考 `framework-biz-ui` 的 `MgBackWrap` 组件

## MgCropper [​](#mgcropper)

图片裁切

- 属性

| 属性名            | 类型          | 说明         |
| ----------------- | ------------- | ------------ |
| source            | string        | 图片地址     |
| visible           | boolean       | 是否显示     |
| title             | string        | 标题         |
| config            | CropperConfig | 裁切配置     |
| cancelButtonText  | string        | 取消按钮文字 |
| confirmButtonText | string        | 确定按钮文字 |

- 事件

| 事件名  | 回调参数 | 说明                   |
| ------- | -------- | ---------------------- |
| confirm | (blob)   | 裁切后的图片二进制数据 |

- CropperConfig

ts

```
type CropperConfig = {
  outputSize?: number // 裁剪生成图片的质量 0.1 ~ 1
  outputType?: 'jpeg' | 'png' | 'webp' // 裁剪生成图片的格式 jpg (jpg 需要传入jpeg) jpeg, png, webp
  info?: boolean // 显示裁剪框的大小信息 true true | false
  canScale?: boolean // 图片是否允许滚轮缩放 true true | false
  autoCrop?: boolean // 是否默认生成截图框 false true | false
  autoCropWidth?: number // 默认生成截图框宽度 容器的 80% 0 ~ max
  autoCropHeight?: number // 默认生成截图框高度 容器的 80% 0 ~ max
  fixed?: boolean // 是否开启截图框宽高固定比例  false  true, false
  fixedNumber?: [number, number] // 截图框的宽高比例  [1, 1]  [ 宽度 , 高度 ]
  full?: boolean // 是否输出原图比例的截图  false  true, false
  fixedBox?: boolean // 固定截图框大小  不允许改变  false
  canMove?: boolean // 上传图片是否可以移动  true  true, false
  canMoveBox?: boolean // 截图框能否拖动  true  true, false
  original?: boolean // 上传图片按照原始比例渲染  false  true, false
  centerBox?: boolean // 截图框是否被限制在图片里面  false  true, false
  high?: boolean // 是否按照设备的dpr 输出等比例图片  true  true, false
  infoTrue?: boolean // true 为展示真实输出图片宽高 false 展示看到的截图框宽高  false  true, false
  maxImgSize?: number // 限制图片最大宽度和高度  2000  0 ~ max
  enlarge?: number // 图片根据截图框输出比例倍数  1  0 ~ max(建议不要太大，不然会卡死)
  mode?: 'contain' | 'cover' | '100%' | 'auto' // 图片默认渲染方式 contain  contain | cover | 100% | auto
}
```

- 使用示例

vue

```typescript
<template>
  <MgCropper :source="source" :visible="visible" :config="config" @confirm="onConfirm"></MgCropper>
</template>

<script lang="ts" setup>
const source = ref('')
const visible = ref(false)
const config = ref({ })

const onConfirm = (blob) => {
  console.log(blob)
}
</script>
```

- ### useCopper [​](#usecopper)

`MgCropper` 组件函数式用法

ts

```javascript
import { useCopper } from "@magustek/framework-ui";

const { crop } = useCropper();

crop(url, title, config).then((blob) => {
  conosole.log(blob);
});
```

## MgIconPicker [​](#mgiconpicker)

图标选择器

- 属性

| 属性名             | 类型   | 说明       |
| ------------------ | ------ | ---------- |
| modelValue/v-model | string | 选择的图标 |
| title              | string | 弹窗标题   |

- 插槽

| 插槽名  | 说明                         | 默认值 |
| ------- | ---------------------------- | ------ |
| default | 默认插槽，自定义默认展示效果 |        |

- 使用示例

vue

```typescript
<template>
  <MgIconPicker title="图标选择" v-model="icon"></MgIconPicker>
</template>

<script lang="ts" setup>
const icon = ref('')
</script>
```

## MgPaging [​](#mgpaging)

- 属性

所有 `el-pagination` 属性 + 扩展属性

扩展属性

| 属性名  | 类型    | 说明             | 默认值 |
| ------- | ------- | ---------------- | ------ |
| showTip | boolean | 是否显示分页信息 | true   |

- 事件

支持所有 `el-pagination` 事件，根据 element-plus [官方文档](https://element-plus.org/zh-CN/component/pagination.html#%E4%BA%8B%E4%BB%B6)，  
如果要监听 `current-page` 和 `page-size` 的改变, 建议使用 v-model, `size-change` 和 `current-change` 在以后的版本中将会被删除

| 事件名 | 回调参数                                | 说明                                 |
| ------ | --------------------------------------- | ------------------------------------ |
| change | (currentPage: number, pageSize: number) | currentPage 或者 pageSize 改变时触发 |

- 使用示例

vue

```typescript
<template>
  <MgPaging
    v-model:current-page="currentPage"
    v-model:page-size="pageSize"
    :show-tip="showTip"
    :total="total"
  />
</template>

<script lang="ts" setup>
const currentPage = ref(1)
const pageSize = ref(100)
const showTip = ref(true)
const total = ref(1000)
</script>
```

## MgTable [​](#mgtable)

表格组件 + 分页组件

- 属性

所有 `el-table` 属性 + `MgPaging` 属性

| 属性名                            | 类型   | 说明           | 默认值 |
| --------------------------------- | ------ | -------------- | ------ |
| page-size/v-model:page-size       | number | 分页器每页数量 | 20     |
| current-page/v-model:current-page | number | 分页器当前页数 | 1      |

[el-table 官方文档](https://element-plus.org/zh-CN/component/table.html)

- 扩展事件

| 事件名        | 回调参数                                | 说明           |
| ------------- | --------------------------------------- | -------------- |
| paging-change | (currentPage: number, pageSize: number) | 分页改变时触发 |

- 使用示例

vue

```typescript
<template>
  <mg-table
    ref="mgTableRef"
    :data="tableData"
    :total="1000"
    v-model:page-size="pageSize"
    v-model:current-page="currentPage"
    @paging-change="onPagingChange"
  >
  </mg-table>
</template>

<script lang="ts" setup>
const tableData = ref([])
const pageSize = ref(100)
const currentPage = ref(1)

const onPagingChange = (page: number, size: number) => {
  console.log('onPagingChange', page, size, currentPage.value, pageSize.value)
}
</script>
```

## MgToolbar [​](#mgtoolbar)

工具栏组件，默认自带 `新增` 和 `批量删除` 按钮

- 属性

| 属性名       | 类型   | 说明                                                | 默认值 |
| ------------ | ------ | --------------------------------------------------- | ------ |
| selectLength | number | 选中的条数，为 0 或者不传时会禁用默认的批量删除按钮 |        |

- 插槽

| 插槽名  | 说明                                                    | 默认值            |
| ------- | ------------------------------------------------------- | ----------------- |
| default | 默认插槽，传递默认插槽会覆盖默认的 `新增` 和 `批量删除` | `新增` `批量删除` |

- 事件

| 事件名       | 回调参数 | 说明           |
| ------------ | -------- | -------------- | -------------------------------- |
| event-handle | ('add'   | 'batchDelete') | 不传递默认插槽时，内置按钮的事件 |

- 使用示例

vue

```html
<template>
  <MgToolbar>
    <el-button>新增</el-button>
    <el-button>批量删除</el-button>
  </MgToolbar>
</template>
```

## MgCodeEditor [​](#mgcodeeditor)

代码编辑器

- 属性

| 属性名             | 类型    | 说明         | 默认值     |
| ------------------ | ------- | ------------ | ---------- | ---------- | ---------- | ------ | -------- | --- |
| modelValue/v-model | string  | 编辑器内容   |            |
| language           | 'css'   | 'javascript' | 'html'     | 'sql'      | 'json'     | 'java' | 语言模式 |     |
| theme              | 'vs'    | 'vs-dark'    | 'hc-black' | 'hc-light' | 编辑器主题 | 'vs'   |
| readOnly           | boolean | 只读默认     | false      |

- 方法

| 方法名           | 参数列表                                      | 说明               |
| ---------------- | --------------------------------------------- | ------------------ |
| getPosition      | ( )                                           | 获取光标位置       |
| setPosition      | (pos: { lineNumber: number, column: number }) | 设置光标位置       |
| setPositionValue | (value: string)                               | 在光标位置插入内容 |

## MgBpmnViewer [​](#mgbpmnviewer)

流程图查看器

- 属性

| 属性名           | 类型    | 说明               | 默认值         |
| ---------------- | ------- | ------------------ | -------------- | --- |
| content          | string  | 流程图 xml 内容    |                |
| showGrid         | boolean | 是否显示背景网格   | fasle          |
| activeNode       | string  | string\[\]         | 当前激活的节点 |     |
| activeNodeColor  | string  | 当前激活节点的颜色 | red            |
| successNode      | string  | string\[\]         | 已经完成的节点 |     |
| successNodeColor | string  | 已经完成节点的颜色 | #67c23a        |

- 使用示例

vue

```typescript
<template>
  <MgBpmnViewer :content="content"></MgBpmnViewer>
</template>

<script lang="ts" setup>
const content = ref('')
</script>
```

## MgCascader [​](#mgcascader)

级联组件, 基于 `el-cascader` 封装，支持以字符串形式双向绑定

- 属性 & 事件 & 插槽

同 `el-cascader`

## MgTimePicker [​](#mgtimepicker)

时间选择器，基于 `el-time-picker` 封装，时间范围支持以两个 v-mode 形式绑定 startTime 和 endTime

- 扩展属性

| 属性名                      | 类型   | 说明                   | 默认值 |
| --------------------------- | ------ | ---------------------- | ------ |
| startTime/v-model:startTime | string | 时间范围选择的开始时间 |        |
| endTime/v-model:endTime     | string | 时间范围选择的结束时间 |        |

- 使用示例

vue

```typescript
<template>
  <mg-time-picker
    is-range
    v-model="valueArray"
    v-model:startTime="startTime"
    v-model:endTime="endTime"
  ></mg-time-picker>
</template>

<script lang="ts" setup>
const valueArray = ref([])
const startTime = ref('')
const endTime = ref('')
</script>
```

## MgDatePicker [​](#mgdatepicker)

日期选择器，基于 `el-date-picker` 封装，日期范围支持以两个 v-mode 形式绑定 startTime 和 endTime

- 扩展属性

| 属性名                      | 类型   | 说明                   | 默认值 |
| --------------------------- | ------ | ---------------------- | ------ |
| startTime/v-model:startTime | string | 时间范围选择的开始时间 |        |
| endTime/v-model:endTime     | string | 时间范围选择的结束时间 |        |

- 使用示例

vue

```typescript
<template>
  <mg-date-picker
    type="daterange"
    v-model="valueArray"
    v-model:startTime="startTime"
    v-model:endTime="endTime"
  ></mg-da-picker>
</template>

<script lang="ts" setup>
const valueArray = ref([])
const startTime = ref('')
const endTime = ref('')
</script>
```

## MgTree [​](#mgtree)

基于 `el-tree` 封装的树组件

### 属性

| 属性名                | 说明                                                 | 类型                | 默认值   |
| --------------------- | ---------------------------------------------------- | ------------------- | -------- |
| data                  | 展示数据                                             | array               | —        |
| node-key              | 树节点唯一标识                                       | string              | `nodeId` |
| show-reload           | 是否需要刷新按钮，默认值为false                      | boolean             | false    |
| show-search           | 是否需要节点搜索，默认值为true                       | boolean             | true     |
| show-expand           | 是否需要节点收缩，默认值为true                       | boolean             | true     |
| icon                  | 自定义树节点图标组件                                 | String \| Component |          |
| icon-spin             | 节点伸缩时是否旋转，默认会顺时针旋转90°              | boolean             | true     |
| icon-list             | 自定义树节点图标                                     | array               |          |
| icon-class            | 自定义树节点图标                                     | Function(node)      | —        |
| icon-style            | 自定义树节点样式                                     | object              |          |
| ~~isNodeEdit~~        | 节点是否可编辑                                       | boolean             |          |
| show-add              | 节点是否需要新增                                     | Function \| boolean |          |
| show-edit             | 节点是否需要编辑                                     | boolean             |          |
| show-delete           | 节点是否需要删除                                     | boolean             |          |
| highlight             | 是否高亮选中节点，默认值为true                       | boolean             |          |
| highlight-current     | 是否高亮选中节点，默认值为false，与highlight任选其一 | boolean             |          |
| ~~expand-all~~        | 默认展开所有，建议替换为`defaultExpandAll`           | boolean             |          |
| default-expanded-all  | 默认展开所有                                         | boolean             |          |
| ~~expanded-keys~~     | 默认展开节点，建议替换为`defaultExpandedKeys`        |                     |          |
| default-expanded-keys | 默认展开节点                                         |                     |          |
| show-line             | 是否需要树形连接线，默认值为true                     | boolean             |          |
| show-label-line       | 树节点label是否需要连接线，默认值为false             | boolean             |          |

### 事件

| 事件名      | 说明 | 回调参数 |
| ----------- | ---- | -------- |
| node-click  |      |          |
| tree-reload |      |          |
| node-add    |      |          |
| node-delete |      |          |
| node-edit   |      |          |

### 插槽

| 事件名         | 说明 | 回调参数 |
| -------------- | ---- | -------- |
| `treeButton`   |      |          |
| `customButton` |      |          |
| `empty`        |      |          |
| `treeFooter`   |      |          |

## MgSplit [​](#mgsplit)

分割面板

- 属性

| 属性名      | 类型       | 说明         | 默认值           |
| ----------- | ---------- | ------------ | ---------------- | -------- |
| options     | object     | split 配置项 | {}               |
| direction   | horizontal | vertical     | 分割方向         | vertical |
| min-width   | string     | number       | 最小宽度         | 200px    |
| min-height  | string     | number       | 最小高度         | 200px    |
| gutter      | string     | number       | 间隙宽度或者高度 | 20px     |
| show-gutter | boolean    | 显示间隙     | true             |

- 事件

| 事件名      | 回调参数 | 说明 | 默认值     |
| ----------- | -------- | ---- | ---------- |
| onDrag      | ()       |      | () => void |
| onDragStart | ()       |      | () => void |
| onDragEnd   | ()       |      | () => void |

- 使用示例

vue

```html
<template>
  <MgSplit direction="horizontal" min-width="200px">
    <MgSplitPanel>
      <MgSplit direction="vertical" min-height="300px">
        <MgSplitPanel></MgSplitPanel>
        <MgSplitPanel></MgSplitPanel>
      </MgSplit>
    </MgSplitPanel>
    <MgSplitPanel></MgSplitPanel>
  </MgSplit>
</template>
```

## MgAddTemplate [​](#mgaddtemplate)

上中下布局模版

- 插槽

| 插槽名 | 说明         | 默认值 |
| ------ | ------------ | ------ |
| title  | 标题         |        |
| append | 标题附加内容 |        |
| main   | 主内容区     |        |
| footer | 底部区域     |        |

- 使用示例

vue

```html
<template>
  <MgAddTemplate>
    <template #title>title</template>
    <template #main>mian</template>
  </MgAddTemplate>
</template>
```

## MgDialog [​](#mgdialog)

弹窗组件，基于 `el-dialog` 封装

- 扩展属性

| 属性名 | 类型    | 说明                                | 默认值 |
| ------ | ------- | ----------------------------------- | ------ |
| normal | boolean | 以 top 值为基准，展示最大高度的弹窗 | false  |

## MgLayout [​](#mglayout)

基本布局容器

- 属性

| 属性名     | 类型   | 说明             | 默认值     |
| ---------- | ------ | ---------------- | ---------- | ----- |
| asideWidth | string | number           | 侧边栏宽度 | 300px |
| gap        | 间距   | 各板块之间的间隙 | 16px       |

- 插槽

| 插槽名 | 说明     | 默认值 |
| ------ | -------- | ------ |
| aside  | 侧边栏   |        |
| header | 头部     |        |
| main   | 主内容区 |        |
| footer | 底部区域 |        |

vue

```html
<template>
  <MgLayout>
    <template #aside> aside </template>
    <template #header> header </template>
    <template #main>
      <mg-layout :showAside="false">
        <template #header> header </template>
        <template #main>
          <mg-layout>
            <template #aside> aside </template>
            <template #main> main </template>
          </mg-layout>
        </template>
      </mg-layout>
    </template>
  </MgLayout>
</template>
```

## MgSearch [​](#mgsearch)

搜索栏组件

- 属性

| 属性名        | 类型    | 说明                   | 默认值   |
| ------------- | ------- | ---------------------- | -------- | --- |
| labelWidth    | string  | number                 | 标签宽度 | 100 |
| showMore      | boolean | 是否显示更多按钮       | false    |
| showButtons   | boolean | 是否显示默认搜索按钮   | true     |
| contentGrowth | boolean | 查询区内容宽度是否撑满 | false    |

- 插槽

| 插槽名  | 说明                                                         | 默认值      |
| ------- | ------------------------------------------------------------ | ----------- |
| search  | 查询区                                                       |             |
| buttons | 自定义按钮，传递该插槽会覆盖默认查询按钮                     | `查询` 按钮 |
| more    | 自定义更多查询，查询条件过多时，可以将其他查询条件放在此区域 |             |

- 事件

| 事件名 | 回调参数   | 说明                 | 默认值 |
| ------ | ---------- | -------------------- | ------ |
| search | (e: Event) | 默认查询按钮点击事件 |        |

- 使用示例

vue

```typescript
<template>
  <mg-search :showMore="true" @search="onSearch">
    <template #search>
      <el-row>
        <el-col :span="12">
          <el-form-item label="姓名">
            <el-input v-model="input" placeholder=""> </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="年龄">
            <el-input v-model="input" placeholder=""> </el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </template>
    <template #more>
      <el-row>
        <el-col :span="12">
          <el-form-item label="更多">
            <el-input v-model="input" placeholder=""> </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="更多">
            <el-input v-model="input" placeholder=""> </el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </template>
  </mg-search>
</template>

<script lang="ts" setup>
const onSearch = () => {
  console.log('查询')
}
</script>
```

## MgTabs [​](#mgtabs)

表格组件，基于 `el-tabs` 封装，支持原生的 el-tabs

- 扩展属性

| 属性名            | 类型    | 说明             | 默认值           |
| ----------------- | ------- | ---------------- | ---------------- | -------- | ---------- | ------ |
| headerHeight      | string  | number           | header 高度      | 40px     |
| tabPosition       | 'left'  | 'right'          | 'top'            | 'bottom' | 标签栏位置 | 'left' |
| tabItemPosition   | 'left'  | 'right'          | 标签项位置       | 'left'   |
| showActiveBar     | boolean | 显示激活项指示器 | false            |
| activeBarPosition | 'top'   | bottom           | 激活项指示器位置 | 'top'    |
| activeBarColor    | string  | 激活项指示器颜色 | #000             |
| activeItemBgColor | string  | 激活项背景颜色   | #fff             |
| headerBgColor     | string  | 标签栏头部颜色   | #f5f7fa          |
| gap               | string  | 间距             |                  |

- 扩展插槽

| 插槽名 | 说明                      | 默认值 |
| ------ | ------------------------- | ------ |
| header | 标签栏 header,除 tab 以外 |        |

- 使用示例

vue

```html
<template>
  <MgTabs
    header-height="58px"
    type=""
    tab-position="top"
    tab-item-position="right"
    :show-active-bar="true"
    active-bar-position="top"
    active-bar-color="#000"
    active-item-bg-color="#f6f6f6"
    header-bg-color="#fff"
    gap="16px"
  >
    <!-- 这里可以使用原生的 el-tab-pane，也可以使用 mg-tab-pane -->
    <el-tab-pane></el-tab-pane>
    <mg-tab-pane></mg-tab-pane>
  </MgTabs>
</template>
```

## MgTabButton [​](#mgtabbutton)

标签按钮组件

- 属性

| 属性名             | 类型   | 说明               | 默认值 |
| ------------------ | ------ | ------------------ | ------ |
| modelValue/v-model | string | 双向绑定激活标签项 |        |
| span               | number | 1-24，左栏所占比例 | 12     |
| defaultActive      | string | 默认激活标签的     |        |
| height             | string | 标签栏高度         | 60px   |

- 插槽

| 插槽名  | 说明         | 子标签                   |
| ------- | ------------ | ------------------------ |
| title   | 标题插槽     |                          |
| buttons | 标签按钮插槽 | 指定为 `MgTabButtonItem` |

- 事件

| 事件名   | 回调参数       | 说明           | 默认值 |
| -------- | -------------- | -------------- | ------ |
| tabClick | (name: string) | tab 项点击事件 |        |

## MgTabButtonItem [​](#mgtabbuttonitem)

`MgTabButton` 组件 `buttons` 插槽的子标签

- 属性

| 属性名 | 类型   | 说明                                                                                                     |
| ------ | ------ | -------------------------------------------------------------------------------------------------------- |
| title  | string | 标题                                                                                                     |
| name   | string | 标签唯一值，当 `MgTabButton` 没有传递 `modelValue` 和 `defaultActive` 时，则默认与当前路由的路径匹配激活 |

- 插槽

| 插槽名  | 说明                   | 子标签 |
| ------- | ---------------------- | ------ |
| default | 标题插槽, 自定义 title |        |

- 使用示例

vue

```typescript
<template>
  <MgTabButton :span="12" @tab-click="onTabClick">
    <template #buttons>
      <MgTabButtonItem title="1" name="/components/a"></MgTabButtonItem>
      <MgTabButtonItem name="/components/b">2</MgTabButtonItem>
      <MgTabButtonItem name="/components/c">3</MgTabButtonItem>
    </template>
  </MgTabButton>
</template>

<script lang="ts" setup>
const onTabClick = (name) => {
  console.log('onTabClick', name)
}
</script>
```

## MgFormulaEditor [​](#mgformulaeditor)

公式/条件编辑器

- 属性

| 属性名                     | 类型                             | 说明                   | 默认值 |
| -------------------------- | -------------------------------- | ---------------------- | ------ |
| modelValue/v-model         | object                           | 双向绑定表达式的 ast   |        |
| modelStr/v-model:model-str | string                           | 双向绑定表达式的字符串 |        |
| fieldOptions               | { label: string, value: string } | 自定义字段             |        |
| hideFunction               | boolean                          | 是否显示函数           | false  |
| hideField                  | boolean                          | 是否显示字段           | false  |

- 使用示例

vue

```typescript
<template>
  <MgFormulaEditor v-model="modelValue" v-model:model-str="modelStr">
  </MgFormulaEditor>
</template>

<script lang="ts" setup>
const modelValue = ref({})
const modelStr = ref('')
</script>
```

## MgEsign [​](#mgesign)

手写签名

- 属性

| 属性名         | 类型    | 说明             | 默认值    |
| -------------- | ------- | ---------------- | --------- | --- |
| width          | string  | number           | 画布宽度  | 800 |
| height         | string  | number           | 画布高度  | 300 |
| lineWidth      | number  | 线宽             | 4         |
| lineColor      | string  | 线条颜色         | #000000   |
| bgColor        | string  | 背景颜色         |           |
| isCrop         | boolean | 是否需要裁剪     | false     |
| isClearBgColor | boolean | 是否清除背景颜色 | true      |
| format         | string  | 输出图片格式     | image/png |
| quality        | number  | 图片质量 0.1 ~ 1 | 1         |

- 方法

| 方法名   | 参数                                                 | 说明           |
| -------- | ---------------------------------------------------- | -------------- |
| reset    | ()                                                   | 重置画布       |
| generate | ({format:string, quality: number }): Promise<string> | 生成base64图片 |

- 使用示例

vue

```typescript
<template>
  <MgEsign ref="mgEsignRef">
  </MgEsign>
</template>

<script lang="ts" setup>
const mgEsignRef = ref()

mgEsignRef.value?
  .generate({format: 'image/png', quality: 1 })
  .then(base64 => {
    console.log(base64)
  })
</script>
```

