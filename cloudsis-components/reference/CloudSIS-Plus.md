---
title: CloudSIS-Plus 帮助中心
description: 基于 Vue3 + Element Plus，部分功能依赖 @magustek/framework-core、@magustek/framework-utils、vue-i18n。在framwork插件的基础上增加sis项目的一些方法和组件
---

> [!tip] 提示
>
> CloudSIS 插件基于 Vue3 + Element Plus，部分功能依赖 @magustek/framework-core、@magustek/framework-utils、vue-i18n。

## 快速开始

### 1、安装

```bash
pnpm add @cloudsis/cloudsis-plus -S
```

### 2、引入样式

```ts
import '@cloudsis/cloudsis-plus/style';
```

### 3、全量注册

```ts
import { createApp } from 'vue';
import CloudsisPlus from '@cloudsis/cloudsis-plus';
import '@cloudsis/cloudsis-plus/style';

const app = createApp(App);
app.use(CloudsisPlus);
app.mount('#app');
```

### 4、按需使用

```ts
import {
  MagusImportCallback,
  MagusUploadFile,
  MagusSelectedLayout,
  MagusDictLabel,
} from '@cloudsis/cloudsis-plus';
import '@cloudsis/cloudsis-plus/style';

app.component('MagusImportCallback', MagusImportCallback);
app.component('MagusUploadFile', MagusUploadFile);
app.component('MagusSelectedLayout', MagusSelectedLayout);
app.component('MagusDictLabel', MagusDictLabel);
```

## 组件总览

| 组件名称            | 说明                                               |
| ------------------- | -------------------------------------------------- |
| MagusImportCallback | 导入结果回调弹窗（暴露 `init` 用于传入列表与标题） |
| MagusUploadFile     | 文件上传组件（基于 Element Plus `el-upload`）      |
| MagusSelectedLayout | 左右分栏 + 已选区布局容器                          |
| MagusDictLabel      | 字典标签展示（基于字典 code/value）                |

## 全局工具与格式化

插件安装后会注册以下全局方法，同时也可按需导入：

- `elTableDateFormat` / `$dateFormat`
- `elTableDictFormat` / `$dictFormat`
- `elTableNumFormat` / `$numFormat`
- `getDictLabel`

```ts
import {
  elTableDateFormat,
  elTableDictFormat,
  elTableNumFormat,
  getDictLabel,
} from '@cloudsis/cloudsis-plus';

const dateFormatter = elTableDateFormat('createdAt');
const dictFormatter = elTableDictFormat('status', 'device_status');
const numFormatter = elTableNumFormat('power', { decimal: 2, includeZeroPadding: true });

const label = getDictLabel('device_status', '1');
```

## [Hooks](../assets/hooks-examples.md) 总览

```ts
import {
  useApi,
  useCommon,
  useDateConfig,
  useDialog,
  useFileCallback,
  useForm,
  useMessageConfig,
  useSelected,
  useTablePaging,
  useTree,
} from '@cloudsis/cloudsis-plus/hooks';
```

| Hook             | 说明                                                                   |
| ---------------- | -------------------------------------------------------------------- |
| useApi           | axios 封装与下载能力（`downloadByUrl` / `streamDownload` / `downloadBycode`） |
| useCommon        | 数字格式化、字典格式、表单回填、轻量菜单树等通用能力                                           |
| useDateConfig    | 日期快捷选项与时间计算（range/single/all）                                        |
| useDialog        | 弹窗显示/关闭与 loading 状态                                                  |
| useFileCallback  | 导入回调辅助（配合 `MagusImportCallback`）                                     |
| useForm          | 表单校验封装（Element Plus `FormInstance`）                                  |
| useMessageConfig | i18n + 成功/警告消息封装                                                     |
| useSelected      | 表格勾选与已选数据维护                                                          |
| useTablePaging   | 列表分页、排序、删除、批量删除封装                                                    |
| useTree          | 树组件与路由联动、节点定位等                                                       |

## Handsontable / Excel 能力（可选）

> [!important] 注意
>
> 以下能力依赖可选包：`handsontable` 与 `xlsx-js-style`。未安装时会自动降级并打印警告。

```bash
pnpm add handsontable xlsx-js-style -S
```

```ts
import { useHandsontable } from '@cloudsis/cloudsis-plus/handson';
import { exportHelper } from '@cloudsis/cloudsis-plus/handson';
```
