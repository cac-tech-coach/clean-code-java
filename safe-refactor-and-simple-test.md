# 遗留代码重构&测试（入门篇）


1. 用 Code Inspection 寻找坏味道

    * Sonar Lint
    * Alibaba Guideline

2. “先重构再测试”还是“先测试再重构”

    * 先**安全**重构再**单元**测试（随手就做）
    * *先**集成**测试再**手动**重构*（慎重计划）(进阶篇)

    > **什么是安全重构？** 可以用工具自动完成。让工具帮我们写“八股文”代码，让工具帮我们检查。
    > * Context Actions(Alt+Enter)
    > * Refactor This(Ctrl+T)
    > * Generate(Cmd+N)
    > * Live Template
    > * 使用 IDE 支持的 Marker Annotation(androidx.annotations.*)

3. 最大化 ROI

    * 围绕最有价值的代码
      * 理解起来难（长方法、复杂条件）
      * 重复代码（避免散弹式修改，极有可能被重用）
    * 投入相对低
      * 安全重构就可以完成
      * 尝试失败立即回滚
      * ~~单元测试基本不用 PowerMock~~

    > **如果特别有价值，但成本高怎么办？** 记录下来，作为**技术债务**按优先级排进迭代计划，先**集成**测试再**手动**重构

4. 小步前进

    * 随时 Undo
    * 尝试多种自动重构
    * 可以添加单元测试就立即添加
    * 修改代码后立即执行测试


5. 单元测试回顾

    * F.I.R.S.T原则
    * 测试类、测试方法命名规范
    * 测试方法模板（Given\When\Then）


# DEMO

## 准备工作

1. 单元测试的 gradle dependencies 和 config

## CreateIssuePresenter


## FeedsPresenter

1. 代码上下文讲解
2. 第一步：提取方法，@NonNull
3. 第二步：简化 return，if/eles，Alt+Enter
4. 第三步：提取条件方法
5. 第四步：使用@VisibleForTesting，提高方法 access level
6. 第五步：编写单元测试，命名回顾，Live template
7. 第六步：用 AssertJ 提高断言可读性
   


