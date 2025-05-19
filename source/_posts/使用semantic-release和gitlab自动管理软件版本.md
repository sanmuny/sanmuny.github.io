---
title: 使用semantic-release和gitlab CI自动管理软件版本
date: 2025-05-19 21:39:59
tags: [devops, "semantic release", commitlint, gitlab]
published: true
hideInList: false
feature: 
isTop: false
categories: 开发工具
---

在敏捷开发和DevOps实践中，持续集成与自动化发布已成为提升团队效率的关键。然而，传统手动管理软件版本的方式常面临诸多挑战：分支繁多导致版本追溯困难，人工更新版本号易出错，且变更日志的编写耗时耗力。尤其在多成员协作的项目中，版本兼容性管理不当可能引发“依赖地狱”，影响交付效率。

为解决这些问题，semantic-release应运而生。该工具通过分析符合规范的Git提交信息（如Angular Commit规范），自动遵循语义化版本（SemVer）规则升级版本号，并生成变更日志与Git标签，实现从代码提交到版本发布的端到端自动化。结合GitLab CI/CD，这一流程进一步无缝集成至持续交付管道：开发者只需推送代码至特定分支（如main），即可触发自动化版本发布、生成文档并推送至仓库，甚至通过插件扩展至npm包发布等场景。这种组合不仅减少了人为操作风险，还确保版本迭代透明可追溯，尤其适用于追求高效、规范化的敏捷团队。

# 语义化版本(Semantic Versioning)

语义化版本控制规范（SemVer）是为软件版本号赋予明确含义的标准格式。其版本号采用X.Y.Z（主版本号.次版本号.修订号）结构，通过数字变化反映代码变更性质：

- 主版本号（X）：不兼容的API重大变更时递增，次版本和修订号归零（如2.1.3→3.0.0）
- 次版本号（Y）：向下兼容的功能新增时递增，修订号归零（如1.2.3→1.3.0）
- 修订号（Z）：向下兼容的问题修复时递增（如1.2.3→1.2.4）

先行版本可通过附加连字符和标识符表示（如1.0.0-alpha.1）。版本比较按数值逐级对比，1.9.0 < 1.10.0 < 2.0.0。该规范强调公共API的明确定义，要求维护版本更新日志，旨在通过标准化的版本号传递兼容性信息，帮助开发者管理依赖关系，避免"依赖地狱"。遵循SemVer可提升软件生态系统的可预测性和协作效率，尤其适用于依赖包管理场景。

# Angular Git提交信息规范

Angular Git提交信息规范要求开发者遵循结构化格式以确保代码变更的可追踪性和自动化处理。核心规则包括：

- 格式模板：提交信息须包含`<type>(<scope>): <subject>`标题行（必填），空行后接正文（选填）及页脚（如BREAKING CHANGE或Issue引用）。

- 类型限定：type需从预设列表选择（如feat（功能）、fix（修复）、docs（文档）、style（样式）等），重大变更需在页脚标注`BREAKING CHANGE:`。

- 语法规范：标题行不超过100字符，正文每行72字符内，使用祈使语气（如“fix”而非“fixed”）。scope可选，用于标记模块（如core）。

该规范通过标准化提交信息，便于生成自动化变更日志（如CHANGELOG.md）和语义化版本升级（结合工具如semantic-release），提升协作效率与版本管理的可靠性。CI工具中可以集成`commitlint`来检查PR(pull request)中的commit message.

# semantic-release

`semantic-release` 是基于提交信息实现全自动化版本管理和软件发布的工具，其核心逻辑与语义化版本（SemVer）及Angular Commit规范深度绑定。主要特性如下：

- 自动化版本控制：通过解析提交信息中的`feat、fix、BREAKING CHANGE`等类型，自动确定版本号升级级别（主版本/次版本/修订号），无需手动维护`package.json`版本字段。

- 发布流程集成：

  - 变更日志生成：自动从提交信息提取内容，生成标准化的CHANGELOG.md。
  - 多平台发布：支持通过插件发布到npm、GitHub/GitLab Releases、Slack通知等场景。
  - Git标签管理：自动创建版本标签（如v1.0.0）并推送至仓库。

