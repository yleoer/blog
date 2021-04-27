---
title: Git 提交规范
tags: Git
categories: 工具
abbrlink: 44821d70
date: 2021-02-02 10:39:02
---

规范的代码提交有利于快速定位改动，有助于生成清晰的 ChangeLog 。

<!-- MORE -->

### 格式规范

参考 [Angular](https://github.com/angular/angular.js) 的提交准侧，一种基于提交信息的轻量级约定。

```
<type>[(scope)]: <short summary>

[body]

[footer]
```

例如：

```
refactor(theme): 修改版权信息

- 版权的日期由2020年变更为2021年
- 将新的开源共享规范纳入版权协议

BREAKING CHANGE: 旧的授权协议将在更新后被新的协议替代

Closes #F12YC
```

### type

`type` 为必填项，用于指定本次提交的类型。

- `feat`: 新功能，对新需求的实现
- `fix`: 修复，对 bug 的修复
- `docs`: 文档，只改动了文档相关的内容
- `perf`: 性能，改进性能的代码更改
- `refactor`: 重构，既不是修复 bug 也不是添加功能的代码更改
- `test`: 测试，添加或修改测试用例

### scope

`scope` 用于描述改动的范围或本次提交印象的范围，方便快速定位。

### subject

`subject` 为必填项，是对本次提交的简短描述概括。

### body

`body` 填写详细描述，主要描述改动前的情况及修改动机，罗列代码功能。

### breaking change

`BREAKING CHANGE` 指明是否产生了破坏性修改，有利于出问题时快速定位，回滚和复盘。

[^1]:  [Angular 提交规范](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format)
[^2]: [开发中的你的Git提交规范吗？](https://segmentfault.com/a/1190000039056198)