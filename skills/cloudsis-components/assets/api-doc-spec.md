---
title: api-doc-spec
description: 后端 API 文档读取规范 —— 教 AI Agent 如何从 Swagger/Knife4j 链接或 api.md 文件自动生成 .api.ts 和 .type.ts
---

# 后端 API 文档读取规范

本规范定义如何从后端提供的 **Swagger/Knife4j 在线链接** 或 **api.md 导出文件** 获取接口信息，并按照 cloudsis 项目规范生成对应的 `.api.ts` 和 `.type.ts` 文件。

---

## 一、数据来源与获取方式

后端提供接口文档通常有两种形式，Agent 需根据输入自动判断：

### 1.1 Swagger/Knife4j 在线链接（推荐）

用户给出的链接格式通常为 `http://host:port/doc.html`。

> **注意**：`doc.html` 是 Knife4j 的 SPA 页面，直接 fetch HTML 无法获取接口数据。必须访问 JSON API 端点。

**获取步骤**：

1. **拼接 JSON 端点**：将 `doc.html` 替换为 `/v3/api-docs/default`
   ```
   用户输入：http://192.168.20.38:45000/doc.html
   JSON 端点：http://192.168.20.38:45000/v3/api-docs/default
   ```

2. **请求 JSON 数据**：使用 `webfetch` 或 `fetch` 工具访问该端点，获取 OpenAPI 3.x JSON

3. **解析 OpenAPI JSON 结构**：

   ```jsonc
   {
     "openapi": "3.0.1",
     "info": { "title": "...", "version": "..." },
     "paths": {
       "/forms/page": {
         "get": {
           "tags": ["表单管理"],        // → 模块名
           "summary": "分页查询表单",    // → 函数注释
           "operationId": "...",
           "parameters": [...],         // → query/path 参数
           "requestBody": {...},        // → body 参数
           "responses": {
             "200": {
               "content": {
                 "application/json": {
                   "schema": { "$ref": "#/components/schemas/XxxResponse" }
                 }
               }
             }
           }
         }
       }
     },
     "components": {
       "schemas": {
         "FormResponse": {
           "type": "object",
           "properties": {
             "formKey": { "type": "string" },
             "method": { "type": "string", "enum": ["iframe", "report", "view"] }
           }
         }
       }
     }
   }
   ```

4. **OpenAPI → 类型/API 的关键映射**：

   | OpenAPI 字段 | 对应用途 |
   |---|---|
   | `paths` 的 key | 接口地址（URL） |
   | HTTP method (`get`/`post`/`put`/`delete`) | 请求方式 |
   | `tags[0]` | 模块名（用于文件命名） |
   | `summary` | 函数 JSDoc 注释 |
   | `parameters` (query/path) | 查询参数类型 |
   | `requestBody.content.*.schema` | 请求体类型 |
   | `responses.200.content.*.schema` | 响应类型 |
   | `components.schemas` | 所有类型定义（$ref 引用解析） |
   | `schema.enum` | 联合类型（枚举值） |
   | `schema.required` | 是否必填（不在数组中则加 `?`） |

5. **$ref 引用解析**：遇到 `"$ref": "#/components/schemas/XxxResponse"` 时，从 `components.schemas.XxxResponse` 取完整属性定义

### 1.2 api.md 导出文件

用户直接提供 Swagger/Knife4j 导出的 Markdown 文件（如 `api.md`），结构固定：

```
# 模块名称（一级标题）           → 文件名前缀，如「表单管理」→ form
## 接口名称（二级标题）         → 函数名称推导依据
**接口地址**: `/forms/page`   → URL 路径
**请求方式**: `GET`            → HTTP method
**请求参数**: 表格             → 函数入参类型
**响应参数**: 表格             → 响应数据类型
**响应示例**: JSON             → 辅助验证类型结构
```

### 1.3 输入判断规则

| 用户输入 | 判断为 | 获取方式 |
|---|---|---|
| `http(s)://xxx/doc.html` | Swagger 链接 | 访问 `/v3/api-docs/default` JSON 端点 |
| `http(s)://xxx/swagger-ui.html` | Swagger 链接 | 访问 `/v3/api-docs/default` JSON 端点 |
| `http(s)://xxx/v3/api-docs` | 直接 JSON 端点 | 直接访问 |
| 本地文件路径（`.md`） | 导出的 Markdown | 读取文件内容 |
| 粘贴的 Markdown 文本 | 导出的 Markdown | 直接解析 |

