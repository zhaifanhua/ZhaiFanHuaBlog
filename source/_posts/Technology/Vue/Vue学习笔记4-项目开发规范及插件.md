---
title:  Vue学习笔记4-项目开发规范及插件
date: 2022-08-18 01:57:15
updated: 2022-08-18 02:13:58
description:  这里对开发规范的配置仅配置ESLint，对代码格式的配置仅配置Prettier，用于代码格式化。
keywords: Vue，前端,学习
cover: https://cdn.zhaifanhua.com/blog/img/202209160000048.png
top_img: https://cdn.zhaifanhua.com/blog/img/202209160000048.png
tags:
  - Vue
categories:
  - Vue
---



# Vue学习笔记4-项目开发规范及插件

## 一、安装插件

开发必备：

- vscode-icons：编辑器图标插件；
- Vue Language Features (Volar)：在功能上 `volar` 和 `vetur` 是一致的，都是针对 `vue` 的插件（可以这样说， `volar` 是 `vue3` 的配套，`vetur` 是 `vue2` 的配套）；
- DotENV：.env 文件语法高亮；
- ESLint：件化的javascript代码检测工具；
- Stylelint：一个强大、先进的 CSS 代码检查器，可以帮助你规避 CSS 代码中的错误并保持一致的编码风格
- Prettier：代码格式化工具；
- EditorConfig for VS Code：项目约束；
- project-tree：生成项目树结构；

其他推荐：

- Chinese (Simplified)：简体翻译插件；
- any-rule：正则表达式插件；
- 会了吧：点击源码文件，会自动分析所有包含的单词，不在 **已掌握单词列表** 中的单词会自动添加到 **陌生单词** 列表；
- GitLens：多人协作开发时，可快速查找 git 提交记录。点击任意一行代码，代码会有个小尾巴，显示本行代码的提交记录。
- Git History：提交记录（Alt + H 查看，安装了 GitLens 可以不用安装）；
- Doxygen Documentation：生成代码文件头和注释；



## 二、基本配置

### 2.1 Yarn

> Yarn自动清除功能,实现每一次`install`之后、`add`之后、`yarn autoclean --force`之后。从程序包依赖项中清除并删除不必要的文件。

#### 2.1.1 配置


```shell
yarn autoclean --init
```

执行玩命令之后，Yarn就会自动在根目录下创建一个`.yarnclean`文件。这样就可以了。

### 2.2 Editor

