## 前言
随着自己工作时间与经验的积累，小伙伴们是不是想让自己的技术更上一层楼，开始考虑考虑管理团队，或者说为团队的基建出把力，增加自己的核心竞争力，但是身在小公司的我，如何拥于一套企业级别的前端开发规范呢？我也是遇到同样的问题，于是着手设计开发一套前端开发规范，在这里做下笔记记录，有需要的可以一起学习下！

## 项目技术栈
> [Vite](https://vitejs.cn/)、[eslint](https://eslint.nodejs.cn/)、[prettier](https://www.prettier.cn/)、[husky](https://typicode.github.io/husky/#/)、[commitlint](https://commitlint.js.org/#/)、[lint-staged](https://www.npmjs.com/package/lint-staged)、[stylelint](https://stylelint.io/)

> node v18.17.1   pnpm v8.1.1

[项目代码](url)

## 使用`Vite`初始化项目

```js
# pnpm pnpm create vite my-vue-app --template vue

cd my-vue-app

pnpm i

pnpm run dev

```

## 加入代码的校验
现在进入正题，上面的项目就是你工作中的项目，然后我们现在对项目进行改造，加入我们所说的规范

### `eslint` 校验 `prettier` 自动格式化
1. 安装`vscode`插件
![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1d103a21b26429285bb8b49687882be~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=650&h=156&e=png&b=1e1e1e)
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3dc56e53debc430c95f50a12bd548f69~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=649&h=139&e=png&b=1e1e1e)

2. 下载对应依赖

`pnpm install eslint eslint-plugin-vue eslint-config-prettier prettier eslint-plugin-import eslint-plugin-prettier eslint-config-airbnb-base -D` 


| eslint | prettier| eslint-config-airbnb-base | eslint-config-prettier| eslint-plugin-vue| eslint-config-airbnb-base | eslint-plugin-prettier| 
| --- | --- |--- | --- |--- |--- | --- |
|ESLint的核心库|  prettier格式化代码的核心库 | airbnb的代码规范（依赖plugin-import）| eslint结合prettier的格式化 | eslint在vue里的代码规范 | 项目里面支持eslint | 将prettier结合进去eslint的插件 |

3. 配置script脚本，项目安装`eslint`配置

```js
 # pageage.json --->scripts
"lint:create": "eslint --init"`

# 执行npm run lint:create

```
执行这个`npm run lint:create` 是一个交互式命令，需要我们根据提示做出选择，我们选择如下

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/352a7a908c5f49a8a73334a23ac2f922~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1268&h=634&e=png&b=181818)

这个时候页面上会出现`.eslintrc.cjs`文件

4. 安装项目运行的所需的依赖

`pnpm install typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-import-resolver-alias @types/eslint @types/node -D`

| @typescript-eslint/parser | @typescript-eslint/eslint-plugin| eslint-config-airbnb-base |
| --- | --- |--- |
|eslint解析器，解析typescript，检查规范typescript代码|  eslint插件，包含了各类定义好的检测typescript代码的规范 | airbnb的代码规范（依赖plugin-import）| 


### `eslintrc`文件的修改

**因为`eslint`是 node 工具，所以文件名是`.cjs`结尾(commonjs 规范)——对应 的`.mjs`就是 ES Module 规范**
这个时候我们将`.eslintrc.cjs`文件中的内容替换成一下内容，每行代码都有注释，可以自行阅读

```js
module.exports = {
	// 环境:
	env: {
		// 浏览器
		browser: true,
		// 最新es语法
		es2021: true,
		// node环境
		node: true,
	},
	// 扩展的eslint规范语法，可以被继承的规则
	// 字符串数组：每个配置继承它前面的配置
	// 分别是：
	// eslint-plugin-vue提供的
	// eslint-config-airbnb-base提供的
	// eslint-config-prettier提供的
	// 前缀 eslint-config-, 可省略
	extends: [
		'plugin:vue/vue3-strongly-recommended',
		'airbnb-base',
		'prettier'
	],
	// eslint 会对我们的代码进行检验
	// parser的作用是将我们写的代码转换为ESTree（AST）
	// ESLint会对ESTree进行校验
	parser: 'vue-eslint-parser',
	// 解析器的配置项
	parserOptions: {
		// es的版本号，或者年份都可以
		ecmaVersion: 'latest',
		parser: '@typescript-eslint/parser',
		// 源码类型 默认是script，es模块用module
		sourceType: 'module',
		// 额外的语言类型
		ecmaFeatures: {
			tsx: true,
			jsx: true,
		},
	},
	// 全局自定义的宏，这样在源文件中使用全局变量就不会报错或者警告
	globals: {
		defineProps: 'readonly',
		defineEmits: 'readonly',
		defineExpose: 'readonly',
		withDefault: 'readonly',
	},
	// 插件
	// 前缀 eslint-plugin-, 可省略
	// vue官方提供了一个ESLint插件 eslint-plugin-vue，它提供了parser和rules
	// parser为 vue-eslint-parser，放在上面的parsr字段，rules放在extends字段里，选择合适的规则
	plugins: [
		'vue',
		'@typescript-eslint'
	],
	settings: {
		// 设置项目内的别名
		'import/reslover': {
			alias: {
				map: [
					['@', './src']
				],
			},
		},
		// 允许的扩展名
		'import/extensions': ['.js', '.jsx', '.ts', '.tsx', '.mjs'],
	},
	// 自定义规则，覆盖上面extends继承的第三方库的规则，根据组内成员灵活定义
	rules: {
		'import/no-extraneous-dependencies': 0,
		'no-param-reassing': 0,
		'vue/multi-word-commponent-names': 0,
		'vue/attribute-hyphenation': 0,
		'vue/v-on-event-hyphenation': 0,
	},
};
```

修改`package.json`添加一个脚本命令

`"lint": "eslint \"src/**/*.{js,vue,ts}\" --fix"`

### 运行eslint的脚本进行校验

这个时候我们可以对我们上面写的规则进行校验了，回到我们新建项目中的`Helloword.vue`中加入如下代码

```js

