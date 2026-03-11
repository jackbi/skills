---
title: typescript-standards
---
## TypeScript 标准

TypeScript 在 Vue 前端代码中类型安全和可维护性的最佳实践。

---

### 严格模式

#### 配置

TypeScript 严格模式在项目中是**启用的**：

```json
// tsconfig.node.json
{
  "compilerOptions": {
    "composite": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports":  true
  },
  "include": ["vite.config.ts"]
}
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "strict": true,
    "jsx": "preserve",
    "sourceMap": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "lib": ["ESNext", "DOM"],
    "skipLibCheck": true,
    "baseUrl": ".",
    "types": ["element-plus/global", "@magustek/framework-types"],
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*.*"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**这意味着：**

- 没有隐式的 "any" 类型
- 必须显式处理 Null/undefined
- 强制执行类型安全

---

### 禁止使用 `any` 类型

#### 规则

```ts
// ❌ 永远不要使用 any
function handleData(data: any) {
  return data.something;
}

// ✅ 使用具体类型
interface MyData {
  something: string;
}

function handleData(data: MyData) {
  return data.something;
}

// ✅ 或者对于真正未知的数据使用 unknown
function handleUnknown(data: unknown) {
  if (typeof data === "object" && data !== null && "something" in data) {
    return (data as MyData).something;
  }
}
```

**如果你真的不知道类型：**

- 使用 `unknown`（强制类型检查）
- 使用类型守卫进行类型收窄
- 记录为什么类型未知

---

### 显式返回类型

#### 函数返回类型

```ts
// ✅ 正确 - 显式返回类型
function getUser(id: number): Promise<User> {
  return apiClient.get(`/users/${id}`);
}

function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// ❌ 避免 - 隐式返回类型（不够清晰）
function getUser(id: number) {
  return apiClient.get(`/users/${id}`);
}
```

#### 组件返回类型

```ts
// 对于自定义 hooks
function useMyData(id: number): { data: Data; isLoading: boolean } {
    const [data, setData] = useState<Data | null>(null);
    const [isLoading, setIsLoading] = useState(true);

    return { data:  data!, isLoading };
}
```

---

### 类型导入

#### 使用 'type' 关键字

```ts
// ✅ 正确 - 显式标记为类型导入
import type { User } from "@/types/index";
import type { Post } from "@/types/index";

// ❌ 避免 - 混合值和类型导入
import { User } from "@/types/user"; // 不清楚是类型还是值
```

**好处：**

- 清晰地分离类型和值
- 更好的 tree-shaking
- 防止循环依赖
- TypeScript 编译器优化

---

### 工具类型

#### Partial\<T\>

```ts
// 使所有属性变为可选
type UserUpdate = Partial<User>;

function updateUser(id: number, updates:  Partial<User>) {
  // updates 可以包含 User 属性的任何子集
}
```

#### Pick\<T, K\>

```ts
// 选择特定属性
type UserPreview = Pick<User, "id" | "name" | "email">;

const preview: UserPreview = {
  id: 1,
  name: "John",
  email: "john@example.com",
  // 不允许其他 User 属性
};
```

#### Omit\<T, K\>

```ts
// 排除特定属性
type UserWithoutPassword = Omit<User, "password" | "passwordHash">;

const publicUser: UserWithoutPassword = {
  id: 1,
  name: "John",
  email: "john@example.com",
  // 不允许 password 和 passwordHash
};
```

#### Required\<T\>

```ts
// 使所有属性变为必需
type RequiredConfig = Required<Config>; // 所有可选属性变为必需
```

#### Record\<K, V\>

```ts
// 类型安全的对象/映射
const userMap:  Record<string, User> = {
  user1: { id: 1, name: "John" },
  user2: { id: 2, name: "Jane" },
};

// 用于样式
import type { SxProps, Theme } from "@mui/material";

const styles: Record<string, SxProps<Theme>> = {
  container: { p: 2 },
  header: { mb: 1 },
};
```

---

### 类型守卫

#### 基本类型守卫

```ts
function isUser(data: unknown): data is User {
  return (
    typeof data === "object" && data !== null && "id" in data && "name" in data
  );
}

// 使用
if (isUser(response)) {
  console.log(response.name); // TypeScript 知道它是 User
}
```

#### 可辨识联合类型

```ts
type LoadingState =
    | { status: 'idle' }
    | { status: 'loading' }
    | { status: 'success'; data: Data }
    | { status: 'error'; error: Error };

function Component({ state }: { state: LoadingState }) {
    // TypeScript 根据 status 收窄类型
    if (state.status === 'success') {
        return <Display data={state.data} />;  // 这里 data 可用
    }

    if (state.status === 'error') {
        return <Error error={state.error} />;  // 这里 error 可用
    }

    return <Loading />;
}
```

---

### 泛型类型

#### 泛型函数

```ts
function getById<T>(items: T[], id: number): T | undefined {
    return items.find(item => (item as any).id === id);
}

// 使用类型推断
const users:  User[] = [... ];
const user = getById(users, 123);  // 类型：User | undefined
```

#### 泛型组件

```ts
interface ListProps<T> {
    items:  T[];
    renderItem: (item: T) => React.ReactNode;
}

export function List<T>({ items, renderItem }: ListProps<T>): React.ReactElement {
    return (
        <div>
            {items.map((item, index) => (
                <div key={index}>{renderItem(item)}</div>
            ))}
        </div>
    );
}

// 使用
<List<User>
    items={users}
    renderItem={(user) => <UserCard user={user} />}
/>
```

---

### 类型断言（谨慎使用）

#### 何时使用

```ts
// ✅ 可以 - 当你比 TypeScript 更了解类型时
const element = document.getElementById("my-element") as HTMLInputElement;
const value = element.value;

// ✅ 可以 - 已验证的 API 响应
const response = await api.getData();
const user = response.data as User; // 你知道数据结构
```

#### 何时不使用

```ts
// ❌ 避免 - 绕过类型安全
const data = getData() as any; // 错误 - 失去了 TypeScript 的作用

// ❌ 避免 - 不安全的断言
const value = unknownValue as string; // 可能实际上不是 string
```

---

### Null/Undefined 处理

#### 可选链

```ts
// ✅ 正确
const name = user?.profile?.name;

// 等价于：
const name = user && user. profile && user.profile.name;
```

#### 空值合并

```ts
// ✅ 正确
const displayName = user?.name ?? "匿名";

// 仅在 null 或 undefined 时使用默认值
// （与 || 不同，|| 在 '', 0, false 时也会触发）
```

#### 非空断言（小心使用）

```ts
// ✅ 可以 - 当你确定值存在时
const data = queryClient.getQueryData<Data>(["data"])!;

// ⚠️ 小心 - 仅在你确定不为 null 时使用
// 更好的做法是显式检查：
const data = queryClient.getQueryData<Data>(["data"]);
if (data) {
  // 使用 data
}
```

---

### 总结

**TypeScript 检查清单：**

- ✅ 启用严格模式
- ✅ 禁止使用 `any` 类型（如需要使用 `unknown`）
- ✅ 函数使用显式返回类型
- ✅ 类型导入使用 `import type`
- ✅ 属性接口添加 JSDoc 注释
- ✅ 使用工具类型（Partial、Pick、Omit、Required、Record）
- ✅ 使用类型守卫进行类型收窄
- ✅ 使用可选链和空值合并
- ❌ 除非必要，否则避免类型断言
