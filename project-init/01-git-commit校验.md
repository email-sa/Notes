# vue3-info-project

基于 vue3 + webpack + antv + ts + lodash 从 0-1 实现一个信息管理系统

# 第一阶段

## 添加 commit 强校验之 husky

项目的提交备注五花八门,以至于后期不好追踪排查问题,强烈推荐大家在项目中加上 commit 规范约束,方便你我他 nice! 实现步骤如下:

1.  首先,安装 `husky`
    `husky`它可以方便我们在项目中添加 git hooks,能够防止不规范代码被 commit、push、merge 等等,ps: 以下都是以 husky@6+版本为例

```
npm i -D husky
```

2. 执行 `npx husky install` 命令,该命令会创建 `.husky/` 目录,并指定该目录为 git hooks 所在目录,即修改 `git hooks` 默认目录

3. 添加 `git hooks`,语法: `husky add \<file\> \[cmd\]` ，运行以下命令创建 git hooks `pre-commit`,git commit 之前会执行的 hook

```
npx husky add .husky/pre-commit "npm run test"
```

执行成功之后 `.husky` 目录下面会有一个名为 `pre-commit` 的 shell 脚本,在执行 commit 之前会执行该脚本.
脚本内容为

```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run test
```

可根据具体问题修改脚本内容,这里我将脚本内容更改为:

```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

echo "========== 执行pre-commit 操作 =========="

git add .
```

> `#!` 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。
> `echo` 命令用于向窗口输出文本。
> 引用自 [这里](https://www.runoob.com/linux/linux-shell.html)

以上脚本表示,commit 之前执行 `git add .` 操作(具体操作可根据自己的项目调整)

4. 添加 commit-msg 校验
   安装 commit 规范校验 commitlint

```
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

在项目根目录下,创建文件 `commitlint.config.js`,配置相关的 commit 规范定义
,文件内容可参考以下

> rules 的配置规则:
> rules 由 name 和配置数组组成 例如: `"name": [ level, 'always', value]` > **level 表示** 错误的状态 ,取值 0,1,2; 0 -disable,1-warning, 2- error
> **数组第二个元素**标识此规则应用与否,取值为 "always| never"
> **value 表示**该 rule 的值,具体配置可以参考一下配置,具体配置可在[官网](https://commitlint.js.org/)查看

```javascript
// commitlint.config.js

module.exports = {
    extends: ["@commitlint/config-conventional"],
    rules: {
        "type-enum": [
            2,
            "always",
            [
                "feat", // 增加新功能
                "fix", // 修复bug
                "docs", // 只改动了文档相关的内容
                "style", // 不影响代码含义的改动，例如去掉空格、改变缩进、增删分号
                "refactor", // 代码重构时使用
                "test", // 添加测试或者修改现有测试
                "build", // 构造工具的或者外部依赖的改动，例如webpack，npm
                "chore", // 不修改src或者test的其他修改，例如构建过程或辅助工具的变更
                "revert", // 执行git revert打印的message
                "pref", // 提升性能的改动
                "merge" // 代码合并
            ]
        ],
        "type-case": [0],
        "type-empty": [0],
        "scope-empty": [0],
        "scope-case": [0],
        "subject-full-stop": [0, "never"],
        "subject-case": [0, "never"],
        "header-max-length": [0, "always", 72]
    }
};
```

5.  绑定检验,执行命令
    `npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'`
    来校验我们 commit 时添加的备注信息是否符合规范,执行成功之后 .husky 目录下面会有一个名为 `commit-msg`的 shell 脚本文件

将 `commit-msg`文件修改内容为:

```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

echo "========= 执行commit-msg校验 ======="
npx --no-install commitlint --edit $1

```

**如果以上命令执行不成功的话,可以去掉 cmd 命令,先创建文件再,修改文件内容即可**

最后 .husk 目录的结构为

```
-- _
     .gitignore
     husky.sh
-- commit-msg
-- pre-commit
```

在代码提交时候,大致会有如下效果:![在这里插入图片描述](https://img-blog.csdnimg.cn/37f3be0d678c4eabb861cd61f50b8151.png#pic_center)

在之后的 git 代码提交中会对每次的 git commit 内容进行校验,纠正大家的 commit 规范,又可以放心的撸代码了 \-.\-

关于 commit 规范,推荐参考 https://zhuanlan.zhihu.com/p/182553920

> 内容参考:
>
> 1. https://zhuanlan.zhihu.com/p/366786798
> 2. https://www.runoob.com/linux/linux-shell.html