- [EditorConfig官网](https://editorconfig.org/)
- [EditorConfig完整配置属性说明表](https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties)

在项目根路径新建文件 `.editorconfig`

> 在项目中我们最好是使用统一行尾符（建议不管还是 mac 还是 windows 都使用 lf ），但是按上面的配置，我们发现保存的时候无法将 crlf 行尾符转换成 lf 行尾符，当然我们可以直接点击 vscode 的右下角切换行尾符，但终究是有点麻烦，这时使用 .editorconfig 就很有必要了。

```ini
# 告诉EditorConfig插件，这是根文件，不用继续往上查找。
root = true

# 匹配全部文件。
[*]
# 使用`utf-8`字符集。
charset=utf-8
# 结尾换行符，可选`lf`、`cr`、`crlf`。
end_of_line=lf
# 在文件结尾插入新行
insert_final_newline=true
# 缩进的样式为空格。
indent_style=space
# 缩进为2
indent_size=2
# 行最大长度为100
max_line_length = 100

# 匹配以`.yml`、`.yaml`、`.json`结尾的文件
[*.{yml,yaml,json}]
indent_style = space
indent_size = 2

# 匹配以`.md`结尾的文件
[*.md]
# 修剪尾随空格
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

在项目根目录看有没有 `.vscode` 文件夹，若没有，就新建。新建 `settings.json` 文件：

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "editor.formatOnSave": true
}
```

一键安装项目推荐的vscode插件，新建 `extensions.json` 文件：

```json
{
  "recommendations": [
    "vue.volar",
    "ms-ceintl.vscode-language-pack-zh-hans",
    "mikestead.dotenv",
    "donjayamanne.githistory",
    "lokalise.i18n-ally",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "zhucy.project-tree",
    "eamodio.gitlens",
    "ms-vscode.powershell",
    "vscode-icons-team.vscode-icons"
  ]
}
```

团队其他成员拉代码后, 打开 vscode, 依次点击1,2,3, 会自动输入@recommended, 工作区推荐的插件就是 `.vscode/extensions.json` 文件推荐的。


![image-20220818103040910.png](https://cdn.zhaifanhua.com/blog/img/202209160000048.png)

### 2.3 Git

提交以上文件到 git 代码仓库，在 `.gitignore` 文件中配置：

```
node_modules
.DS_Store
dist
.cache
.turbo
.local
# local env files
.env.local
.env.*.local
.eslintcache

# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# Editor directories and files
.idea
# .vscode
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

package-lock.json
pnpm-lock.yaml

.history
```

## 三、插件配置

### 3.1 TypeScript

#### 3.1.1 安装

```shell
yarn add -D typescript
```

- typescript

#### 3.1.2 配置

根目录下新建 TypeScript 的配置文件：`tsconfig.json`文件

```json
{
  "compilerOptions": {
    // 指定ECMAScript目标版本，esnext为最新版本
    "target": "esnext",
    // 指定生成哪个模块系统代码，esnext为最新版本
    "module": "esnext",
    // 决定如何处理模块
    "moduleResolution": "node",
    // 启用所有严格类型检查选项
    "strict": true,
    // 禁止对同一个文件的不一致的引用
    "forceConsistentCasingInFileNames": true,
    // 允许从没有设置默认导出的模块中默认导入这并不影响代码的输出，仅为了类型检查
    "allowSyntheticDefaultImports": true,
    // 禁用函数参数双向协变检查
    "strictFunctionTypes": false,
    // 在 .tsx文件里支持JSX
    "jsx": "preserve",
    // 解析非相对模块名的基准目录查看 模块解析文档了解详情
    "baseUrl": ".",
    // 允许编译javascript文件
    "allowJs": true,
    // 生成相应的 .map文件
    "sourceMap": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    // 若有未使用的局部变量则抛错
    "noUnusedLocals": true,
    // 若有未使用的参数则抛错
    "noUnusedParameters": true,
    // 启用实验性的ES装饰器
    "experimentalDecorators": true,
    // 编译过程中需要引入的库文件的列表
    "lib": ["dom", "esnext"],
    // 要包含的类型声明文件名列表
    "types": ["vite/client"],
    // 要包含的类型声明文件路径列表
    "typeRoots": ["./node_modules/@types/", "./types"],
    "incremental": true,
    // 在表达式和声明上有隐含的 any类型时报错
    "noImplicitAny": false,
    // 忽略所有的声明文件（ *.d.ts）的类型检查
    "skipLibCheck": true,
    // 模块名到基于 baseUrl的路径映射的列表查看 模块解析文档了解详情
    "paths": {
      "/@/*": ["src/*"],
      "/#/*": ["types/*"]
    }
  },
  // 指定一个匹配列表(属于自动指定该路径下的所有ts相关文件)
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "types/**/*.d.ts",
    "types/**/*.ts",
    "mock/**/*.ts"
  ],
  // 指定一个排除列表(include的反向操作)
  "exclude": ["node_modules", "dist", "**/*.js"]
}
```

### 3.2 ESLint

> `ESLint`简单的来说就是去判断你的`JS`代码写的格式对不对的一个依赖。没有它你的代码也能运行，有了它你的代码可以写的更漂亮。`ESLint`还支持插件，第三方框架会基于`ESLint`写出自己的代码检查插件。比如`Vue3`对应`eslint-plugin-vue`。

- [ESLint官网](https://eslint.org/)
- [ESLint-快速入门](https://eslint.org/docs/user-guide/getting-started)
- [ESLint配置](https://eslint.org/docs/user-guide/configuring/)
- [ESLint的配置规则](https://eslint.org/docs/rules/)

#### 3.2.1 安装

```shell
yarn add -D eslint eslint-define-config eslint-plugin-vue vue-eslint-parser @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

- eslint：判断代码是否符合规则；
- eslint-define-config：大部分开发者使用的默认规则；
- eslint-plugin-vue：`vue`官方开发的`ESLint`插件；
- vue-eslint-parser：允许对.vue文件的<template>进行检查，和 eslint-plugin-vue 搭配使用；
- @typescript-eslint/eslint-plugin：与eslint-plugin-vue同理；
- @typescript-eslint/parser：与vue-eslint-parser同理；

#### 3.2.2 配置

在项目根目录新建 ESLint 风格检查配置文件：`.eslintrc.cjs`

> 这里可以使用`eslint`的`init`,eslint --init` 或者 `npx eslint --init


```js
import { defineConfig } from 'eslint-define-config';

export default defineConfig({
  root: true,
  /* 指定如何解析语法。*/
  parser: 'vue-eslint-parser',
  /* 优先级低于parse的语法解析配置 */
  parserOptions: {
    parser: '@typescript-eslint/parser',
    //模块化方案
    sourceType: 'module',
  },
  env: {
    browser: true,
    es6: true,
    node: true,
    // 解决 defineProps and defineEmits generate no-undef warnings
    'vue/setup-compiler-macros': true,
  },
  // https://eslint.bootcss.com/docs/user-guide/configuring#specifying-globals
  globals: {},
  extends: [
    'prettier',
    'eslint:recommended',
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended', // typescript-eslint推荐规则,
    'plugin:prettier/recommended',
    './.eslintrc-auto-import.json',
  ],
  // 自定义ESLint检查规则
  rules: {
    // 禁止使用 var
    'no-var': 'error',
    semi: 'off',
    // 优先使用 interface 而不是 type
    '@typescript-eslint/consistent-type-definitions': ['error', 'interface'],
    '@typescript-eslint/no-explicit-any': 'off', // 可以使用 any 类型
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    // 解决使用 require() Require statement not part of import statement. 的问题
    '@typescript-eslint/no-var-requires': 0,
    // https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/ban-types.md
    '@typescript-eslint/ban-types': [
      'error',
      {
        types: {
          // add a custom message to help explain why not to use it
          Foo: "Don't use Foo because it is unsafe",

          // add a custom message, AND tell the plugin how to fix it
          String: {
            message: 'Use string instead',
            fixWith: 'string',
          },

          '{}': {
            message: 'Use object instead',
            fixWith: 'object',
          },
        },
      },
    ],
    // 禁止出现未使用的变量
    '@typescript-eslint/no-unused-vars': [
      'error',
      { vars: 'all', args: 'after-used', ignoreRestSiblings: false },
    ],
    'prettier/prettier': ['error', { singleQuote: true, parser: 'flow', semi: false }],
    'vue/html-indent': 'off',
    // 关闭此规则 使用 prettier 的格式化规则，
    'vue/max-attributes-per-line': ['off'],
    // 优先使用驼峰，element 组件除外
    'vue/component-name-in-template-casing': [
      'error',
      'PascalCase',
      {
        ignores: ['/^el-/', '/^router-/'],
        registeredComponentsOnly: false,
      },
    ],
    // 强制使用驼峰
    camelcase: ['error', { properties: 'always' }],
    // 优先使用 const
    'prefer-const': [
      'error',
      {
        destructuring: 'any',
        ignoreReadBeforeAssign: false,
      },
    ],
  },
});

```

在项目根目录新建 ESLint 的代码检测忽略的文件的配置文件：`.eslintignore`

```ini
*.sh
node_modules
*.md
*.woff
*.ttf
.vscode
.idea
dist
/public
/docs
.husky
.local
/bin
Dockerfile
```

### 3.3 Prettier

#### 3.3.1 安装

```shell
yarn add -D prettier eslint-plugin-prettier eslint-config-prettier
```

- prettier：代码格式化，一般IDE都有prettier的插件，在保存的时候格式化代码。
- eslint-plugin-prettier：不同的程序员有不同的IDE，不同的IDE有不同的`prettier`插件。但对于项目来说只有一个`eslint-plugin-prettier`，即统一格式；
- eslint-config-prettier：`prettier`和`ESLint`之间有些规则不一样，为解决冲突，`eslint-config-prettier`将`prettier`一些规则默认关闭了。

#### 3.3.2 配置 

在项目根目录新建 Prettier 的代码格式化配置文件：`.prettierrc.cjs`

```js
module.exports = {
  // 超过最大值换行
  printWidth: 100,
  // 缩进字节数
  tabWidth: 2,
  // 缩进不使用tab，使用空格
  useTabs: false,
  // 句尾添加分号
  semi: true,
  vueIndentScriptAndStyle: true,
  // 使用单引号代替双引号
  singleQuote: true,
  quoteProps: 'as-needed',
  // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
  bracketSpacing: true,
  // 在对象或数组最后一个元素后面是否加逗号(在ES5中加尾逗号)
  trailingComma: 'es5',
  jsxSingleQuote: false,
  //  (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
  arrowParens: 'avoid',
  insertPragma: false,
  requirePragma: false,
  // 默认值。因为使用了一些折行敏感型的渲染器(如GitHub comment)而按照markdown文本样式进行折行
  proseWrap: 'preserve',
  htmlWhitespaceSensitivity: 'strict',
  // 结尾
  endOfLine: 'lf',
  rangeStart: 0,
};
```

在项目根目录新建 Prettier 的代码检测忽略的文件的配置文件：`.prettierignore`

```ini
/dist/*
.local
.output.js
/node_modules/**

**/*.svg
**/*.sh

/public/*
```

### 3.4 StyleLint

#### 3.4.1 安装

```shell
yarn add -D stylelint stylelint-config-standard stylelint-config-prettier stylelint-order
```

- stylelint：是对我们编写的样式进行检查的插件；
- stylelint-config-standard：是`stylelint`扩展的检查标准库；
- stylelint-config-prettier：是用来解决冲突的（参考之前的`prettier`的 eslint-config-prettier）；
- stylelint-order：是检查我们样式编写顺序的；

#### 3.4.2 配置

在项目根目录新建 StyleLint的代码格式化配置文件：`.stylelintrc.cjs`

```js
module.exports = {
  root: true,
  // 插件
  plugins: ['stylelint-order'],
  // 扩展
  extends: ['stylelint-config-standard', 'stylelint-config-prettier', 'stylelint-config-standard-scss', 'stylelint-config-recommended-vue'],
  // Unknown word (CssSyntaxError) 错误,这个问题主要是因为 stylelint 升级到 14 大版本造成的,解决办法是安装 stylelint-config-recommended-scss 插件
  customSyntax: 'postcss-html',
  overrides: [
    {
      files: ['**/*.{scss,css,sass}'], // css 相关文件由 postcss-scss 处理
      customSyntax: 'postcss-scss'
    },
  ],
  // 自定义规则
  rules: {
    // 禁止使用未知的伪类选择器
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['global'],
      },
    ],
    // 禁止使用未知规则
    'at-rule-no-unknown': [
      true,
      {
        ignoreAtRules: ['function', 'if', 'each', 'include', 'mixin'],
      },
    ],
    // 禁止空来源
    'no-empty-source': null,
    // 禁止使用无效的命名网格区域
    'named-grid-areas-no-invalid': null,
    // 要求或不允许使用Unicode字节顺序标记
    'unicode-bom': 'never',
    // 禁止较低特异性的选择器在覆盖较高特异性的选择器之后出现
    'no-descending-specificity': null,
    // 禁止在字体系列名称列表中缺少通用系列
    'font-family-no-missing-generic-family-keyword': null,
    // 在声明的冒号后面需要一个空格或禁止空格
    'declaration-colon-space-after': 'always-single-line',
    // 在声明的冒号之前需要一个空格或禁止使用空格
    'declaration-colon-space-before': 'never',
    // 在声明块内要求或不允许尾随分号
    'declaration-block-trailing-semicolon': 'always',
    // 在规则之前要求或禁止使用空行
    'rule-empty-line-before': [
      'always',
      {
        ignore: ['after-comment', 'first-nested'],
      },
    ],
    // 禁止使用未知单位
    'unit-no-unknown': [true, { ignoreUnits: ['rpx'] }],
    // Specify the alphabetical order of the attributes in the declaration block
    // 样式顺序
    'order/properties-order': [
      'position',
      'top',
      'right',
      'bottom',
      'left',
      'z-index',
      'display',
      'float',
      'width',
      'height',
      'max-width',
      'max-height',
      'min-width',
      'min-height',
      'padding',
      'padding-top',
      'padding-right',
      'padding-bottom',
      'padding-left',
      'margin',
      'margin-top',
      'margin-right',
      'margin-bottom',
      'margin-left',
      'margin-collapse',
      'margin-top-collapse',
      'margin-right-collapse',
      'margin-bottom-collapse',
      'margin-left-collapse',
      'overflow',
      'overflow-x',
      'overflow-y',
      'clip',
      'clear',
      'font',
      'font-family',
      'font-size',
      'font-smoothing',
      'osx-font-smoothing',
      'font-style',
      'font-weight',
      'hyphens',
      'src',
      'line-height',
      'letter-spacing',
      'word-spacing',
      'color',
      'text-align',
      'text-decoration',
      'text-indent',
      'text-overflow',
      'text-rendering',
      'text-size-adjust',
      'text-shadow',
      'text-transform',
      'word-break',
      'word-wrap',
      'white-space',
      'vertical-align',
      'list-style',
      'list-style-type',
      'list-style-position',
      'list-style-image',
      'pointer-events',
      'cursor',
      'background',
      'background-attachment',
      'background-color',
      'background-image',
      'background-position',
      'background-repeat',
      'background-size',
      'border',
      'border-collapse',
      'border-top',
      'border-right',
      'border-bottom',
      'border-left',
      'border-color',
      'border-image',
      'border-top-color',
      'border-right-color',
      'border-bottom-color',
      'border-left-color',
      'border-spacing',
      'border-style',
      'border-top-style',
      'border-right-style',
      'border-bottom-style',
      'border-left-style',
      'border-width',
      'border-top-width',
      'border-right-width',
      'border-bottom-width',
      'border-left-width',
      'border-radius',
      'border-top-right-radius',
      'border-bottom-right-radius',
      'border-bottom-left-radius',
      'border-top-left-radius',
      'border-radius-topright',
      'border-radius-bottomright',
      'border-radius-bottomleft',
      'border-radius-topleft',
      'content',
      'quotes',
      'outline',
      'outline-offset',
      'opacity',
      'filter',
      'visibility',
      'size',
      'zoom',
      'transform',
      'box-align',
      'box-flex',
      'box-orient',
      'box-pack',
      'box-shadow',
      'box-sizing',
      'table-layout',
      'animation',
      'animation-delay',
      'animation-duration',
      'animation-iteration-count',
      'animation-name',
      'animation-play-state',
      'animation-timing-function',
      'animation-fill-mode',
      'transition',
      'transition-delay',
      'transition-duration',
      'transition-property',
      'transition-timing-function',
      'background-clip',
      'backface-visibility',
      'resize',
      'appearance',
      'user-select',
      'interpolation-mode',
      'direction',
      'marks',
      'page',
      'set-link-source',
      'unicode-bidi',
      'speak',
    ],
  },
  // 忽略检查的文件
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts'],
};
```

在项目根目录新建 StyleLint的代码检测忽略的文件的配置文件：`.stylelintignore`

```ini
/dist/*
/public/*
```

### 3.5 PostCSS

#### 3.5.1 安装

```shell
yarn add -D postcss autoprefixer
```

- postcss：编写一般的样式，渲染页面的`dom`节点有面向各种浏览器厂商的样式；
- autoprefixer：postcss 的一个插件；

#### 3.5.2 配置

在项目根目录新建 PostCSS 的代码格式化配置文件：`.postcssrc.cjs`

```js
module.exports = {
  plugins: {
    autoprefixer: {},
  },
};
```

### 3.6 rimraf

> 由于手动删除文件夹太慢了，需要实现脚本删除文件。

#### 3.6.1 安装

```shell
yarn add -D rimraf
```

### 3.7 npm-check-updates

检查当前项目中有没有哪些依赖包可更新（简写ncu）

#### 3.7.1 安装

```
npm install -g npm-check-updates
```

全部更新package.json

```
ncu -u
```