- 安全与协作保障：

  - 依赖CI环境：仅在持续集成（如GitLab CI、GitHub Actions）中运行，避免本地误操作。
  - 分支策略：默认仅对`main`分支触发发布，支持预发布通道（如beta分支）。

- 版本更新规则说明：
  - PR(Pull Request)中的Git commit，消息体中包含`BREAKING CHANGE:`，则主版本号+1，次版本号和修订号归零
  - 包含类型为`feat`的消息则主版本号不变，次版本号+1，修订号清零
  - 包含类型为`fix`的消息则修订号+1，主次版本号保持不变

该工具通过标准化提交、版本与发布的联动，显著降低人为错误风险，适用于追求高效、可追溯的敏捷团队，尤其与semantic-release生态插件（如`@semantic-release/gitlab`）结合时，可无缝融入DevOps流程。

# Gitlab CI配置举例

*.gitlab-ci.yaml*

``` yaml
stages:
  - pr-check # Triggered after PR is created
  - release # Triggered after PR is merged
  - build # Triggered after release is succeeded

variables:
  REGISTRY: "http://npm.registry.com"

pr-check-job:
  stage: pr-check
  script:
    - |
      yarn config set registry ${REGISTRY}
      yarn install
      echo "Running commitlint..."
      git fetch origin main $CI_MERGE_REQUEST_TARGET_BRANCH_NAME
      BASE_SHA=$(git merge-base origin/$CI_MERGE_REQUEST_TARGET_BRANCH_NAME $CI_COMMIT_SHA)
      for COMMIT_SHA in $(git log --pretty=format:%H $BASE_SHA..$CI_COMMIT_SHA); do
        COMMIT_MSG=$(git log --format=%B -n 1 $COMMIT_SHA)
        echo "$COMMIT_MSG" | npx commitlint
      done
      echo "Commitlint completed."
  rules:
    - if: $CI_PIPELINE_SOURCE == 'merge_request_event'

release:
  stage: release
  tags:
    - docker
  image: node:lts
  variables:
    NODE_TLS_REJECT_UNAUTHORIZED: 0
  script:
    - yarn config set registry ${REGISTRY}
    - yarn install
    - git config http.sslVerify false
    - npx semantic-release
  rules:
    - if: $CI_COMMIT_BRANCH == "main" && $CI_PIPELINE_SOURCE == "push"

build-image-job:
  stage: build
  script:
    - |
      echo "Build docker image..."
      echo "Image push completed."
  rules:
    - if: $CI_COMMIT_BRANCH == "main" && $CI_PIPELINE_SOURCE == "push"
```

*.releaserc.json*

``` json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "./ci-plugin.cjs",
    "@semantic-release/gitlab",
    "@semantic-release/git"
  ]
}
```

*./ci-plugin.cjs*

``` js
const https = require("https");

module.exports = {
  // Called during verifyConditions step
  verifyConditions: async (_, context) => {
    context.logger.log("Ensure env variable is respected");
    https.globalAgent.options.rejectUnauthorized =
      process.env.NODE_TLS_REJECT_UNAUTHORIZED != "0";
  },
};
```

注意：ci-plugin.js用于绕过semantic-release@gitlab插件不能设置跳过证书检查的问题，详细信息参考：https://github.com/semantic-release/gitlab/issues/725

# 小结

本文通过整合`semantic-release`与`GitLab CI/CD`，团队可将版本管理与发布流程彻底自动化，形成从代码提交到生产部署的闭环。这一方案以语义化版本为基准，依赖规范化提交信息（如Angular Commit）触发版本号升级逻辑，并通过CI流水线实现零人工干预的版本发布、变更日志生成及多平台分发。

其核心价值在于将开发规范转化为自动化约束，既规避人为错误，又通过SemVer机制明确传递版本兼容性，有效缓解依赖管理风险。这种实践不仅加速迭代周期，更使版本历史清晰可溯，为DevOps“持续交付”理念提供了标准化落地方案，助力团队在复杂协作中维持高效与可靠性。