<script setup lang="ts">
import { ref } from 'vue'

defineProps<{ msg: string }>()

const count = ref(0)
// 改写的代码
const add = () => {
  count.value++
}
</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button type="button" @click="add">count is {{ count }}</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
      >create-vue</a
    >, the official Vue + Vite starter
  </p>
  <p>
    Install
    <a href="https://github.com/vuejs/language-tools" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>

```
这个时候我们就能看到eslint的插件给我们做出提示，告诉我们不能不用`+ +`

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/524793b61f9347aabd334c713c2e50b5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=683&h=238&s=27368&e=png&b=202020)

然后运行刚刚配置的脚本

`pnpm run lint`



![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/80f67b4d434742b2ac57957c946a4154~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=742&h=130&s=12899&e=png&b=181818)

也给出了对应的报错，我们更改一下代码


```js
const add = () => {
  count.value += 1
}
```
这个时候报错也就消失了

## `Vite`与`eslint`的结合
`pnpm install vite-plugin-eslint -D`

vite的一个插件，让项目可以方便的得到eslint支持，完成eslint配置后，可以快速的将其集成进vite之中，便于在代码不符合eslint规范的第一时间看到提示


```js
// vite.config.ts

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import eslintPlugin from 'vite-plugin-eslint'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),eslintPlugin()],
})
```

## 修改添加配置文件
1. 新增`.eslintignore`文件
  
```js
# 忽略 shell 脚本文件
*.sh

# 忽略 node_modules 目录
node_modules

# 忽略 Markdown 文件
*.md

# 忽略字体文件
*.woff
*.ttf

# 忽略 Visual Studio Code 配置
.vscode

# 忽略 IntelliJ IDEA 配置
.idea

# 忽略分发目录
dist

# 忽略 public 目录
/public

# 忽略文档目录
/docs

# 忽略 Husky 配置
.husky

# 忽略二进制文件
/bin

# 忽略 ESLint 配置文件
.eslintrc.js

# 忽略 Prettier 配置文件
prettier.config.js

