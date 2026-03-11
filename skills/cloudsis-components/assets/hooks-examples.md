---
title: hooks-examples
description: cloudsis项目一些封装好的hooks方法使用
---
## @cloudsis/cloudsis-plus插件hooks使用

### useApi(基于@magustek/framework-core封装了一些接口操作主要是下载请求)
#### 主要方法
```ts
const useApi: () => {
    setLocalAuth: (isDev: boolean, compCode?: string | undefined) => void;
    downloadByUrl: (url: string, fileName?: string | undefined) => boolean;
    streamDownload: <T = any>(downloadData: DownloadData | undefined, url: string, params?: Any, headers?: AnyObject, other?: AnyObject) => Promise<MgUseAxiosReturn<T, AxiosRequestConfig<any>>>;
    downloadBycode: (code: string, appCode?: string) => void;
}
```

#### 基本使用
``` ts
import { useApi } from '@cloudsis/cloudsis-plus/hooks'
import { Method } from '@cloudsis/cloudsis-plus'

const { streamDownload, downloadByUrl, downloadBycode } = useApi()

streamDownload({ method: Method.get, filename: '' }, downloadRulesExcel) // 通过文件流下载

downloadByUrl(url) // 通过url下载

downloadBycode(code) // 通过模板code下载
```

---
### `useCommon`(封装了一些常用方法)
#### 主要方法
``` ts
const useCommon: () => {
	// 保留两位消暑
    keepTwoDecimalFull: (num: string | number, datas?: {
        decimal: number;
        includeZeroPadding: boolean;
    } | undefined) => string | number;
    // 格式化字典数据
    formDictData: (list: AnyObject<any>[]) => AnyObject[];
    // 获取字典需要展示的值
    getDictLabel: (code: string, value: string | number, showType?: "id" | "text" | "nodeId" | "code", findType?: "id" | "text" | "nodeId" | "code") => string | undefined;
    // 组装菜单数据
    getLightMockMenu: (data: {
        path: string;
        name: string;
    }[], fatherData?: {
        path?: string;
        name?: string;
    }) => treeItemType[];
    // 重制表单数据
    resetFormData: <T extends Record<string, any>>(formData: T, formTemplate: T) => T;
    // 表单数据回填
    fillFormDataMutable: <T extends Record<string, any>>(formData: T, data: Partial<Record<keyof T, any>> | null | undefined) => T;
    // 表单数据回填返回新对象
    fillFormDataImmutable: <T extends Record<string, any>>(formTemplate: T, data: Partial<Record<keyof T, any>> | null | undefined) => T;
    // 表单数据回填+自定义map
    fillFormDataMutableWithMap: <T extends Record<string, any>, M extends Partial<Record<keyof T, string>>>(formData: T, data: Record<string, any> | null | undefined, map?: M) => T;
    //表单数据回填返回新对象+自定义map
    fillFormDataImmutableWithMap: <T extends Record<string, any>, M extends Partial<Record<keyof T, string>>>(formTemplate: T, data: Record<string, any> | null | undefined, map?: M) => T;
}
```

---
### `useDateConfig`(时间方法)

#### 主要方法
```ts
const useDateConfig: (dateFormat?: string) => {
	// 返回el-date-picker使用的Shortcuts
    getShortcuts: (type?: "single" | "range" | "all", shortcuts?: {
        text: string;
        value: Date | Date[];
    } | undefined) => {
        text: string;
        value: any;
    }[];
    // 默认时间格式化
    dateFormat: string;
    // 默认范围时间
    timeFrame: Ref<any, any>;
    // 获取当前时间
    getCurrentTime: (format?: string) => any;
    // 获取当前往前推的时间
    getCurrentPreTime: (pre?: number, preType?: ManipulateType, format?: string) => any;
    // 获取当前往后推的时间
    getCurrentNextTime: (pre?: number, preType?: ManipulateType, format?: string) => any;
}
```

---
### `useDialog`(弹窗的方法)
#### 主要方法
```ts
const useDialog: () => {
	// 是否展示弹窗
    isShow: Ref<boolean, boolean>;
    // 加载状态
    loading: Ref<boolean, boolean>;
    // 隐藏弹窗方法
    cancel: (callback?: AnyFn) => void;
    // 展示弹窗方法
    show: () => void;
}
```