> **优先级**：JSON 端点数据比 Markdown 更结构化、更准确，优先使用。

---

## 二、类型映射规则

### 2.1 基础类型映射

| 源类型 | TypeScript 类型 | 说明 |
|---|---|---|
| `string` | `string` | |
| `string` + `format: date-time` | `string` | 日期时间统一用 string，不用 Date |
| `integer` / `int32` / `int64` | `number` | |
| `number` | `number` | |
| `boolean` | `boolean` | |
| `object`（无 properties） | `Record<string, unknown>` | 不用 any |
| `array` + `items` | `T[]` | T 取 items 指定的类型 |
| `string` + `format: binary` | `File` | 文件上传 |

### 2.2 枚举值映射

OpenAPI JSON 的 `enum` 字段，或 api.md 中「可用值:xxx,yyy,zzz」，必须转为联合类型：

```ts
// OpenAPI: "enum": ["done", "todo"]
// api.md: 可用值:done,todo
status: 'done' | 'todo'

// OpenAPI: "enum": ["iframe", "report", "view"]
method: 'iframe' | 'report' | 'view'
```

### 2.3 嵌套对象映射

**OpenAPI JSON**：通过 `$ref` 引用或内联 `properties` 定义嵌套，直接解析即可。

**api.md**：用 `&emsp;&emsp;` 缩进表示嵌套：

```
| result       |              | BaseRspResult |     → 父属性
| &emsp;&emsp;bizCode | 业务代码 | integer(int32) | → result 的子属性
```

转换为嵌套 interface：

```ts
interface BaseRspResult {
  bizCode: number
  resultCode: string
  message: string
  validResults: BeanValidResult[]
}
```

---

## 三、通用类型提取

### 3.1 实体基类（几乎所有响应都包含）

```ts
// src/types/common.type.ts

/** 实体基类字段 */
export interface BaseEntity {
  id: string
  createBy: string
  createDate: string
  lastModifiedBy: string
  lastModifiedDate: string
  forceModifiedVersion: number
}
```

### 3.2 通用响应结构（所有 500 响应）

```ts
/** 表单校验错误 */
export interface BeanValidResult {
  propertyPath: string
  message: string
}

/** 系统结果码 */
export type ResultCode = 'SUCCESS' | 'FAIL_SYSTEM' | 'FAIL_BUSINESS'

/** 基础响应结果 */
export interface BaseRspResult {
  bizCode: number
  resultCode: ResultCode
  message: string
  validResults: BeanValidResult[]
}

/** 通用响应（错误） */
export interface CommonRsp {
  result: BaseRspResult
  rspFlag: boolean
}
```

### 3.3 分页响应结构（Spring Data Page）

```ts
/** 排序对象 */
export interface SortObject {
  sorted: boolean
  empty: boolean
  unsorted: boolean
}

/** 分页对象 */
export interface PageableObject {
  pageNumber: number
  offset: number
  sort: SortObject
  paged: boolean
  unpaged: boolean
  pageSize: number
}

/** 分页响应泛型，T 为 content 数据类型 */
export interface PageResponse<T> {
  totalPages: number
  totalElements: number
  pageable: PageableObject
  size: number
  content: T[]
  number: number
  sort: SortObject
  numberOfElements: number
  first: boolean
  last: boolean
  empty: boolean
}
```

### 3.4 分页查询通用参数

```ts
/** 分页查询基础参数 */
export interface PageQuery {
  page?: number
  size?: number
  attr?: string
  sort?: 'ASC' | 'DESC'
}
```

---

## 四、业务类型生成规则

### 4.1 文件命名

| 类型 | 命名规则 | 示例 |
|---|---|---|
| API 调用 | `模块名.api.ts` | `form.api.ts` |
| 类型定义 | `模块名.type.ts` | `form.type.ts` |
| 统一导出 | `index.api.ts` / `index.type.ts` | 桶文件 |