# 忽略 /src 下的模拟文件
/src/mock/*

# 忽略日志目录
logs

# 忽略日志文件
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# 忽略 macOS 的 DS_Store 文件
.DS_Store

# 忽略 dist-ssr 目录
dist-ssr

# 忽略本地文件
*.local

# 忽略 Cypress 视频和截图
/cypress/videos/
/cypress/screenshots/

# 忽略 Visual Studio 特定文件
.suo
*.njsproj
*.sln
*.sw?

# 忽略 TypeScript 声明文件
components.d.ts

```

2.  新增`.prettiercjs`文件


```js
{
  "printWidth": 80,
  "tabWidth": 9,
  "useTabs": true,
  "semi": false,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "jsxSingleQuote": false,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "arrowParens": "always",
  "requirePragma": false,
  "insertPragma": false,
  "proseWrap": "always",
  "htmlWhitespaceSensitivity": "css",
  "endOfLine": "lf"
}
```
由于JSON中不支持注释，这里给大家做个注释，复制使用的话用上面的代码就行

```js
# Prettier 配置文件解释

printWidth: 一行最多多少字符
tabWidth: 使用9个空格缩进
useTabs: 使用制表符（tab）进行缩进
semi: 行尾不需要分号
singleQuote: 使用单引号
quoteProps: 对象的key仅在必要时使用引号
jsxSingleQuote: jsx不使用单引号，而使用双引号
trailingComma: 尾随逗号，适用于ES5
bracketSpacing: 大括号内的收尾需要空格
arrowParens: 箭头函数，只有一个参数的时候，也需要括号
requirePragma: 不需要写文件开头的@prettier
insertPragma: 不需要自动在文件开头插入@prettier
proseWrap: 使用默认的折行标准
htmlWhitespaceSensitivity: 根据显示样式决定HTML要不要折行
endOfLine: 换行符使用lf

```
3. 新增`.prettierignore`文件

对不需要进行风格格式化的文件进行忽略

```js
/dist/*
.local
.output.js
/node_modules/**
src/.DS_Store

**/*.svg
**/*.sh

/public/*
components.d.ts
```

上面的这些配置我们就基本把`eslint`与`prettier`结合在一起了，我们现在来测试一下

我们在`vscode`的设置文件`setting.json`中配置下面代码。当然不配置也可以，我们后面会把这些配置文件以一个单文件的形式存在我们的项目根目录中，这样无论团队中的小伙伴的配置什么什么样的都能保证我们`git`仓库中的代码是一致的，我们继续


```js
"[vue]":{
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode"
},
```

这个时候我们点击`Ctrl + S`保存后我们的代码就会按照`prettiercjs`中的配置进行格式化，但是为了更加方便是使用，我们可以在`package.json`设置一个脚本配置,用命令替我们进行保存

```js
# package.json

"prettier-format": "prettier --config .prettierrc  \"src/**/*.{vue,js,ts}\" --write"

# prettier --config .prettierrc 找到.prettierrc 文件作为prettier的配置文件
#  \"src/**/*.{vue,js,ts}\"  格式化src目录下以.vue .js .ts 结尾的文件
# --write 表示写入

```

这个时候我们执行`pnpm run prettier-format`命令就会自动帮我们进行格式化操作

4. `tsconfig.json`的配置

这里是ts的一些配置，大家可以直接复制使用，具体的意思就是ts方面的知识的，这里不做赘述


```js
{
	"compilerOptions": {
		// 指定es的目标版本
		"target": "ESNext",
		"useDefineForClassFields": true,
		"module": "ESNext",
		// 决定如何处理模块
		"moduleResolution": "node",
		"strict": true,
		"jsx": "preserve",
		"sourceMap": true,
		"resolveJsonModule": true,
		"isolatedModules": true,
		"esModuleInterop": true,
		// 编译过程中需要引入的库文件的列表
		"lib": ["ESNext", "DOM", "DOM.Iterable"],
		// 默认所有可见的"@types"包会在编译过程中被包含进来
		"types": ["vite/client"],
		"skipLibCheck": true,
		"noEmit": true,
		// 解析非相对模块名的基准目录
		"baseUrl": ".",
		// 模块名到基于baseurl的路径映射的列表
		"paths": {
			"@/": ["src/"],
			"*.ts": ["*"]
		}
	},
	"include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
	"references": [{ "path": "./tsconfig.node.json" }]
}
```

## 相关配置与`git`相结合

我们上面做的很多的配置，但是都需要我们手动的敲命令才能帮我们进行格式化的校验，这样其实也不是很方便，于是这里我们就把它跟`git`做出关联。在你对本地代码进行git操作时就会触发我们上述写的脚本命令，这样我们就不用关心太多，只需要看我们的代码能不能提交到仓库就行，根据报错信息进行修改即可，开搞！！！

### Husky

[husky](https://typicode.github.io/husky/#/)是一个未 git 客户端增加 hook 的工具，在一些 git 操作之前自动触发的函数，如果我们希望在检测错误的同时，自动修复`eslint`语法错误，就可以通过后面钩子实现

首先我们在使用`Husky`之前需要保证我们是项目存在一个仓库的

1. `git init`

2. 下载`husky`

`pnpm install husky -D`

3. `package.json`中配置一个脚本命令

`"prepare": "husky install"`

4 执行脚本

`pnpm run prepare`

这个时候项目根目录就会出现一个文件夹

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/239f2490b907454cac0ad39598f83b84~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=213&h=112&s=4652&e=png&b=222222)

5. 添加`git hook`

这里需要用到`lint-staged`主要过滤出 git 代码暂存区（被 git add 的文件）的工具，将所有暂存文件 的列表传递给任务
     
 `pre-commit`添加的是lint-staged，去对git暂存区的代码做一些格式化的操作
 
 下载`lint-staged
 
 `pnpm install lint-staged -D`
 
 执行`npx husky add .husky/pre-commit "npx lint-staged"`
 
 6. 放入`lint-staged`脚本命令
 
 