#### 使用案例
```vue
<template>
	<MgDialog
	    v-model="isShow"
	    :title="$t('overlimit.dialogLabel.addWarnAuths')"
	    :close-on-click-modal="false"
	    :close-on-press-escape="false"
	    :before-close="confirmCancel"
	    class="no-bg-dialog"
	    width="80%">
	</MgDialog>
</template>
<script lang="ts" setup>
import { useDialog } from '@cloudsis/cloudsis-plus/hooks';

const { isShow, cancel, show } = useDialog();

const confirmCancel = () => {
  cancel();
  tableData.value = [];
  checkedData.value = [];
};

const init = () => {
  queryAction();
  show();
};

defineExpose({
  init,
});
</script>
```

---

### `useFileCallback`(导入文件后的提示方法)
#### 主要方法
```ts
const useFileCallback: () => {
	// 组件的ref
    ListCallbackDataRef: Ref<any, any>;
    // 上传按钮的名称，进度，状态
    uploadData: {
        btnName: string;
        process: number;
        loading: boolean;
    };
    // 上传成功后的方法
    successImport: (res: {
        data: string[];
        message: string;
        success: boolean;
    }, callback?: AnyFn) => void;
}
```

#### 使用案例
```vue
<template>
	<MagusUploadFile
		v-model:upload-loading="loadingData.upload"
		:is-need-tip="false"
		:action="importAlarmRules"
		:show-file-list="false"
		:choose-file-title="uploadData.btnName"
		methods="post"
		:expiration-time="60000"
		:auto-upload="true"
		class="mx-[6px]"
		:disabled="uploadData.loading"
		accept="application/vnd.ms-excel,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
		@handle-success="handleSuccess"
		@handle-process="handleProcess">
	</MagusUploadFile>
	<MagusImportCallback ref="ListCallbackDataRef"></MagusImportCallback>
</template>
<script lang="ts" setup>
import { useFileCallback } from '@cloudsis/cloudsis-plus/hooks';

const { ListCallbackDataRef, successImport, uploadData } = useFileCallback();

const handleProcess = () => {
  uploadData.loading = true;
  uploadData.btnName = '上传中...';
};

const handleSuccess = (res: AnyObject) => {
  successImport(res.data.value);
};

</script>
```

---
### `useForm`(表单的常用方法)

#### 主要方法
```ts
const useForm: () => {
	// 表单ref
    formRef: Ref<FormInstance | undefined, FormInstance | undefined>;
    // 表单验证
    validate: () => Promise<unknown>;
}
```

#### 使用案例

```vue
<template>
<el-form ref="formRef" :model="formData" :rules="formRules" label-width="auto">
</el-form>
</template>

<script lang="ts" setup>
import { useForm } from '@cloudsis/cloudsis-plus/hooks';
const { formRef, validate } = useForm();

const save = () => {
  validate().then(() => {})
}
</script>
```

---
### `useHandsontable`（handsontable常用方法封装）
#### 主要方法
```ts
const useHandsontable: () => {
	// handsontable的一些基础配置
    reportOption: {
        colHeaders: boolean;
        rowHeaders: boolean;
        copyPaste: boolean;
        width: string;
        height: string;
        language: string;
        licenseKey: string;
    };
    // 自定义单元格渲染方法
    customCellStyleRender: (_instance: any, td: HTMLTableCellElement, _row: number, _column: number, _prop: string | number, value: any, cellProperties: any) => HTMLTableCellElement;
    // handsontable导出excel方法
    exportCsv: ({ filename, data, mergeCells, fomatterData, }: {
        filename: string;
        data: string[][];
        mergeCells: AnyObject<any>[];
        fomatterData: AnyObject;
    }) => void;
    // handsontable dom ref定义
    hotTableRef: Ref<any, any>;
    // handsontable 实例
    handsonTables: Ref<any, any>;
    // 通过配置创建handsontable
    createHandson: (tableData?: Any) => Promise<...>;
    // handsontable 父级dom ref定义
    hotTableBoxRef: Ref<...>;
    // 读取表格数据
    fileParsing: (File: File) => Promise<...>;
}
```

