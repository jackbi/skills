---
title: 自定义插件使用案例
description: 结合@cloudsis/cloudsis-plus，@magustek/framework-core，@magustek/framework-ui，@magustek/framework-utils等自定义插件的使用案例
---
## 初始要求

| 名称                        | 版本    | 备注                      |
| ------------------------- | ----- | ----------------------- |
| @magustek/framework-core  | 5.2.0 | framework基础包            |
| @magustek/framework-ui    | 5.2.0 | framework组件包            |
| @magustek/framework-utils | 5.2.0 | framework方法包            |
| @magustek/icon-svg        | 5.2.0 | framework图标包            |
| @magustek/framework-types | 5.2.0 | framework typescript类型包 |
| @cloudsis/cloudsis-plus   | 1.1.2 | 基于framwork的sis项目自定义封装组件 |

### 案例一 分页查询页面不带左侧树

>[!tip] 提示
>使用自定义组件：MgLayout，MgSearch，MgPaging
>使用自定义Hooks: useTablePaging


```vue
<template>
  <MgLayout class="list-inner">
    <template #header>
      <MgSearch :label-width="120" :show-buttons="false" :content-growth="true">
        <template #search>
          <el-row>
            <el-col :span="6">
              <el-form-item label="条件1">
                <el-input
                  v-model="query.conditions1"
                  class="!w-full"
                  clearable
                  placeholder="条件1"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件2">
                <el-input
                  v-model="query.conditions2"
                  class="!w-full"
                  clearable
                  placeholder="条件2"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件3">
                <el-input
                  v-model="query.conditions3"
                  class="!w-full"
                  clearable
                  placeholder="条件3"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col class="pl-[15px]" :span="6">
              <el-button type="primary" @click="queryAction">查询</el-button>
            </el-col>
          </el-row>
        </template>
      </MgSearch>
    </template>
    <template #main>
      <el-table
        v-loading="tableLoading"
        border
        script
        height="100%"
        :data="tableData"
        :style="{ width: '100%' }"
        @selection-change="handleSelectionChange"
      >
        <!-- <el-table-column type="selection" width="55"> </el-table-column> -->
        <el-table-column :label="属性1" prop="attribute1" min-width="120"></el-table-column>
        <el-table-column
          :label="属性2" prop="attribute2"
          width="180"
        ></el-table-column>
        <el-table-column
          :label="属性3" prop="attribute3"
          min-width="120"
        ></el-table-column>
        <el-table-column
          :label="属性4" prop="attribute4"
          min-width="120"
        ></el-table-column>
        <!-- ... -->
      </el-table>
    </template>
    <template #footer>
      <!-- 分页器组件 -->
      <MgPaging
        v-model:current-page="query.page"
        :page-sizes="[1, 10, 20, 30, 40]"
        :page-size="query.size"
        layout="sizes, prev, pager, next, jumper"
        :total="count"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      ></MgPaging>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { useTablePaging } from '@cloudsis/cloudsis-plus/hooks'
import { reactive } from 'vue'
import { getAllUnits, getPerformanceComputePage } from '@sisCommon/api/index.api'

defineOptions({
  name: 'ComputeDataSourcesPage',
})

const {
  query: queryHooks,
  queryAction,
  tableData,
  tableLoading,
  count,
  handleSizeChange,
  handleCurrentChange,
  queryTableData,
  handleSelectionChange,
} = useTablePaging(() => getTableData())

const query = reactive({
  conditions1: '',
  conditions2: '',
  conditions3: '',
  ...queryHooks,
})

const getTableData = () => {
  queryTableData(getPerformanceComputePage, query)
  console.log(1)
}

getTableData()
</script>
```


### 案例二 不分页查询页面

>[!tip] 提示
>使用自定义组件：MgLayout，MgSearch

