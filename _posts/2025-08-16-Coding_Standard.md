---
title: 【技术备忘录】 我的代码编写规范
date: 2025-08-16 00:00:00 +0800
description: 无论是编写测试代码，还是编写实际大型项目的代码，都需要有一个统一的规范，才能提高代码的可维护性。
categories: [Technical, Memorandum]
tags: [memorandum, conding]     # TAG names should always be lowercase
toc: true
# comments: true
pin: true
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false
image:
    path: assets/img/covers/barnetts_demesne.png
    alt: 雪原村落，宁静致远。
---


## Git 提交信息规范
```text
feat: 新功能的添加。
示例: feat: 添加用户注册功能
fix: 修复 bug。
示例: fix: 修复用户登录后无法跳转的问题
docs: 文档相关的更改，例如更新 README 文件或 API 文档。
示例: docs: 更新API文档
style: 代码格式的更改（空格、格式化、缺少分号等），不影响程序逻辑。
示例: style: 调整代码缩进
refactor: 代码重构，既不修复 bug 也不添加功能，改善代码结构。
示例: refactor: 优化数据处理逻辑
perf: 性能优化的更改。
示例: perf: 提升数据加载速度
test: 添加或修改测试用例，不涉及生产代码的改动。
示例: test: 添加用户注册测试
chore: 其他日常维护工作，比如构建流程或辅助工具的更改，不影响源代码。
示例: chore: 更新依赖库
build: 影响构建过程或外部依赖的更改（如 Maven、Gradle 等）。
示例: build: 更新构建脚本
ci: 持续集成相关的更改。
示例: ci: 更新CI配置
revert: 撤销某次提交。
示例: revert: 撤销添加用户注册功能
```

## 版本号命名规范
参考1：[Semantic Versioning 2.0.0](https://semver.org/)  
参考2：[软件项目、产品版本号的命名规则、定义、命名规范（git代码分支版本、项目产品文档版本）](https://blog.csdn.net/qq_29974981/article/details/121008029)
