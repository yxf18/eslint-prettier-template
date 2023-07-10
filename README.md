## eslint

代码检查工具

1. 安装
   `npm i eslint -D`
2. 初始化
   `npx eslint --init`

> init 命令会自动生成 .eslintrc.js

## prettier

代码风格工具(格式化代码)

1. 安装
   `npm i prettier eslint-config-prettier eslint-plugin-prettier -D`
2. 在.eslintrc 中,extend 中添加 "prettier" 解决 eslint 和 prettier 的冲突
   因为 eslint-config-prettier 新版本更新之后,只需要写一个 "prettierr" 即可,无需多言指令

3. 创建 .prettierrc (可选)

```json
{
  "semi": false,
  "tabWidth": 2,
  "trailingComma": "none",
  "singleQuote": true,
  "arrowParens": "avoid"
}
```

{
"semi": false,
"tabWidth": 2,
"trailingComma": "none",
"singleQuote": true,
"arrowParens": "avoid"
}

## git 规范

Git 有很多的 hooks, 让我们在不同的阶段,对代码进行不同的操作,控制提交到仓库的代码的规范性,和准确性, 以下只是几个常用的钩子

### 提交的代码规范

`pre-commit`: 通过钩子函数,判断提交的代码是否符合规范

### 提交的信息规范

`commit-msg`: 通过钩子函数,判断 commit 信息是否符合规范

### 提交的代码影响

`pre-push`: 描述: 通过钩子,执行测试,避免对以前的内容造成影响

## git 工具

1. husky: 操作 git 钩子的工具
2. lint-staged: 本地暂存代码检查工具
3. commitlint: commit 信息校验工具
4. commitizen: 辅助 commit 信息 ,就像这样,通过选择输入,规范提交信息

## lint-staged

lint-staged 是一个对`git暂存区`代码进行格式化的工具

1. 安装 lint-staged
   `npm install lint-staged -D`
2. package.json 配置

```json
"lint-staged": {
    "*.{js,ts,vue,jsx,tsx}": [
      "eslint --cache --fix"
    ],
    "*.md": ["prettier --write"]
},
```

## husky

husky 是一个 Git hooks 工具，能够在项目中配置 hooks 脚本；当我们执行 git 操作时，自动触发配置的脚本；常用的 hooks 有 pre-commit 和 commit-message。

1. 安装 husky
   `npm install husky --D`

2. 在 package.json 中 scripts 添加 prepare 命令,
   命令行: `npm set-script prepare "husky install" # 在package.json中添加脚本`
   手动加: `"prepare": "husky install"`

设置 prepare 命令的目的是：在其他用户执行 npm install 后会自动执行 husky install；
执行 husky install 时，会使用 git 命令配置 core.hooksPath，将其设置成.husky;
配置完成后，在项目中执行 npm prepare(也就是执行了 husky install)；
执行完成后，会在项目根目录生成一个.husky 文件夹，

3. 添加 pre-commit 钩子
   `npx husky add .husky/pre-commit "npm run lint-staged"`
   执行这一步后，在.husky 目录下会创建一个 pre-commit 文件，内容如下：

```
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint-staged

```

至此，配置完成；
我们从头理一下工具的工作模式：

1. 当我们进行一次 git 提交时
2. 触发 husky 配置的`pre-commit`钩子
3. 执行`npm run lint-staged`命令
4. 触发 lint-staged 对暂存区的文件进行格式化（使用 package.json 中配置的 lint-staged 任务）
5. 使用`eslint`进行格式化

## commitlint 安装提交信息校验工具

```shell
npm i commitlint @commitlint/config-conventional -D
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

> `@commitlint/config-conventional` 这是一个规范配置,标识采用什么规范来执行消息校验, 这个默认是 Angular 的提交规范

## commitizen 安装提交信息辅助工具

1. 安装指令和命令行的展示信息

```shell
npm i commitizen cz-conventional-changelog -D
```

2. 编写 commit 指令

```shell
npm set-script commit "git-cz" # package.json 中添加 commit 指令, 执行 `git-cz` 指令
```

3. 初始化命令行的选项信息,不然没有选项

```shell
npx commitizen init cz-conventional-changelog --save-dev --save-exact
```

4. 自定义提交规范**cz-customizable**(可选)

- 变更 commitlint.config.js 这里采用自己定义的规范,将会覆盖上面那个,所以上面那个可以不用安装

```shell
npm i -D commitlint-config-cz  cz-customizable
```
5. 创建 commitlint.config.js 配置文件
```shell
// extends: ['config-conventional']
echo "module.exports = {extends: ['cz']}" > commitlint.config.js
```

- 增加 .cz-config.js

```js
module.exports = {
  types: [
    {
      value: ":sparkles: feat",
      name: "✨ feat:     新功能",
    },
    {
      value: ":bug: fix",
      name: "🐛 fix:      修复bug",
    },
    {
      value: ":package: build",
      name: "📦️ build:    打包",
    },
    {
      value: ":zap: perf",
      name: "⚡️ perf:     性能优化",
    },
    {
      value: ":tada: release",
      name: "🎉 release:  发布正式版",
    },
    {
      value: ":lipstick: style",
      name: "💄 style:    代码的样式美化",
    },
    {
      value: ":recycle: refactor",
      name: "♻️  refactor: 重构",
    },
    {
      value: ":pencil2: docs",
      name: "✏️  docs:     文档变更",
    },
    {
      value: ":white_check_mark: test",
      name: "✅ test:     测试",
    },
    {
      value: ":rewind: revert",
      name: "⏪️ revert:   回退",
    },
    {
      value: ":rocket: chore",
      name: "🚀 chore:    构建/工程依赖/工具",
    },
    {
      value: ":construction_worker: ci",
      name: "👷 ci:       CI related changes",
    },
  ],
  // scopes: ["empty"],
  messages: {
    type: "请选择提交类型(必填)",
    // scope: "更改的范围scope (可选)",
    emptyScope: "请输入文件修改范围(可选)",
    subject: "请简要描述提交(必填)",
    body: "请输入详细描述(可选)",
    breaking: "列出任何BREAKING CHANGES(可选)",
    footer: "请输入要关闭的issue(可选)",
    confirmCommit: "确定提交此说明吗？",
  },
  allowCustomScopes: true,
  // 跳过问题
  skipQuestions: ["body", "footer"],
  subjectLimit: 72,
};
```

- package.json 中,将原来 commit 配置,变更为自定义配置

```json
  "config": {
    "commitizen": {
      //"path": "node_modules/cz-conventional-changelog" // 这里是原来的配置,变更为自定义配置
      "path": "node_modules/cz-customizable"
    }
  }
```

## commitizen、cz-customizable、commitlint 三者之间的关系

- commitizen 就像是生产线上的模板，它定义了产品的外观和结构，提供了一种易于理解和使用的模板来生成规范化的提交信息。
- cz-customizable 就像是生产线上的调整机器，你可以给产品换个颜色，换个包装等等。它可以根据不同的需求对模板进行定制，适应不同的项目需求。
- commitlint 就像是生产线上的检测设备，这意味着不管你如何去 DIY 这个产品，他总要有一个审核标准来说明他是一个合格产品。而 commitlint 支持多种规范配置文件，其中就包括 commitlint-config-cz，它继承了 commitlint-config-conventional 的基础规范，并增加了对 commitizen 规范的支持。