```vue
<template>
  <MgLayout class="list-inner">
    <template #header>
      <MgSearch :label-width="120" :show-buttons="false" :content-growth="true">
        <template #search>
          <el-row>
            <el-col :span="6">
              <el-form-item label="条件1">
                <el-input
                  v-model="query.conditions1"
                  class="!w-full"
                  clearable
                  placeholder="条件1"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件2">
                <el-input
                  v-model="query.conditions2"
                  class="!w-full"
                  clearable
                  placeholder="条件2"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件3">
                <el-input
                  v-model="query.conditions3"
                  class="!w-full"
                  clearable
                  placeholder="条件3"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col class="pl-[15px]" :span="6">
              <el-button type="primary" @click="queryAction">查询</el-button>
            </el-col>
          </el-row>
        </template>
      </MgSearch>
    </template>
    <template #main>
      <el-table
        v-loading="tableLoading"
        border
        script
        height="100%"
        :data="tableData"
        :style="{ width: '100%' }"
      >
        <!-- <el-table-column type="selection" width="55"> </el-table-column> -->
        <el-table-column :label="属性1" prop="attribute1" min-width="120"></el-table-column>
        <el-table-column
          :label="属性2" prop="attribute2"
          width="180"
        ></el-table-column>
        <el-table-column
          :label="属性3" prop="attribute3"
          min-width="120"
        ></el-table-column>
        <el-table-column
          :label="属性4" prop="attribute4"
          min-width="120"
        ></el-table-column>
        <!-- ... -->
      </el-table>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { reactive, ref } from 'vue'
import { getPerformanceComputePage } from '@sisCommon/api/index.api'

defineOptions({
  name: 'ComputeDataSourcesPage',
})

const tableData = ref<CurrentType[]>()
const tableLoading = ref(false)

const query = reactive({
  conditions1: '',
  conditions2: '',
  conditions3: '',
})

const getTableData = () => {
  tableLoading.value = true
  getPerformanceComputePage().then(res => {
	  tableData.value = res.value.data || []
  }).finally(() => {
	  tableLoading.value = false
  })
}

const queryAction = () => {
	getTableData()
}

queryAction()
</script>
```

###  案例三 带左侧树的查询页面

>[!tip] 提示
>使用自定义组件：MgLayout，MgSearch，MgPaging， MgTree
>使用自定义Hooks: useTablePaging，useTree

```vue
// src/views/*/index.vue
<template>
  <MgLayout class="container-inner">
    <template #aside>
      <div v-loading="treeLoading" class="h-full w-full">
        <MgTree
          ref="magusTreeRef"
          :is-edit="false"
          :node-key="nodeKey"
          :data="treeData"
          :magus-icon-style="() => ({ color: '#6097FF' })"
          :is-need-expand="false"
          :expand-on-click-node="false"
          :expand-all="true"
          @node-click="clickNode"
          @reload-tree="reloadTree"
        ></MgTree>
      </div>
    </template>
    <template #main>
      <router-view ref="Childmain"></router-view>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { useTree } from '@cloudsis/cloudsis-plus/hooks'
import { treeItemType, PowerPlantsType } from '@sisCommon/types/index.type'
import { getAllPowerPlants } from '@sisCommon/api/index.api'
import { useRouter } from 'vue-router'
import { ref, provide } from 'vue'
defineOptions({
  name: 'UnitsPage',
})

const treeData = ref<treeItemType[]>([])

const powerPlants = ref<PowerPlantsType[]>([])

const router = useRouter()

const treeLoading = ref(false)

const nodeKey = 'nodeId'

const { magusTreeRef, nodeData, setCurrentNode } = useTree(() => getTree(), nodeKey)

const clickNode = (datas: treeItemType) => {
  const { nodeId } = datas
  nodeData.value = datas
  router.push({
    name: 'SisCommonUnitsPageList',
    params: { entityId: nodeId },
  })
}

const reloadTree = () => {
  getTree()
}

provide('nodeData', nodeData)
provide('powerPlants', powerPlants)

const getTree = () => {
  treeLoading.value = true
  getAllPowerPlants()
    .then((res) => {
      const datas = (res.data.value || []) as PowerPlantsType[]
      powerPlants.value = datas
      treeData.value = [
        {
          nodePid: '',
          nodeId: 'root',
          text: '电厂总览',
          entityId: 'root',
          leaf: false,
          extend: {},
          children: datas.map((el) => ({
            nodePid: '',
            nodeId: el.code,
            text: el.name,
            entityId: el.code,
            leaf: true,
            extend: {},
            children: [],
          })),
        },
      ]
      setCurrentNode(treeData.value, 'SisCommonUnitsPageList')
    })
    .finally(() => {
      treeLoading.value = false
    })
}
getTree()
</script>

```