---

### `useMessageConfig`（提示方法）
#### 主要方法
```ts
const useMessageConfig: () => {
	// 成功提示
    successDefault: (message?: string | undefined) => void;
    // 错误提示
    warningDefault: (message?: string | undefined) => void;
}
```

#### 使用案例
```vue
<script lang="ts" setup>
import { useMessageConfig } from '@cloudsis/cloudsis-plus/hooks';

const { successDefault, warningDefault } = useMessageConfig();
</script>
```


---

### `useSelected`（el-table 多选框选中的常用方法，配合多选弹窗使用）

#### 主要方法
```ts
const useSelected: <TABLE_DATA extends Record<string, any>>(tableData: Ref<TABLE_DATA[]>, selectedCert?: string) => {
	// 选中数据
    checkedData: Ref<UnwrapRefSimple<TABLE_DATA>[], TABLE_DATA[] | UnwrapRefSimple<TABLE_DATA>[]>;
    // 选中某一行
    checkRow: (_selection: TABLE_DATA[], row: TABLE_DATA) => void;
    // 选中所有行
    checkAllRow: (datas: TABLE_DATA[]) => void;
    // 删除选中行
    deleteCheckedRow: (datas: TABLE_DATA) => void;
    // 表格ref
    checkedTableRef: Ref<any, any>;
    // 选中自定义数据
    checkedCustomData: (datas: TABLE_DATA[]) => void;
}
```

#### 使用案例
``` vue
<template>
	<el-table
		ref="checkedTableRef"
		v-loading="tableLoading"
		:data="tableData"
		border
		height="100%"
		size="small"
		@select="checkRow"
		@select-all="checkAllRow">
	</el-table>
</template>

<script lang="ts" setup>
import { useSelected } from '@cloudsis/cloudsis-plus/hooks';
const tableData = ref<TrendPontTableDataType[]>([]);
const { checkRow, checkedTableRef, checkedCustomData, checkAllRow, checkedData } =
  useSelected<TrendPontTableDataType>(tableData, 'pointCode');
</script>
```


---


### useTablePaging(分页表格方法)
#### 主要方法
```ts
import { UnwrapRef, ToRefs, Ref } from 'vue';
import { pagingType } from '../types/common';
interface TablePagingReturn<TABLE_DATA> {
  // 分页条件
  query: ToRefs<{
    attr: string;
    sort: string;
    page: number;
    size: number;
  }>;
  // 总数据
  count: Ref<number, number>;
  // 加载状态
  tableLoading: Ref<boolean, boolean>;
  // 表格数据
  tableData:
    | Ref<any>
    | Ref<TABLE_DATA>
    | Ref<UnwrapRef<TABLE_DATA>, TABLE_DATA | UnwrapRef<TABLE_DATA>>;
  // 多选数据
  multipleSelection:
    | Ref<any>
    | Ref<TABLE_DATA>
    | Ref<UnwrapRef<TABLE_DATA>, TABLE_DATA | UnwrapRef<TABLE_DATA>>;
  // 修改一页展示条目数
  handleSizeChange: (val: number) => void;
  // 修改当前页
  handleCurrentChange: (val: number) => void;
  // 选中项修改方法
  handleSelectionChange: (val: TABLE_DATA) => void;
  // 排序修改方法
  sortChange: ({ prop, order }: { prop: string; order: string }) => void;
  // 查询永远返回第一页数据
  queryAction: () => void;
  // 分页查询方法
  queryTableData: (
    api: string,
    params: any,
    method?: 'get' | 'post',
  ) => Promise<pagingType<TABLE_DATA> | undefined>;
  // 单个删除方法
  deleteFun: (api: string, params?: {}) => Promise<boolean>;
  // 批量删除方法
  batchDeleteFun: (api: string, params: any) => Promise<boolean>;
  // 自定义查询方法
  queryTableDataCustomResponse: (
    api: string,
    params: AnyObject<any>,
    method?: 'get' | 'post',
    field?: string,
  ) => Promise<AnyObject<any> | undefined>;
}
export declare const useTablePaging: <TABLE_DATA = AnyObject[]>(
  getTableData: AnyFn,
) => TablePagingReturn<TABLE_DATA>;
export {};
```

