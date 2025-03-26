---
createTime: 2024-07-10
---

## 1、项目配置

Node：v16.14.2

pnpm：8.0.0

> 使用安装命令可能导致 ESlint 和 Prettier 问题

nvm 设置默认 node 版本

```sh
nvm alias default v16.14.2
```

下载 pnpm

```sh
npm i -g pnpm@8.0.0
```

## vite 创建项目

### 1、创建项目

注意：npm 版本必须在 16+

```sh
pnpm create vite@4.3.2
```

`? Project name: ` 项目名称（文件夹名称）

`? Select a framework:` 项目类型

`? Select a variant: ` 使用的语法

完整日志

```sh
pnpm create vite
.../Library/pnpm/store/v3/tmp/dlx-84199  |   +1 +
Packages are hard linked from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/xiang/Library/pnpm/store/v3
  Virtual store is at:             ../../../Library/pnpm/store/v3/tmp/dlx-84199/node_modules/.pnpm
.../Library/pnpm/store/v3/tmp/dlx-84199  | Progress: resolved 1, reused 1, downloaded 0, added 1, done
✔ Project name: … guigu-zhenxuan-yun
✔ Select a framework: › Vue
✔ Select a variant: › TypeScript

Scaffolding project in /Users/xiang/xiang/study/front-end/guigu-zhenxuan-yun...

Done. Now run:

  cd guigu-zhenxuan-yun
  pnpm install
  pnpm run dev
```

#### 1.1、默认启动

在 package.json 中修改，添加 `--open` 自动打开浏览器

```
  "scripts": {
    "dev": "vite --open",
    ...
    }
```

#### 1.2、Eslint 配置

[eslint 中文官网](http://eslint.cn) ，安装

```sh
pnpm i eslint@^8.40.0 -D
```

生成 eslint 的初始化配置文件

```sh
npx eslint --init
```

问题 1:你要用 ESLint 做啥呢？ 选择 `problems`

```sh
? How would you like to use ESLint? …
  To check syntax only  # 仅仅是检查语法
❯ To check syntax and find problems # 检查语法并且发现错误
```

问题 2:你的项目是啥结构呢 选择 `JavaScript`

```sh
? What type of modules does your project use? …
❯ JavaScript modules (import/export)  # JS
  CommonJS (require/exports) # 没见过
  None of these
```

问题 3:你项目啥框架呢？ 选 `Vue.js`

```sh
? Which framework does your project use? …
  React
❯ Vue.js
  None of these
```

问题 4: 你项目是不是用的 TypeScript ？ 选 `Yes`

```sh
? Does your project use TypeScript? …
  No
❯ Yes
```

问题 5: 你的项目运行在浏览器还是 Node 端？ 选 `Browser`

```sh
? Where does your code run? …  (Press <space> to select, <a> to toggle all, <i> to invert selection)
✔ Browser
  Node
```

> 如果你想要校验 js、ts、vue 的语法需要安装这些插件
>
> ```sh
> The config that you've selected requires the following dependencies:
>
> eslint@9.x, globals, @eslint/js, typescript-eslint, eslint-plugin-vue
> ```
>
> 问题 6：你想现在安装他们嘛？选 `YES`
>
> ```sh
> ✔ Would you like to install them now? · No / Yes
> ```

问题 7 ，你要用什么去安装他们？ 选 `pnpm`

```sh
? Which package manager do you want to use? …
  npm
  yarn
❯ pnpm
  bun
```

#### 1.2.1、eslintignore 忽略文件

创建 `.eslintignore`

```
dist
node_modules
```

#### 1.2.2、运行脚本

package.json 新增两个运行脚本

```json
  "scripts": {
    "lint": "eslint src",
    "fix": "eslint src --fix"
  },
```

### 2、配置 prettier

eslint 和 prettier 保证 js 代码质量

```sh
pnpm install -D eslint-plugin-prettier prettier eslint-config-rettier
```

合格的 Eslint 和 prettier 可以使用 `eslint src` 和 `eslint src --fix` 修复代码格式

### 3、配置 stylelint

[stylelint](https://stylelint.bootcss.com/) 为 css lint 工具，格式化 css 代码，检查 css 语法配置错误，指定 css 书写顺序等

```sh
pnpm add sass sass-loader stylelint postcss postcss-scss postcss-html stylelint-config-prettier stylelint-config-recess-order stylelint-config-recommended-scss stylelint-config-standard stylelint-config-standard-vue stylelint-scss stylelint-order stylelint-config-statndard-scss -D
```

### 4、配置 husky

利用 husky 在代码提交之前触发 git hook，然后执行 ppm run format 做到自动格式化代码

```sh
pnpm install -D husky
```

初始化 husky 环境，会在项目根路径创建 .husky 文件，里面有` pre-commit` 文件，这里面就是 在`git` 提交之前会执行的钩子脚本

```sh
npx husky-init
```

### 5、配置 commitlint

标准化 commit 信息，不能随便写

安装包：

```sh
pnpm add @commitlint/config-conventional @commitlint/cli -D
```

然后添加配置文件 `commitlint.config.cjs`

```
'feat', // 新特性、新功能
'fix',  // 修改bug
'docs', // 文档修改
'style',    // 代码格式修改，不是 css 修改
'refactor', // 代码重构
'perf', // 优化相关，比如性能提升
'test', // 测试用例修改
'chore',    // 其他修改，比如改变构建流程、增加依赖库、工具等
'revert',   // 会滚到上一个版本
'build',    // 编译相关修改，例如版本发布、对项构建或者依赖的改动
```

配置 husky 保证提交的时候能校验住 会生成 `.husky/commit-msg` 文件

```sh
npx husky add .husky/commit-msg
```

在这个文件中配置 `pnpm commitlint`，后续提交的时候， `-m` 必须符合以上添加的配置才行，必须是英文，且冒号后面需要空一格，不能省略

![](images/Pasted%20image%2020240701111305.png)

规范提交：`git commit -m 'feat: 新增git提交规则' ` 这个就可以了

### 6、强制 pnpm 包管理工具

见 `script/preinstall.js` 文件，然后在 `package.json` 中的 `scripts` 添加如下

```
"preinstall": "node ./scripts/preinstall.js",
```

再执行 `yarn add element-plus` 的时候就会报错了

![](images/Pasted%20image%2020240701111932.png)
