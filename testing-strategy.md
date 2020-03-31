---
marp: true
theme: default
class: lead
paginate: true
---
<!-- _class: invert -->

# <!--fit--> 遗留代码重构&测试

# 进阶篇：测试策略

![bg brightness:0.3](http://5b0988e595225.cdn.sohucs.com/images/20190911/718243ebcaca488eaa50fc2637e3e477.jpeg)

CAC@OPPO by 黄俊彬 & 覃宇

---

# 讨论：你所了解的自动化测试有哪些:question:

---
<!-- 对测试活动/实践/方法进行分类 -->

![bg containt 80%](/assets/test-type.png)

---

# 讨论：如何选择对应的测试:question:

---

# 测试金字塔-Android

## 合理设计策略提高自动化测试的ROI

![](assets/test-pyramid.png)

---

# Android自动化测试类型

| 分类   | 框架  | 范围                                                                                           | 设备 | 速度                                                                                   |
|--------|--------------------------------|------------------------------------------------------------------------------------------------|-----|----------------------------------------------------------------------------------------|
| 大型测试 | Appium 、UIAutomator            | 用户通过界面操作的流                                                                              | 是  | 慢，分钟级(一般在1~5分钟，依测试场景复杂度而定)                                                |
| 中型测试 | JUnit、Instrumentation、Espresso | 功能测试：用户通过界面操作的流程(只能在单个应用内操作)集成测试：Java方法(依赖Android框架、数据库、第三方能力实现) | 是  | 较慢，涉及界面操作的在分钟级，不涉及界面操作的Java方法的测试在秒级(一般在1秒内，依被测方法的执行时间而定) |
| 小型测试 | JUnit、Mockito、Robolectric      | “纯”Java方法(不依赖Android框架、数据库、第三方能力，或这些依赖可以很方便的Mock，对代码可测试性要求较高)        | 否  | 快，毫秒级                                                                               |

---

# 总结 

## 原则

不同的自动化测试(工具)有不同的适用场景，单靠一种测试不能完成自动化。要结合实际综合运用多种工具。

## 经验 

Google 根据自己 APK 开发团队的实践经验，采用测试驱动开发实践，推荐的小、中、大型测试的比例为：**7:2:1** :+1:

---

# 讨论：对应到项目中，我们如何进行分层测试设计:question:

---

# MVP

![bg containt 60%](/assets/mvp.png)

---

# 分层测试策略

## 单元测试

* Repository
* Presenter

## 集成测试

* Local data source

## 功能测试

* Activity

---

# MVVM

![bg containt 60%](/assets/mvvm.png)

---

# 分层测试策略

## 单元测试

* Repository
* ViewModel

## 集成测试

* Local data source

## 功能测试

* Activity

---

# 讨论：遗留系统中，并没有使用整洁架构，怎么办:question:

---
 
# 遗留系统测试策略

1. 补充核心功能的功能和集成测试，以保护核心功能不被破坏
2. 新功能需求，使用新的整洁架构进行设计及测试覆盖
3. 建立技术债偿还计划，逐优先级重构并补充核心功能的各级别自动化测试

---
<!-- _class: invert -->

# <!--fit--> Demo

X项目组，开发新用户故事，使用MVP架构，并为Presenter覆盖单元测试

 