#### 使用案例

```vue
<template>
	<MgPaging
		v-model:current-page="query.page"
		:page-sizes="[1, 10, 20, 30, 40]"
		:page-size="query.size"
		layout="sizes, prev, pager, next, jumper"
		:total="count"
		@size-change="handleSizeChange"
		@current-change="handleCurrentChange">
	</MgPaging>
</template>

<script lang="ts" setup>
import { useTablePaging } from '@cloudsis/cloudsis-plus/hooks'

const {
  query: queryHooks,
  count,
  tableLoading,
  tableData,
  handleSizeChange,
  handleCurrentChange,
  handleSelectionChange,
  queryAction,
  queryTableData,
} = useTablePaging(() => getTableData())


const getTableData = () => {
  queryTableData(getIotEquipmentsProperties(query.value.indicatorCategoryId), query.value)
}
</script>
```


---
### `useTree`配合MgTree组件使用，主要用于左侧树查询页面

#### 主要方法

```ts
const useTree: (getTree: () => void, nodeKey?: string, routeParamsKey?: string) => {
	//展开第一个节点
    expandFirstNode: (datas: string[] | number[] | undefined) => void;
    // 数节点默认图标
    magusIconClass: (_node: TreeNode, data: treeItemType) => string;
    // 选中当前节点
    setCurrentNode: (datas: {
        [key: string]: Any;
    }[], routerName: string, routerParams?: object | undefined, expandedKeys?: string[] | number[] | undefined) => void;
    // 刷新页面
    reloadTree: () => void;
    // 展开某些节点
    expandedKeys: Ref<string[] | number[], string[] | number[]>;
    // 节点数据
    nodeData: Ref<any, any>;
    // 更新节点数据
    upDateNodeData: (datas: treeItemType) => void;
    // 数节点Ref
    magusTreeRef: Ref<...>;
    // 无数据跳转
    treeNoDataLink: (routerName: string, routerParams?: {
        [key: string]: Any;
    }) => void;
}
```

#### 使用案例

```vue
<template>
  <MgLayout class="container-inner">
    <template #aside>
      <MgTree
        ref="magusTreeRef"
        :is-need-reload="false"
        :expand-on-click-node="false"
        :magus-icon-class="magusIconClass"
        :magus-icon-style="magusIconStyle"
        node-key="entityId"
        :data="treeData"
        default-expand-all
        :is-edit="false"
        @node-click="clickNode"
      ></MgTree>
    </template>
    <template #main>
      <router-view></router-view>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { useTree } from '@cloudsis/cloudsis-plus/hooks';
import { useRouter } from 'vue-router';
import { getAlarmAuthRoleTree } from '@/api/index.api';
import type { TreeNode } from 'element-plus';
import type { treeItemType } from '@/types/index.type';
import { ref } from 'vue';
defineOptions({
  name: 'AlarmAuthsindex',
});

const { setCurrentNode, magusTreeRef } = useTree(() => getTree());
const router = useRouter();
const treeData = ref<treeItemType[]>([]);

const getTree = () => {
  getAlarmAuthRoleTree().then((res) => {
    treeData.value = res.data.value?.map((item) => {
      return {
        nodePid: '',
        nodeId: item.id,
        entityId: item.id,
        entityType: 'role',
        text: item.name,
        leaf: true,
        extend: {
          ...item,
        },
        children: [],
      };
    }) as treeItemType[];
    setCurrentNode(treeData.value, 'warnAuthsPage');
  });
};
const magusIconClass = (node: TreeNode, data: treeItemType) => {
  return data.icon?.replace('magusdip', '') || 'magusdip-iconfont magusdipjiaose';
};

const magusIconStyle = () => {
  let datas = {
    color: 'rgb(43, 120, 255)',
  };
  return datas;
};

const clickNode = (data: treeItemType) => {
  router.push({
    name: 'warnAuthsPage',
    params: {
      entityId: data.entityId,
    },
  });
};

getTree();
</script>
<style lang="scss" scoped></style>
```