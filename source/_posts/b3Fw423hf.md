---
title: 'Feature flags 概要'
date: 2021-11-10 16:44:57
tags: [devops]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
cover: https://sanmuny.github.io/post-images/1636536399408.png
---
Feature flags可以让软件开发更加的安全、迭代更加迅速，而且可以更加灵活的控制软件产品。Feature flags将功能迭代从代码部署中分离出来，从而可以使产品团队（开发团队、管理团队、SRE等）在版本控制之外更加精细地控制谁、什么时候可以使用什么功能。

<!-- more -->

### 什么是Feature flags?

Feature flags是一种软件开发方法，开发者将新的功能封装在带有标志位的`if/else`语句中，从而使得产品团队可以在版本之外通过设置标志位来控制哪些功能可以被用户使用。标志位的开关独立于软件部署，所以即使产品环境部署同样一份代码，也可以通过设置标志位来打开/关闭某种功能。因此，支持Feature  flags也是持续交付(Continous delivery)的重要一环，它可以让版本发布更加迅速、更加可靠。

```
if (flags.kms) {
    /* with KMS feature */
} else {
    /* without KMS feature */
}
```

![](https://sanmuny.github.io/post-images/1636536399408.png)


### Feature flags最佳实践

1. 选择正确的级别。根据自身的需求来选择feature flags的级别。如果级别选的过低，可能会有成百上千的flags，而且还需要来维护一些用不上的flags。但如果级别定的过高，feature flags控制的粒度又不够，精细程度满足不了需求。
2. Flag的命名一定要准确的描述是什么功能。否则的话，系统可能因为错误的flag被关闭或者打开而被破坏。另外，如果一个flag总是处于打开或者关闭的状态，那就应该被删掉。
3. 设置日志来记录是谁打开或者关闭了flag。
4. 需要给开发者之外的人操作flag的权限。
5. 控制flag的访问权限。
6. 通过flag的状态来管理feature flags的生命周期。

### 蓝绿部署集成feature flags

蓝绿部署是一种比较成熟的用于降低风险的升级系统的方法。升级时维护两套系统，蓝系统和绿系统，分别为运行中的版本和新的版本。可以在系统升级的时候，通过负载均衡器将用户流量从旧版本系统导入到新版本的系统。蓝绿部署一方面可以缩短甚至消除升级的宕机时间，新版本部署的时候旧的系统仍在运行。另一方面可以降低风险，一旦新版本有问题，可以很容易地将用户流量切回到旧的版本。

![](https://sanmuny.github.io/post-images/1636948353910.png)

在蓝绿部署中可以引入feature flags，新的版本可以将新的功能通过feature flags关闭，然后再逐一打开。蓝绿升级要么使用新的版本的所有功能，要么使用旧的版本。feature flags 则弥补了这个不足。

### nodejs 中的feature flags资源

1. [LaunchDarkly](https://launchdarkly.com/feature-management/) 是一个完整的feature flags解决方案，它提供了服务器端和前端的SDK。
2. [Flipit](https://github.com/FenrirUnbound/flipit)是一个nodejs中使用feature flags的模块。它通过配置文件存取feature flags并提供了查询/更新feature flags的接口，可以让程序不需要重启便能读取到最新的配置文件更改。
3. [fflip](https://github.com/FredKSchott/fflip)除了查询/更新feature flags之外，增加了对feature flags访问权限的控制。
4. [Unleash](https://github.com/Unleash/unleash)和LaunchDarkly类似，是一个完整的feature flags解决方案，同样提供了nodejs和前端的SDK。





