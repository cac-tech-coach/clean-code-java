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

目前的自动化测试有那些类型？
用什么样的工具？
有什么特点？

---

# 回顾：测试金字塔，是如何进行分层，有什么特点:question:

---

# 测试金字塔-Android

## 合理设计策略提高自动化测试的ROI

![](assets/test-pyramid.png)

---

# Android自动化测试类型 ？？？

![](assets/android-test-type.png)

---

# 总结 

## 原则

不同的自动化测试(工具)有不同的适用场景，单靠一种测试不能完成自动化。要结合实际综合运用多种工具。

## 经验 

Google 根据自己 APK 开发团队的实践经验，采用测试驱动开发实践，推荐的小、中、大型测试的比例为：**7:2:1** :+1:

---

# 挑战

* 人员能力不足
* 难以覆盖全面
* 难以单元测试
* “影响开发进度”:question:

---

# 讨论：典型Android应用的分层测试，如何进行分层测试设计:question:

---

# MVP

![bg containt 60%](/assets/mvp.png)

---

# 什么样的情形适合用？有什么样的要求？

---

# 分层测试策略

### 小型测试

* Repository
* Presenter :heart:
* Model

### 中型测试

* Local data source

* Activity

### 大型测试

* ?

---

# 怎么和现在具体的实现做讨论？

---

# MVVM

![bg containt 60%](/assets/mvvm.png)

---

# 什么样的情形适合用？有什么样的要求？

---

# 分层测试策略

### 小型测试

* Repository
* ViewModel :heart: (双向绑定，Databinding xml 不好测)，不用验证回调，验证数据)
* Model

### 中型测试

* Local data source
* service
* Activity

### 大型测试

?

---

<!-- _class: invert -->

# <!--fit--> Demo

X项目组，开发新用户故事，使用MVP架构重构，并为Presenter新业务逻辑覆盖单元测试

 ---

 # 用户故事

 ## 动态服务更新后进入应用及时展示出来

 As a : 服务订阅用户
 I want: 有新的服务更新
 So that: 可以第一时间看到动态服务

 **AC1**: 在应用底部
 Given：用户已将列表收起或不存在服务信息
 When：有新的服务
 Then：将列表展示出来（堆叠状态）

// Other AC ...... 

 ---

 # 测试策略分析 (KickOff、DeskCheck)

 表格示例？ 小型 中型 大型    AC质量高，按AC来。

 1. 服务更新逻辑判断-小型测试
 2. 列表展示-中型测试
 3. 

 ---

# 原实现逻辑及问题

``` 
class XView extend View{
    //找一个典型的方法实现伪代码例子
    void show();
    void queryData();
}

```

* 在自定义View中实现了大部分的业务逻辑，编写新功能时对原有的代码改动大
* 新业务代码和View层耦合，测试编写难度大

 
---

# 重构实现

//原来View的变化，与Presenter的关系

``` 
class XPresenter{
    //View 没有按标准的接口定义
    XView xView;
    void queryData();
    //关键检查是否有新的服务更新
    public boolean checkIfHasNewServiceCards(){
        boolen hasNewCard=false;
        if(currentCount>lastCount){
            hasNewCard=true;
        }else if(!mLastSceneSet.containtsAll(mCurrentSceneSet)){
            hasNewServiceCards=true;
        }
        //do something ... ...
        return hasNewServiceCards;
    }
}
```

* 使用MVP模式定义对应的Presenter将业务逻辑与View层剥离（方法抽取、移动）
* 对新增业务方法checkIfHasNewServiceCards编写单元测试 

---

# 单元测试

``` 
class XViewPresenterTest{
    var xPresenter:XPresenter
    var xView=mock(XView::class.java)
    var callBack=mock(CallBack::class.java)

    @Before
    fun setUp{
        xPresenter=XPresenter(xView,callBack)
    }

    @Test
    fun `should return true when checkIfHasNewServiceCards called if have new service cards with last set is empty` (){
        //Given
        val currentList=getElementsList()
        //when
        val result=presenter.checkIfHasNewServiceCards()
        //Then
        Assert.assertTrue(result)
    }

    // other test case ... ...

    //补充其他的方法名称

}
```

* 覆盖方法的条件分支
* mock隔离依赖view

---

# 讨论：还有哪些地方可以优化:question:

---

# 注意

1. Presenter 直接依赖了XView，应该定义接口进行隔离
2. 为新的业务方法补充单元测试后，原的关键方法也当及时补充测试

---

# 回顾总结

1、整体流程思路
2、过程注意事项

---

# 讨论：遗留系统中，并没有使用整洁架构，怎么办:question:

---

# 遗留系统测试策略

1. 补充核心功能的功能和集成测试，以保护核心功能不被破坏
2. 新功能需求，使用新的整洁架构进行设计及测试覆盖
3. 建立技术债偿还计划，逐优先级重构并补充核心功能的各级别自动化测试

---
 
 # 流程图

 ？

 # 时机

 1、加新功能需要改动原因的功能

 # 步骤

 1、新功能需要加上小型测试保障，覆盖核心业务逻辑
 2、如果调用到原有代码，使用安全重构，或者先加上中型测试

 # 原则

 提炼？
 

 