```vue
// src/views/*/page.vue
<template>
  <MgLayout class="list-inner">
    <template #header>
      <MgSearch :label-width="120" :show-buttons="false" :content-growth="true">
        <template #search>
          <el-row>
            <el-col :span="6">
              <el-form-item label="条件1">
                <el-input
                  v-model="query.conditions1"
                  class="!w-full"
                  clearable
                  placeholder="条件1"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件2">
                <el-input
                  v-model="query.conditions2"
                  class="!w-full"
                  clearable
                  placeholder="条件2"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件3">
                <el-input
                  v-model="query.conditions3"
                  class="!w-full"
                  clearable
                  placeholder="条件3"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col class="pl-[15px]" :span="6">
              <el-button type="primary" @click="queryAction">查询</el-button>
            </el-col>
          </el-row>
        </template>
      </MgSearch>
    </template>
    <template #main>
      <el-table
        v-loading="tableLoading"
        border
        script
        height="100%"
        :data="tableData"
        :style="{ width: '100%' }"
        @selection-change="handleSelectionChange"
      >
        <!-- <el-table-column type="selection" width="55"> </el-table-column> -->
        <el-table-column :label="属性1" prop="attribute1" min-width="120"></el-table-column>
        <el-table-column
          :label="属性2" prop="attribute2"
          width="180"
        ></el-table-column>
        <el-table-column
          :label="属性3" prop="attribute3"
          min-width="120"
        ></el-table-column>
        <el-table-column
          :label="属性4" prop="attribute4"
          min-width="120"
        ></el-table-column>
        <!-- ... -->
      </el-table>
    </template>
    <template #footer>
      <!-- 分页器组件 -->
      <MgPaging
        v-model:current-page="query.page"
        :page-sizes="[1, 10, 20, 30, 40]"
        :page-size="query.size"
        layout="sizes, prev, pager, next, jumper"
        :total="count"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      ></MgPaging>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { useTablePaging } from '@cloudsis/cloudsis-plus/hooks'
import { reactive, watch, ref, PropType } from 'vue'
import { getUnitsPage, deleteUnits } from '@sisCommon/api/index.api'
import type { treeItemType, UnitsType } from '@sisCommon/types/index.type'
import { useRoute } from 'vue-router'
defineOptions({
  name: 'UnitsList',
})

const route = useRoute()

const props = defineProps({
  nodeData: {
    type: Object as PropType<treeItemType>,
    default: () => ({}),
  },
})

const {
  tableData,
  tableLoading,
  query: queryHooks,
  queryAction,
  queryTableData,
  count,
  handleSizeChange,
  handleCurrentChange,
  batchDeleteFun,
  multipleSelection,
  handleSelectionChange,
} = useTablePaging<UnitsType[]>(() => getTableData())

const query = reactive({
  conditions1: '',
  conditions2: '',
  conditions3: '',
  ...queryHooks,
})

const getTableData = () => {
  if (!route.params.entityId) return
  query.powerPlantCode = route.params.entityId == 'root' ? '' : (route.params.entityId as string)
  queryTableData(getUnitsPage, query)
}

const addUnits = () => {
  changeUnitsRef.value.init()
}

const editRow = (row: UnitsType) => {
  changeUnitsRef.value.init(row.id)
}

const deleteRow = (row: UnitsType) => {
  batchDeleteFun(deleteUnits, [row.id])
}

const batchDeleteUnits = () => {
  const ids = multipleSelection.value.map((el: UnitsType) => el.id)
  batchDeleteFun(deleteUnits, ids)
}

watch(
  () => route.params.entityId,
  () => {
    getTableData()
  },
  {
    immediate: true,
  }
)
</script>

```

### 案例四 新增页面

>[!tip] 提示
>使用自定义组件：MgAddTemplate
>使用自定义Hooks: useForm, useMessageConfig, useCommon