### 4.2 模块名推导

从 OpenAPI 的 `tags` 或 api.md 一级标题推导文件名：

| 模块 | 文件名 | 说明 |
|---|---|---|
| 表单管理 | `form` | 取接口路径前缀 `/forms` 的单数 |
| 待办中心 | `todo` | 取接口路径前缀 `/todo` |
| 流程操作 | `processOperation` | 取接口路径前缀 `/processOperations`，驼峰 |
| 流程管理 | `process` | 取接口路径前缀 `/processes` |
| 流程实例管理 | `processInstance` | 取接口路径前缀 `/processInstances` |
| 平台接口 | `platform` | 取接口路径前缀 `/platform` |
| 报表总览 | `reportOverview` | 取接口路径前缀 `/reportOverviews` |

### 4.3 响应类型提取

1. 取响应状态 200 的 schema 名称作为 interface 名称
2. 去除基类字段（`id/createBy/createDate/lastModifiedBy/lastModifiedDate/forceModifiedVersion`），用 `extends BaseEntity`
3. 枚举值转联合类型
4. 嵌套对象拆为独立 interface

**示例：从「表单管理 - 新增」提取：**

```ts
// src/types/form.type.ts
import type { BaseEntity, PageQuery } from './common.type'

/** 表单使用方法 */
export type FormMethod = 'iframe' | 'report' | 'view'

/** 表单响应 */
export interface FormResponse extends BaseEntity {
  formKey: string
  formName: string
  method: FormMethod
  value: string
}

/** 表单新增/编辑请求 */
export interface FormRequest {
  formKey: string
  formName: string
  method: FormMethod
  value: string
}

/** 表单查询参数 */
export interface FormQueryParams extends PageQuery {
  formKey?: string
  formName?: string
}
```

### 4.4 请求类型提取

1. 请求类型取 body 参数的 schema 名称，或用 `模块Request` 命名
2. query 参数合并为 `模块QueryParams`，分页参数继承 `PageQuery`
3. path 参数直接作为函数入参，不单独定义类型
4. `required` 中的参数不加 `?`，其余加 `?`

---

## 五、API 调用方法生成规则

### 5.1 基本结构

```ts
// src/api/模块名.api.ts
import { useAxios } from '@magustek/framework-core'
import type { XxxResponse, XxxRequest, XxxQueryParams } from '@/types/index.type'
import type { PageResponse } from '@/types/common.type'

const api = import.meta.env.VITE_APP_XXX_API
const { get, post, request } = useAxios()
```

### 5.2 HTTP 方法映射

| 请求方式 | useAxios 方法 | 说明 |
|---|---|---|
| `GET` | `get<T>(url, params)` | 查询类 |
| `POST` | `post<T>(url, data)` | 新增类 |
| `PUT` | `request<T>(url, { data, method: 'PUT' })` | 修改类 |
| `DELETE` | `request(url, { method: 'DELETE' })` | 删除类 |

### 5.3 函数命名规则

| 操作类型 | 命名模式 | 示例 |
|---|---|---|
| 分页查询 | `get模块Page` | `getFormPage` |
| 查询所有 | `get模块All` | `getFormAll` |
| 根据 ID 查询 | `get模块ById` | `getFormById` |
| 新增 | `add模块` | `addForm` |
| 修改 | `edit模块` | `editForm` |
| 删除 | `delete模块` | `deleteForm` |
| 特定操作 | `动词模块` | `deployProcess`、`cancelProcess` |

### 5.4 返回类型标注

**必须**用泛型标注返回类型，禁止 `any`：

```ts
// ✅ 正确：明确泛型
export const getFormPage = (params: FormQueryParams) =>
  get<PageResponse<FormResponse>>(`${api}/forms/page`, params)

// ✅ 正确：删除无业务数据返回，可不加泛型
export const deleteForm = (id: string) =>
  request(`${api}/forms/${id}`, { method: 'DELETE' })

// ❌ 错误：禁止 any
export const getFormPage = (params: any) => get<any>(`${api}/forms/page`, params)
```

---

## 六、完整示例：表单管理模块

### 6.1 类型文件 `src/types/form.type.ts`