```js
# package.json

"lint-staged": {
    "*.{js,jsx,vue,ts,tsx}": [
      "npm run lint",
      "npm run prettier-format"
    ]
  }
 
```

上述代码的意思就是说在我们执行`git commit -m ''`命令之前执行`pnpm run lint` `pnpm run prettier-format` 这两个命令就是我们上面大费周章进行配置的

当我们执行`git add .` 以及 `git commit -m '暂时随便写` 后，如果我们有语法错误是无法进行`commit`的，并提示报错信息，如下

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/20f161416f5d4059b3c8a37e4e3acb6a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=903&h=154&s=15906&e=png&b=181818)

 ### commitlint
`commitlint`是对我们 `git commit` 提交的注释进行校验的工具,良好的注释有利于我们对版本以及功能的控制，会让仓库代码更加清晰，下面是我常用的主题规范

| type | 说明 |
| --- | --- |
| feat | 新的功能 |
| fix | Bug的修复 |
| docs | 仅改变说明文档 |
| update | 更新某个功能 |
| style | 代码格式的美化（formatting） |
| refactor | 重构（没有feat和fix操作） |
| perf | 性能优化 |
| test | 增加单元测试、修改当前单元测试 |
| build | 项目构建脚本或者外部依赖的改动（webpack、npm等） |
| revert | 撤销commit，回滚到上一个版本 |


```js
# 举个例子 git commit -am "feat: 完成分页功能"
```

下载校验工具

`pnpm install @commitlint/config-conventional @commitlint/cli -D`

执行命令

`npx husky add .husky/commit-msg "npx --no -- commitlint --edit ${1}"`

项目根目录多出一个文件

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/30655bcb8a5e4cbebfc27504f71b1e5c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=409&h=133&s=6181&e=png&b=181818)


根目录新建一个`commitlint.config.cjs`文件

```js
module.exports = {
	extends: ['@commitlint/config-conventional'],
	rules: {
		'type-enum': [
			2,
			'always',
			[
				// 编译相关的修改，例如发布版本，对项目构建或者依赖的改动
				'build',
				// 新功能(feature)
				'feat',
				// 修复bug
				'fix',
				// 更新某功能
				'update',
				// 重构
				'refactor',
				// 文档
				'docs',
				// 构建过程或者辅助工具的变动,如增加依赖库等
				'chore',
				// 不影响代码运行的变动
				'style',
				// 撤销commit,回滚到上一个版本
				'revert',
				// 性能优化
				'perf',
				// 测试(单元,集成测试)
				'test',
			],
		],
		'type-case': [0],
		'type-empty': [0],
		'scope-empty': [0],
		'scope-case': [0],
		'subject-full-stop': [0, 'never'],
		'subject-case': [0, 'never'],
		'header-max-length': [0, 'always', 74],
	},
};
```

我们执行`git commit -m '做测试，随便写'`这个时候控制台就会报错，告诉我们缺少主题

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f2e4ee57ddb4286a37f234a1cb208c7~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1020&h=208&s=26680&e=png&b=181818)

我们需要为其添加一个主题

`git commit -m 'fix:修改懒加载逻辑问题'`

这样就正常提交了

`git hook的总结`
-   `pre-commit`：由 `git commit` 调用，在 `commit` 之前执行
-   `commit-msg`：由` git commit` 或 `git merge` 调用
-   `pre-merge-commit`：由 `git merge` 调用，在 `merge` 之前执行
-  ` pre-push`：被 `git push` 调用，在 `git push` 之前执行，防止进行推送

### `Stylelint`钩子

[Stylelint](<https://stylelint.io/user-guide/get-started>) 是CSS 检查器(linter)，帮助我们规避 CSS 代码中的错误并保持一致的编码风格

1. 下载`stylelint stylelint-config-standard`

`pnpm install stylelint stylelint-config-standard -D`

2.  安装`vscode`插件，`Stylelint`

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3feeb8bc7bca4b7aab81ffebfb1d70cd~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=701&h=150&s=17637&e=png&b=1b1b1b)

3. 修改`settings.json`，添加下面代码