```vue
<template>
  <MgAddTemplate>
    <template #title>
      {{ $route.params.id ? 编辑 : 新增 }}
    </template>
    <template #main>
      <el-form>
        <el-form ref="formRef" :model="formData" label-width="120" :rules="formRules">
          <el-form-item label="属性1" prop="attribute1">
            <el-input v-model="formData.attribute1" placeholder="属性1"></el-input>
          </el-form-item>
          <el-form-item label="属性2" prop="attribute2">
            <el-input v-model="formData.attribute2" placeholder="属性2"></el-input>
          </el-form-item>
        </el-form>
      </el-form>
    </template>
    <template #footer>
      <el-button @click="cancelAndGoBack">取消</el-button>
      <el-button ref="ChangeaddHomesRef" :loading="loading" type="primary" @click="save">确定</el-button>
    </template>
  </MgAddTemplate>
</template>

<script lang="ts" setup>
import { useForm, useMessageConfig, useCommon } from '@cloudsis/cloudsis-plus/hooks'
import { ref, reactive } from 'vue'
import type { FormRules } from 'element-plus'
import { useI18n } from 'vue-i18n'
import { addHomes, editHomes, getHomesInfoById, getAllUnits } from '@sisCommon/api/index.api'
import type { HomesType } from '@sisCommon/types/index.type'
import { useDicts } from '@magustek/framework-core'
import { useRouter, useRoute } from 'vue-router'
import { Delete } from '@magustek/icon-svg'

defineOptions({
  name: 'ChangeHomes',
})

const router = useRouter()

const route = useRoute()

const addPointsRef = ref()
const HomeConfigchangeIconRef = ref()

const loading = ref(false)

const { formRef, validate } = useForm()
const { fillFormDataMutable } = useCommon()
const { successDefault } = useMessageConfig()

const { t } = useI18n()

const formData = reactive<HomesType>({
  attribute1: '',
  attribute2: '',
})

const configTypeList = useDicts('SISCOMMON_HOME_CONFIG_TYPE')
const { data: scheduleList } = getAllUnits()

const formRules = reactive<
  FormRules<{
    attribute1: string
    attribute2: string
  }>
>({
  attribute1: [
    {
      required: true,
      message: '属性1',
      trigger: 'blur',
    },
  ],
  attribute2: [
    {
      required: true,
      message: '属性2',
      trigger: 'change',
    },
  ],
})

const init = () => {
  if (route.params.id) {
    getHomesInfoById(route.params.id as string).then((res: AnyObject) => {
      fillFormDataMutable<HomesType>(formData, res.data.value)
    })
  }
}

const cancelAndGoBack = () => {
  router.back()
}
const save = () => {
  validate().then(() => {
    loading.value = true
    const funcMethod = route.params.id ? editHomes(route.params.id as string, formData) : addHomes([formData])
    funcMethod
      .then(() => {
        successDefault()
        cancelAndGoBack()
      })
      .finally(() => {
        loading.value = false
      })
  })
}

const openAddPoints = () => {
  addPointsRef.value.init()
  pointsInputRef.value.blur()
}

const openChangeIcon = () => {
  HomeConfigchangeIconRef.value.init()
}

const checkedPoints = (val: AnyObject) => {
  formData.globalCode = val.globalCode
}

const checkedIcon = (datas: { code: string; name: string }) => {
  formData.icon = datas.code
}

init()
</script>
```

### 案例五 弹窗组件的使用
>[!tip] 提示
>使用自定义组件：MgDialog
>使用自定义Hooks: useDialog, useForm, useMessageConfig

```vue
<template>
  <MgDialog v-model="isShow" :before-close="hideDialog" width="40%">
    <template #header>{{ $t('sis_common.entryData.approval_opinion') }}</template>
    <template #default>
      <el-form>
        <el-form ref="formRef" :model="formData" label-width="120">
          <el-form-item label="属性1" prop="attribute1">
            <el-input v-model="formData.attribute1" placeholder="属性1"></el-input>
          </el-form-item>
          <el-form-item label="属性2" prop="attribute2">
            <el-input v-model="formData.attribute2" placeholder="属性2"></el-input>
          </el-form-item>
        </el-form>
      </el-form>
    </template>
    <template #footer>
      <el-button @click="hideDialog">取消</el-button>
      <el-button :loading="loading" type="primary" @click="save">确认</el-button>
    </template>
  </MgDialog>
</template>

<script lang="ts" setup>
import { useCoreStore } from '@magustek/framework-core'
import { getAllOpinions, rejectEntryData } from '@sisCommon/api/index.api'
import { useDialog, useForm, useMessageConfig } from '@cloudsis/cloudsis-plus/hooks'
import { reactive, ref } from 'vue'

defineOptions({
  name: 'ChangeEntryData',
})

const props = defineProps({
  type: {
    type: String,
    default: 'reject',
  },
})

const { userInfo } = useCoreStore()

const { isShow, loading, cancel, show } = useDialog()
const { formRef, validate } = useForm()
const { successDefault } = useMessageConfig()

const emit = defineEmits(['success'])

const entryDataId = ref()

const formData = reactive({
  attribute1: '',
  attribute2: ''
})

const init = (id: string) => {
  entryDataId.value = id
  getAllOpinions(id).then((res: AnyObject) => {
    formData.opinion = res.data.value[res.data.value.length - 1].opinion
  })
  show()
}

const hideDialog = () => {
  cancel(() => {
    entryDataId.value = null
    formRef.value?.resetFields()
  })
}

const save = () => {
  if (props.type === 'watch') {
    hideDialog()
  } else {
    validate().then(() => {
      loading.value = true
      rejectEntryData(entryDataId.value, formData)
        .then(() => {
          successDefault()
          hideDialog()

          emit('success')
        })
        .finally(() => {
          loading.value = false
        })
    })
  }
}

defineExpose({
  init,
})
</script>
```