```ts
import type { BaseEntity, PageQuery } from './common.type'

/** 表单使用方法 */
export type FormMethod = 'iframe' | 'report' | 'view'

/** 表单响应 */
export interface FormResponse extends BaseEntity {
  formKey: string
  formName: string
  method: FormMethod
  value: string
}

/** 表单新增/编辑请求 */
export interface FormRequest {
  formKey: string
  formName: string
  method: FormMethod
  value: string
}

/** 表单查询参数（分页） */
export interface FormPageQuery extends PageQuery {
  formKey?: string
  formName?: string
}

/** 表单查询参数（全量） */
export interface FormAllQuery {
  formKey?: string
  formName?: string
}
```

### 6.2 API 文件 `src/api/form.api.ts`

```ts
import { useAxios } from '@magustek/framework-core'
import type {
  FormResponse,
  FormRequest,
  FormPageQuery,
  FormAllQuery,
} from '@/types/index.type'
import type { PageResponse } from '@/types/common.type'

const api = import.meta.env.VITE_APP_XXX_API
const { get, post, request } = useAxios()

/** 分页查询表单 */
export const getFormPage = (params: FormPageQuery) =>
  get<PageResponse<FormResponse>>(`${api}/forms/page`, params)

/** 查询所有表单 */
export const getFormAll = (params: FormAllQuery) =>
  get<FormResponse[]>(`${api}/forms/all`, params)

/** 根据 ID 查询表单 */
export const getFormById = (id: string) =>
  get<FormResponse>(`${api}/forms/${id}`)

/** 新增表单 */
export const addForm = (data: FormRequest) =>
  post<FormResponse>(`${api}/forms`, data)

/** 编辑表单 */
export const editForm = (id: string, data: FormRequest) =>
  request<FormResponse>(`${api}/forms/${id}`, { data, method: 'PUT' })

/** 删除表单 */
export const deleteForm = (id: string) =>
  request(`${api}/forms/${id}`, { method: 'DELETE' })
```

### 6.3 桶文件导出

```ts
// src/types/index.type.ts
export * from './common.type'
export * from './form.type'

// src/api/index.api.ts
export * from './form.api'
```

---

## 七、生成流程清单

AI Agent 收到接口文档后，按以下步骤执行：

1. **判断输入来源**：根据 1.3 判断规则，确定是 Swagger 链接还是 Markdown 文件
2. **获取接口数据**：
   - Swagger 链接 → 访问 `/v3/api-docs/default` 获取 OpenAPI JSON
   - Markdown 文件 → 读取文件内容
3. **识别模块**：从 `tags`（JSON）或 `#` 一级标题（Markdown）确定模块划分
4. **提取通用类型**：检查是否已有 `common.type.ts`，没有则创建（BaseEntity、CommonRsp、PageResponse 等）
5. **遍历每个接口**：
   - 解析接口地址、请求方式、参数、响应
   - 生成响应 interface（继承 BaseEntity）
   - 生成请求 interface（body 参数）
   - 生成查询参数 interface（query 参数，分页继承 PageQuery）
   - 枚举值转联合类型
6. **去重**：同一模块的相同响应类型只定义一次（如 FormResponse 在多个接口复用）
7. **生成 API 函数**：按命名规则生成，标注泛型返回类型
8. **更新桶文件**：在 `index.type.ts` 和 `index.api.ts` 中添加 `export *`

---

## 八、注意事项

1. **禁止 `any`**：所有类型必须明确，用 `unknown` + 类型守卫替代
2. **类型导入用 `import type`**：值与类型导入分开
3. **环境变量**：API 基础路径从 `import.meta.env.VITE_APP_XXX_API` 读取，不硬编码
4. **删除/PUT 用 `request`**：不用 `get`/`post`，必须用 `request` 并指定 method
5. **分页接口**：返回类型统一用 `PageResponse<T>`，不重复定义分页结构
6. **复用已有类型**：相同结构的响应只定义一个 interface，多接口复用
7. **注释**：从 `summary`（JSON）或参数说明列（Markdown）提取中文注释，用 JSDoc `/** */` 格式
8. **JSON 端点优先**：如果 Swagger 链接和 Markdown 文件同时可用，优先使用 JSON 端点，数据更准确