这里后面会统一设置一个文件保证团队配置一致

```js
#settings.json
{
	"editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true
  },
	"stylelint.validate": [
		"css",
		"less",
		"scss",
		"vue"
	],
}
```
这是一个标准样式库，也可以自动添加一些样式规则在`stylelintrc.cjs`文件里面
4. 新建`.stylelintrc.cjs`

```js
module.exports = {
	extends: ['stylelint-config-standard'],
};
```

5.执行 `npx stylelint "**/*.css"`

新创建出来的项目里会有一个main.css 这个就会检验它，可以看到有爆红，这说明我们没有按照规定来书写，如下


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4071f4488464d83aca4fab77c24d7c2~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1184&h=650&s=88966&e=png&b=1f1f1f)

按下保存，就可以修复这样报错


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/110142f02a4344c0a33d36bc5f6fac9e~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1068&h=638&s=78359&e=png&b=1f1f1f)

但是我们的项目中肯定要用到`less` `scss`这种预编译器，那我们该怎么办呢？

6. 预编译的安装

- 对`scss`的支持
`pnpm install stylelint-config-recommended-vue stylelint-config-standard-scss postcss -D`

我们这里有`scss`为例

`Vite`也同时提供了对 `.scss` `.sass` `.less` `.style` `.stylus` 文件的内置支持，不需要再安装特定插件和预处理器

7. 修改`.stylelintrc.cjs`

需要将校验规则换成scss的
 ```js
 #scss
extends:[
	"stylelint-config-standard-scss",
	"stylelint-config-recommended-vue/scss"
]
```


 ```js
 #less
extends: [
	"stylelint-config-standard",
	"stylelint-config-recommended-less",
	"stylelint-config-recommended-scss",
	"stylelint-config-recommended-vue"
]
```

8. `package.json`增加脚本

`"lint:css": "stylelint **/*.{vue,css,sass,scss} --fix"`

执行脚本，可以看到我们样式的错误


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be5a32d4e77f44fb852eff14eeadd076~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1088&h=107&s=10404&e=png&b=181818)

9. 给`Vite`添加插件

`pnpm install vite-plugin-stylelint -D`


```js
# vite.config.js
import stylelitPlugin from 'vite-plugin-stylelint';

plugins: [... stylelitPlugin({fix：true})],
```

10. `stylelint` 与 `git` 结合

```js
#package.json

"lint-staged": {
    "*.{js,jsx,vue,ts,tsx}": [
      "npm run lint",
      "npm run prettier-format"
    ],
    # 新加入的
    "*.{vue,less,css,scss,sass}": [
      "pnpm run lint:css"
    ]
  }
```

11. 添加一个`.stylelintignore`文件

```js
/dist/*
/public/*
```
这个时候就可以验证自己的结果了，继续执行git的相关操作，如果有问题，你的终端就会报错，你将无法提交到仓库上去

## 团队配置文件的统一

前面一直在设置我们自己vscode 中的配置文件，小伙伴们肯定很疑惑，难道要手动去改每个人本地的配置文件吗？ 当然不需要，我么现在就来解决这个问题！！！

- 在`.vscode`文件中新建`setting.json`文件

```js
"editor .defaultFormatter": "esbenp.prettier-vscode"
"editor. formatOnsave": true,
"'editor .codeActionsOnSave": {
"source. fixAll. eslint": true
"[vue]": {
"'editor. defaultFormatter".
"esbenp.prettier-vscode"
"[ts]": {
"editor. defaultFormatter": "esbenp.prettier-vscode"
"(less]": {
// "editor .defaultFormatter": "stylelint.vscode-stylelint"
"'editor. defaultFormatter": "esbenp.prettier-vscode"
"[scss]": f
"editor. defaultFormatter": "esbenp.prettier-vscode"
}, 
"typescript.tsdk": "node_modules/typescript/lib"

```

## 断点调试


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea5b22a0459741a690cae1e54ce5581c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=389&h=395&s=17102&e=png&b=252526)


这个时候会在我们`.vscode`文件中增加一个`launch.json`文件，这个时候我只需要在vscode 中增加断点，点击运行就可以进行断点，这里就不赘述了


## 总结
这样我们基本就完成了前端代码规范的检验，这样可以保证团队代码的统一性，大家可以试试，我这里放上了我的代码仓库，需要的可以拉下来看看，放在了文章的开始

> 夫学须静也，才须学也，非学无以广才，非志无以成学。淫慢则不能励精，险躁则不能治性。年与时驰，意与日去，遂成枯落



