# Useless Analyzer Webpack Plugin

这是一个用于分析项目中**未导入代码文件**的 Webpack 插件，支持 Webpack 4 和 5。

This is a Webpack plugin for analyzing **unimported code files** in a project, supporting Webpack 4 and 5.

## 功能特点 / Features

- 支持 Webpack 4 和 5 / Support Webpack 4 and 5
- 内置预设, 开箱即用 / Built-in presets, out of the box
- 灵活可配置 / Flexible and configurable

## 安装 / Installation

```bash
npm install useless-analyzer-webpack-plugin -D
```

## 使用方法 / Usage

> 插件功能仅在 Webpack Build `mode === development` 场景下有效！
>
> Plugin function only works in Webpack Build `mode === development` scenario!

在你的 webpack 配置文件中：

In your webpack config file:

```javascript
const UselessAnalyzerWebpackPlugin = require('useless-analyzer-webpack-plugin')

module.exports = {
  // ...

  plugins: [
    // ...
    new UselessAnalyzerWebpackPlugin({
      // 插件选项 ... / Plugin options ...
    }),
  ],

  // ...
}
```

## 插件选项 / Plugin Options

### 定义 / Definition

```ts
/**
 * @description 插件选项接口定义 / Plugin Options Interface Definition
 */
interface Options {
  /**
   * @description 选项预设，必须是以下选项之一：
   *
   * - `common`: 通用
   * - `webpack`: Webpack 项目
   * - `vue`: Vue 2 项目
   * - `nuxt`: Nuxt 2 项目
   *
   * @description Options preset, must be one of the following:
   *
   * - `common`: For commonly usage
   * - `webpack`: For Webpack project
   * - `vue`: For Vue 2 project
   * - `nuxt`: For Nuxt 2 project
   *
   * @default 'common'
   */
  preset: string

  /**
   * @description 源文件的位置
   *
   * @description Where are the source files located
   *
   * @default 取决于预设 / Depends on preset
   */
  src: string

  /**
   * @description 要忽略的文件，支持 glob 模式，会同预设提供的默认 ignores 合并
   *
   * @description Files to ignore, support glob pattern. These will merge with
   * the default ignores provided by preset
   *
   * @default 取决于预设 / Depends on preset
   */
  ignores: string[]

  /**
   * @description 不允许忽略的文件，支持 glob 模式
   *
   * @description Files that are not allowed to be ignored, support glob pattern
   *
   * @default undefined
   */
  important: string[]

  /**
   * @description 在哪里保存无用的文件报告
   *
   * @description Where to save the useless files report
   *
   * @default './useless/useless.json'
   */
  output: string

  /**
   * @description 是否显示控制台输出
   *
   * @description Whether to show console output
   *
   * @default false
   */
  debug: boolean
}
```

### 预设 / Presets

