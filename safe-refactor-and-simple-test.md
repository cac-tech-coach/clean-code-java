---
marp: true
theme: default
class: lead
paginate: true
---
<!-- _class: invert -->

# <!--fit--> 遗留代码重构&测试
# 入门篇

CAC@OPPO by 黄俊彬 & 覃宇

---

## 用 Code Inspection 寻找坏味道

* Sonar Lint
* Alibaba Guideline

---

##  “先重构再测试”还是“先测试再重构”

* 先**安全**重构再**单元**测试（随手就做）
* 先**集成**测试再**手动**重构（慎重计划）(进阶篇)

---

### 什么是安全重构？

可以用工具自动完成，小步前进。让工具帮我们写“八股文”代码，让工具帮我们检查。

> Android Studio 最让人惊艳的功能？你最常用的快捷键是？

---

### 我最常用的快捷键

* Context Actions(Alt+Enter)
* Refactor This(Ctrl+T)
* Generate(Cmd+N)
* Live Template
* 使用 IDE 支持的 Marker Annotation(androidx.annotations.*)

---

## 最大化 ROI

* 围绕最有价值的代码
      * 理解起来难（长方法、复杂条件）
      * 重复代码（避免散弹式修改，极有可能被重用）
* 投入相对低
      * 安全重构就可以完成
      * 尝试失败立即回滚
      * 不用 PowerMock

---

### 如果特别有价值，但成本高怎么办？

记录下来，作为**技术债务**按优先级排进迭代计划，先**集成**测试再**手动**重构，最后结对完成。

---

## 小步前进

* 随时 Undo
* 多尝试几种自动重构
* 可以添加单元测试就立即添加
* 修改代码后立即执行测试

---


## 单元测试回顾

* F.I.R.S.T原则？
* 测试类、测试方法命名规范？
* “三段式”测试方法模板？

---

<!-- _class: invert -->

# <!--fit-->  DEMO

---

# FastHub示例介绍

[https://github.com/k0shk0sh/FastHub]()

开源 GitHub 客户端

- 采用 MVP 模式
- 代码结构相对清晰
- 几乎没有注释
- :thumbUp:完全没有自动化测试

---

## 准备活动

- Code Inspection 介绍
- Android Studio 快捷键介绍
- 增加单元测试 dependencies 和 config

---

## 轻松热身：FeedsPresenter

- 几乎不用自己写代码
- 单元测试不用 mock

<!-- 1. 代码上下文讲解
1. 第一步：提取方法，@NonNull
2. 第二步：简化 return，if/eles，Alt+Enter
3. 第三步：提取条件方法
4. 第四步：使用@VisibleForTesting，提高方法 access level
5. 第五步：编写单元测试，命名回顾，Live template
6. 第六步：用 AssertJ 提高断言可读性 -->
   

---

## 强度升级：CreateIssuePresenter

- 用到了 Robolectric 和 Mockito