### 案例六 Handsontable表格展示的使用
>[!tip] 提示
>使用自定义组件：MgLayout
>使用自定义Hooks: useHandsontable

```vue
<template>
  <MgLayout>
    <template #header>
      <MgSearch :label-width="120" :show-buttons="false" :content-growth="true">
        <template #search>
          <el-row>
            <el-col :span="6">
              <el-form-item label="条件1">
                <el-input
                  v-model="query.conditions1"
                  class="!w-full"
                  clearable
                  placeholder="条件1"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件2">
                <el-input
                  v-model="query.conditions2"
                  class="!w-full"
                  clearable
                  placeholder="条件2"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="条件3">
                <el-input
                  v-model="query.conditions3"
                  class="!w-full"
                  clearable
                  placeholder="条件3"
                ></el-input>
              </el-form-item>
            </el-col>
            <el-col class="pl-[15px]" :span="6">
              <el-button type="primary" @click="queryAction">查询</el-button>
            </el-col>
          </el-row>
        </template>
      </MgSearch>
    </template>
    <template #main>
      <MgToolbar v-if="settings && settings.data">
        <el-button type="primary" @click="downs('班值考核-数据明细查询')">导出
        </el-button>
      </MgToolbar>
      <div v-if="!settings?.data && !tableLoading" class="h-full w-full">
        <el-empty :image-size="210" :description="'暂无数据'" style="height: 100%" />
      </div>
      <div
        v-else
        id="preview"
        ref="table-config-preview"
        v-loading="tableLoading"
        class="bi-preview-content-table h-[calc(100%-50px)]"
      >
        <div id="hotTable" ref="hotTableRef"></div>
      </div>
    </template>
  </MgLayout>
</template>

<script lang="ts" setup>
import { defineAsyncComponent, ref } from 'vue'
import { getFullTimeDataDetail } from '@/rtaCenter/api/index.api'
import { useDateConfig } from '@/rtaCenter/hooks/useDateConfig'
import { useHandsontable } from '@cloudsis/cloudsis-plus/handson'
import { onShow } from '@magustek/framework-biz-utils'
import Handsontable from 'handsontable'

defineOptions({
  name: 'FullTimeDataDetailPage',
})

const RtaUnitSelect = defineAsyncComponent(() => import('@/rtaCenter/components/RtaUnitSelect.vue'))
const RtaMajorsSelect = defineAsyncComponent(() => import('@/rtaCenter/components/RtaMajorsSelect.vue'))
const { reportOption, exportCsv, hotTableRef, handsonTables, createHandson } = useHandsontable()
const { currentMonth } = useDateConfig()

const tableLoading = ref(false)
const settings = ref<Handsontable.GridSettings>()
const query = ref({
  conditions1: '',
  conditions2: '',
  conditions3: '',
})
const changeDate = (val: string) => {
  if (!val) {
    query.value.time = currentMonth
  }
}
const downs = (filename: string) => {
  const data = handsonTables.value.getData()
  const mergeCells = settings.value?.mergeCells ? (settings.value.mergeCells as AnyObject[] | []) : []
  exportCsv({ data, mergeCells, filename, fomatterData: {} })
}

const getTableData = () => {
  tableLoading.value = true
  getFullTimeDataDetail(query.value)
    .then((res) => {
      if (!res.data.value) {
        // 需要清空上一次查询的数据
        if (handsonTables.value) {
          handsonTables.value.destroy()
          handsonTables.value = null
        }
        settings.value = {}
        return
      }
      const { cellData, cells: tableData, merges } = res.data.value
      let cells: Any = cellData.map((item) => {
        item.visualRow = item.rindex
        item.visualCol = item.cindex
        item.row = item.rindex
        item.col = item.cindex
        item.extend = item.extend
        return item
      })
      const responseSettings = {
        ...reportOption,
        cell: cells,
        data: tableData,
        fixedRowsTop: 1,
        fixedColumnsLeft: 1,
        colWidths: 120,
        fixedRowsBottom: 1,
        colHeaders: false,
        rowHeaders: false,
        mergeCells: merges,
        readOnly: true,
      }
      settings.value = responseSettings
      settings.value &&
        settings.value.cell &&
        settings.value.cell.forEach((el: any) => {
          if (el.extend) {
            el.renderer = 'customCellStyleRender'
          }
        })
      createHandson(settings.value)
    })
    .finally(() => {
      tableLoading.value = false
    })
}

getTableData()
</script>
```