```js
/**
 * 默认选项。 / Default options.
 */
const DEFAULT_OPTIONS = {
  preset: 'common',
  /**
   * 默认忽略项，会合并到预设忽略项中。 / Default ignores, will be merged with any other preset ignores.
   */
  ignores: [
    // node_modules & 构建输出 / node_modules & build output
    'node_modules/**/*',
    'dist/**/*',
    'build/**/*',
    // 配置文件 / config files
    '*.config.js',
    '*.config.ts',
    '*.config.json',
    '*.config.yaml',
    '*.config.yml',
    '*.config.toml',
    // 工具配置 / tool profiles
    'sonar-project.properties',
    'jsconfig.json',
    // 包管理器文件 / package manager files
    'package.json',
    'package-lock.json',
    'yarn.lock',
    'pnpm-lock.yaml',
    'pnpm-workspace.yaml',
    // 点文件夹和文件 / dot dirs & dot files
    '.*/**/*',
    '**/.*',
    // 文档文件 / documentation files
    '**/*.md',
    '**/*.txt',
    '**/LICENSE',
    // 资源 / resources
    'assets/**/*',
    'public/**/*',
    'static/**/*',
    // 脚本 / scripts
    '**/*.sh',
    '**/*.bat',
    '**/*.ps1',
    'sudo',
    // 非源码文件 / non-source files
    '**/*.d.ts',
    '**/*.map',
    '**/*.min.*',
  ],
  output: '.useless/unused-files.json',
  debug: false,
}

/**
 * 预设选项。 / Preset options.
 */
const PRESET_OPTIONS = {
  /**
   * 通用预设 —— 默认 / Common preset -- Default
   */
  common: {
    src: './',
    ignores: [
      ...DEFAULT_OPTIONS.ignores
    ],
    output: DEFAULT_OPTIONS.output,
    debug: DEFAULT_OPTIONS.debug,
  },

  /**
   * Webpack 预设 / Webpack preset
   */
  webpack: {
    src: './src',
    ignores: [
      ...DEFAULT_OPTIONS.ignores
    ],
    output: DEFAULT_OPTIONS.output,
    debug: DEFAULT_OPTIONS.debug,
  },

  /**
   * Vue 预设 / Vue preset
   */
  vue: {
    src: './src',
    ignores: [
      ...DEFAULT_OPTIONS.ignores
    ],
    output: DEFAULT_OPTIONS.output,
    debug: DEFAULT_OPTIONS.debug,
  },

  /**
   * Nuxt 预设 / Nuxt preset
   */
  nuxt: {
    src: './',
    ignores: [
      ...DEFAULT_OPTIONS.ignores,
      'app/**/*',
      'modules/**/*',
      'router/**/*',
      'app.html',
    ],
    output: DEFAULT_OPTIONS.output,
    debug: DEFAULT_OPTIONS.debug,
  },
}
```

### 示例 / Example

选项 / Options:

webpack.config.js

```js
// TODO: Need example
```

vue.config.js

```js
// TODO: Need example
```

nuxt.config.js

```js
export default {
  // ...

  build: {
    plugins: [
      new UselessAnalyzerWebpackPlugin({
        preset: 'nuxt',
        ignores: [
          // 添加你需要忽略的文件... / Add files you need to ignore...
          '**/*.scss',
        ],
        important: [
          // 添加你不想忽略的文件... / Add files you don't want to ignore...
        ],
      })
    ]
  }

  // ...
}
```

输出 / Output:

<!-- prettier-ignore -->
```json
[
  "assets/styles/old.css",
  "src/components/UnusedComponent.vue",
  "src/api/deprecated.js",
  "src/utils/useless.js"
]
```

<!-- prettier-ignore-end -->

## 注意事项 / Attention

- 插件会在 webpack 构建完成后执行 / The plugin will be executed after the webpack build is complete
- 输出文件中的路径是相对于你设置给 `src` 选项的路径 / The path in the output file is relative to the path you set for the 'src' option

## 发布日志 / Release Note

### v1.x.x

#### v1.0.x

- v1.0.0: Refactor
- v1.0.1: Add ignores
- v1.0.2: Update github repo url
- v1.0.3: Add release note
- v1.0.4 ~ v1.0.5: Add preset option
- v1.0.6: Improving document
- v1.0.7: Add important option & other improvements

#### v2.0.x

- v2.0.0: Remove support of node<=18
- v2.0.1: Change to monorepo
- v2.0.2: Set files field in package.json, to reduce package size
- v2.0.3: Update ignores & documents

## 已知问题 / Known Issues

- [ ] Nuxt 项目中的 `.scss` 检测 / `.scss` files detection in Nuxt project

  描述 / Description：

  Nuxt 项目中，`.vue` 文件导入的 `.scss` 文件误识别为未使用文件 / In the Nuxt project, the `.scss` file imported from the `.vue` file was incorrectly identified as an unused file

  临时方案 / Temporary Solution：

  暂时将 `.scss` 文件添加至排除项 / Temporarily add the `.scss` file to ignores
