# ESLint & Prettier 配置案例

## ESLint 配置

```ts
// eslint.config.js
import stylistic from '@stylistic/eslint-plugin';
import parserTs from '@typescript-eslint/parser';
import vueParser from 'vue-eslint-parser';
import eslintVue from 'eslint-plugin-vue';
import eslintPluginPrettierRecommended from 'eslint-plugin-prettier/recommended';

export default [
  ...eslintVue.configs['flat/recommended'],
  eslintPluginPrettierRecommended,
  {
    plugins: {
      '@stylistic': stylistic,
    },
    files: ['**/*.ts', '**/*.tsx', '**/*.vue'],
    languageOptions: {
      parser: vueParser,
      parserOptions: {
        extraFileExtensions: ['.vue'],
        ecmaFeatures: {
          jsx: true,
          tsx: true,
        },
        ecmaVersion: 2020,
        useJSXTextNode: true,
        sourceType: 'module',
        parser: parserTs,
      },
    },
    ignores: [
      'build',
      'config/*.js',
      'test/*.js',
      ' static/dist',
      'router',
      ' *.cjs',
      'auto-imports.d.ts',
      'components.d.ts',
      'docs/**/*',
    ],
    rules: {
      'prettier/prettier': ['error', {}, { usePrettierrc: true }],
    },
  },
];
```

## Prettier 配置

```js
// prettier.config.cjs
module.exports = {
  singleQuote: true,
  semi: true,
  trailingComma: 'all',
  printWidth: 100,
  tabWidth: 2,
  proseWrap: 'never',
  endOfLine: 'auto',
  // 使用空格缩进行,而不是制表符
  useTabs: false,
  // 更改引用对象属性的时间 可选值"<as-needed|consistent|preserve>"
  quoteProps: 'as-needed',
  // 在JSX中使用单引号而不是双引号
  jsxSingleQuote: false,
  // 在对象文字中的括号之间打印空格
  bracketSpacing: true,
  // 在单独的箭头函数参数周围包括括号 always：(x) => x \ avoid：x => x
  arrowParens: 'always',
  // 这两个选项可用于格式化以给定字符偏移量（分别包括和不包括）开始和结束的代码
  rangeStart: 0,
  rangeEnd: Infinity,
  // 指定要使用的解析器，不需要写文件开头的 @prettier
  requirePragma: false,
  // 不需要自动在文件开头插入 @prettier
  insertPragma: false,
  // 指定HTML文件的全局空格敏感度 css\strict\ignore
  htmlWhitespaceSensitivity: 'ignore',
  // Vue文件脚本和样式标签缩进
  vueIndentScriptAndStyle: false,
  overrides: [
    {
      files: '*.ejs',
      options: {
        parser: 'html',
      },
    },
    {
      files: '*.json',
      options: {
        parser: 'json',
      },
    },
  ],
};
```