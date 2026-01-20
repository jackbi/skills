---
title: framework-utils | 帮助中心
url: https://doc.magustek.com/%E5%B7%A5%E4%B8%9A%E6%95%B0%E6%8D%AE%E7%AE%A1%E7%90%86%E5%B9%B3%E5%8F%B0/API%E5%B8%AE%E5%8A%A9/%E5%89%8D%E7%AB%AFAPI/framework-utils.html
publishedTime: undefined
---

## framework-utils [​](#framework-utils)

常用工具方法

## 安装 [​](#安装)

bash

```
pnpm add @magustek/framework-utils
```

## flattenTree [​](#flattentree)

树结构数据扁平化

ts

```typescript
function flattenTree<
  K extends string = "children",
  S = Array<TreeNode<PlainObject, K>>,
  T extends S = S,
>(data: T, childten?: K): T;
```

- 使用示例

ts

```javascript
import { flattenTree } from "@magustek/framework-utils";
const treeData = [
  {
    id: "1",
    name: "1",
    children: [{ id: "1.1", name: "1.1", children: [] }],
  },
  { id: "2", name: "2", children: [] },
];
const flatData = flattenTree(treeData, "children");
console.log(flatData);
// 输出
/**
 [
   { id: '1', name: '1', children: []},
   { id: '1.1', name: '1.1', children: []},
   { id: '2', name: '2', children: []},
 ]
 */
```

## useMessage [​](#usemessage)

通用消息提示

- 使用示例

ts

```javascript
import { useMessage } from "@magustek/framework-utils";
const { success, error, warning, info } = useMessage({ duration: 3000 });
success("成功");
error({ message: "错误", duration: 2000 });
warning("警告");
info("提示");

useMessage.success("成功");
useMessage.error({ message: "错误", duration: 2000 });
useMessage.warning("警告");
useMessage.info("提示");
```

## useDateFormat [​](#usedateformat)

日期格式化

ts

```javascript
type timeType = string | number | Date | null | undefined
function useDateFormat(date: timeType, format?: string): string
```

- 使用示例

ts

```javascript
import { useDateFormat } from "@magustek/framework-utils";

const formatData = useDateFormat(Date.now(), "YYYY-MM-DD HH:mm:ss");
```

## useCookies [​](#usecookies)

cookie 相关

[参考链接](https://vueuse.org/integrations/useCookies/)

## useNProgress [​](#usenprogress)

进度条

[参考链接](https://vueuse.org/integrations/useNProgress/)

## commonUtils [​](#commonutils)

ts

```javascript
import { commonUtils } from "@magustek/framework-utils";

const {
  deepCopy,
  toggleClass,
  getQueryVariable,
  getUrlQueryParams,
  closePage,
  queryParams,
  checkUrl,
  flatTree,
  keepTwoDecimalFull,
  getImage,
} = commonUtils;
```

## judgeUtils [​](#judgeutils)

ts

```javascript
import { commonUtils } from '@magustek/framework-utils'
const {
  isEmail,
  isMobile,
  isPhone,
  isURL,
  isString,
  isNumber,
  isBoolean,
  isFunction,
  isNull,
  isUndefined,
  isObj,
  isArray,
  isJSON,
  isDate,
  isRegExp,
  isError,
  isSymbol,
  isPromise,
  isSet,
  isWeiXin,
  isDeviceMobile,
  isQQBrowser,
  isSpider,
  isIos,
  isPC,
  removehtmltag,
  injectScript,
  hasClass,
  addClass,
  removeClass,
  getScrollPosition,
  scrollToTop,
  elementIsVisibleInViewport,
  shuffle,
  copyTextToClipboard,
  isCardID,
  random,
  numberToChinese,
  changeToChinese,
  colorToRGB,
}
```

## validateUtils [​](#validateutils)

ts

```javascript
import { validateUtils } from '@magustek/framework-utils'

const {
  ip,
  url_address,
  domain,
  data_source,
  integer_num,
  phone,
  cn_en_num,
  cn_en_num_point,
  cn_en_num_colon,
  positive_interger_two,
  request_api,
  ip_and_port,
  cn_num,
  en_num,
  en_num_backslash,
  en_num_line,
  en_line,
  cn_en_num_underline,
  en_num_point,
  cn_en_num_line,
  positive_interger,
  cn,
  en,
  cn_en_num_cen_line,
  en_cn,
  en_cn_semicolon,
  all_match,
  en_dot_line,
  number,
  en_common_character,
  pure_en_num,
  email,
  code_validate_general,
  code_validate_strict
}
```

