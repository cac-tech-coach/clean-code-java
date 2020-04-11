---
marp: true
theme: default
class: lead
paginate: true
---
<style>
.container{

    display: flex;

}
.col{

    flex: 1;

}
</style>

<!-- _class: invert -->

![bg brightness:0.2](https://insights.thoughtworks.cn/wp-content/uploads/2018/09/3.jpg)

# <!-- fit -->**正交设计**&emsp; &emsp; 

CAC@OPPO by 黄俊彬 & 覃宇

---
<!-- _class: invert -->

# 软件设计是为了什么:question:

---

软件设计最重要的目的是**实现功能**。随着时间推移，不可提前预知的**不确定性**导致软件的复杂度不断增加，超出了开发者的**认知极限**，功能实现越来越难。软件设计是解决这个问题的重要手段。

> 软件设计是为了让软件在**长期范围**内**容易应对变化**。——*Kent Beck*

---
<!-- _class: invert -->

# 软件设计如何解决复杂性？如何长期应对变化:question:

---

![bg right:55% contain 90%](https://godsme.github.io/img/module.png)

# 模块化，分而治之 

- 降低认知难度
- 将影响局部化
- 带来模块复用

---
<!-- _class: invert -->

# 软件模块怎么分，又怎么合:question:

---

![bg right:55% contain 90%](https://godsme.github.io/img/orth1.png)

# 高内聚，低耦合

软件设计的**最基本原则**

* 只有**关联(?)**紧密的事物才能并应该被放在一起
* 软件模块之间尽可能不要相互**影响(?)**
* 一方的**变化**不会影响另外一方的**变化**（**正交**）

---
<!-- _class: invert -->

# 你还能想到哪些软件设计原则:question:

---

# S. O. L. I. D 原则

* S 单一职责原则
* O 开闭原则
* L 里氏替换原则
* I 接口隔离原则
* D 依赖倒置原则

* > 一个类只应该有一个**变化原因**（**职责**）。——Robert. C. Martin

<!-- 单一职责是所有设计原则的基础，开闭原则是设计的终极目标。里氏替换原则强调的是子类替换父类后程序运行时的正确性，它用来帮助实现开闭原则。而接口隔离原则用来帮助实现里氏替换原则，同时它也体现了单一职责。依赖倒置原则是过程式编程与 OO 编程的分水岭，同时它也被用来指导接口隔离原则。 -->

---
<!-- _class: invert -->

# 如何在编码开发时贯彻这些原则:question:

---

# 避免一个变化导致多处修改

遵循**单一职责原则**，**消除重复**

![bg right:65% contain 90%](https://godsme.github.io/img/srp1.png)

---

# 避免多个变化导致一处修改

遵循**单一职责原则**，拆分模块，**分离不同变化方向**

![bg right:65% contain 90%](https://godsme.github.io/img/srp2.png)

---

# 避免一个变化导致多处修改

遵循**开闭原则**，提取可以扩展的接口，**分离不同变化方向**

![bg right:65% contain 90%](https://godsme.github.io/img/ocp.png)

---

# 不依赖不稳定的依赖

遵循**里氏替换原则**，子类实现不能破坏父类被依赖的稳定接口，**向稳定的方向依赖**

![bg right:65% contain 90%](https://godsme.github.io/img/lsp.png)

---

# 不依赖不必要的依赖

遵循**接口隔离原则**，拆分接口，**缩小依赖的范围**

![bg right:65% contain 80%](https://godsme.github.io/img/isp.png)

---

# 不依赖不稳定的依赖

循**依赖倒置原则**，从“上层”定义稳定接口，“下层”实现向**稳定的方向依赖**

![bg right:65% contain 80%](https://godsme.github.io/img/dip.png)

---

![bg left:34% fit 70%](https://godsme.github.io/img/relation.png)

# 正交设计四原则

1️⃣消除重复（避免一个变化导致多处修改）
2️⃣分离不同变化方向（避免多个变化导致一处修改）
3️⃣缩小依赖范围（不依赖不必要的依赖）
4️⃣向着稳定的方向依赖（不依赖不稳定的依赖）

<!-- 变化导致的修改，我们要努力消除变化发生时不必要的修改 -->

---

<!-- 

# XP 的简单设计原则

1. 通过测试
2. 消除重复
3. 揭示意图
4. 最少元素

![bg right:65% fit 90%](https://martinfowler.com/bliki/images/beckDesignRules/sketch.png)

--- -->

<!-- _class: invert -->

# <!--fit--> &emsp; &emsp; 案例展示&emsp; &emsp; 

# <!--fit--> &emsp; &emsp; X项目软件设计演化之旅 🗺️&emsp; &emsp; 

 ---

 > 需求一: 在聊天界面支持选择本地图片进行发送 🖼️

 ---

# 实现

```kotlin
class ChooseLocalImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        //省略异步回调 ... ...
        var imageList = getLocalImageList()
        var listAdapter = ListAdapter(imageList)
        lvList.adapter = listAdapter
    }

    private fun getLocalImageList():List<Image>{
        var imageList= mutableListOf<Image>()
        //从本地读出图片列表
        //... ...
        return imageList
    }
}
```

---

 > 需求二: 在聊天界面支持选择网盘图片进行发送 🏞️

 ---

 # 快速实现

```kotlin
class ChooseRemoteImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_remote_image)
        showImageList()
    }

    private fun showImageList() {
        var imageList = getRemoteImageList()
        var listAdapter = ListAdapter(imageList)
        lvList.adapter= listAdapter
    }

    private fun getRemoteImageList():List<Image>{
        //省略异步回调 ... ...
        var imageList = mutableListOf<Image>()
        //从通过API读出图片列表
        //... ...
        return imageList
    }
}

```

---

# 讨论：有没有同学遇到这种情况，你是如何解决:question:

---

# 重复设计 :-1:

## Copy-Paste是最快的实现方法，但会产生「重复设计」

---

# 散弹式修改 :sob:

> 需求：页面除了支持列表布局，还需要支持九宫格布局

---

<!-- _class: invert -->

## <!--fit--> 1️⃣ 消除重复 &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 避免一个变化导致多处修改

---

 # 实现 

```kotlin
class ChooseImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        var imageList= listOf<Image>()
        //省略异步回调 ... ...
        if (isLocal) {
            imageList = getLocalImageList()
        } else {
            imageList = getRemoteImageList()
        }
        var listAdapter = ListAdapter(imageList)
        lvList.adapter = listAdapter
    }

    fun getLocalImageList():List<Image>{
        var imageList= mutableListOf<Image>()
        //从本地读出图片列表
        //... ...
        return imageList
    }

    fun getRemoteImageList():List<Image>{
        var imageList= mutableListOf<Image>()
        //从通过API读出图片列表
        //... ...
        return imageList
    }
}

```

---

# 问题 :question:

* ## 开放封闭原则，对扩展是开放的, 而对修改是封闭的。

---

<!-- _class: invert -->

## <!--fit--> 2️⃣ 分离不同变化&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 避免多个变化导致一处修改

---

# 思考 💡

## 1、显示部分不会经常变

## 2、获取数据源的方式会不断扩展

---

# 分离(单一职责原则)

```kotlin
interface ImageDataSource{
    fun getImageList():List<Image>
}

class LocalImageSource :ImageDataSource{
    override fun getImageList(): List<Image> {
        var imageList= mutableListOf<Image>()
        //从本地读出图片列表
        //... ...
        return imageList
    }
}

class RemoteImageSource :ImageDataSource{
    override fun getImageList(): List<Image> {
        var imageList= mutableListOf<Image>()
        //从通过API读出图片列表
        //... ...
        return imageList
    }
}

```

---

<!-- _class: invert -->

## <!--fit--> 3️⃣  缩小依赖范围&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 不依赖不必要的依赖

---

# 抽象(依赖倒置原则)

```kotlin
class ChooseImageActivity : AppCompatActivity() {

    var imageDataSource:ImageDataSource? = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        //省略异步回调 ... ...
        var imageList = imageDataSource?.getImageList()
        imageList?.let {
            var listAdapter= ListAdapter(imageList)
            lvList.adapter= listAdapter
        }
    }
}

```

---

> 需求三：在聊天界面支持选择本地及网盘文件进行发送 📁

---

# 类型重复

按照既有的代码结构，可以通过Copy Paste快速地实现这个功能

```kotlin
interface FileDataSource{
    fun getFileList():List<FileInfo>
}
class LocalFileSource :FileDataSource{
    override fun getFileList(): List<FileInfo> {
        var FileList= mutableListOf<FileInfo>()
        //从本地读出文件列表
        //... ...
        return FileList
    }
}
class RemoteFileSource :FileDataSource{
    override fun getFileList(): List<FileInfo> {
        var FileList= mutableListOf<FileInfo>()
        //从通过API读出文件片列表
        //... ...
        return FileList
    }
}
```

---

 

# 类型参数化

```kotlin
data class BaseFileInfo(val id:String,val name:String,val path:String,val size:String)

interface FileDataSource<T:BaseFileInfo>{
    fun getFileList():List<T>
}

```

---

# 实现

```kotlin
class ChooseFileActivity: AppCompatActivity() {

   var fileDataSource: FileDataSource<BaseFileInfo>?=null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_file)
    }

    private fun showFileList() {
        var imageList = fileDataSource?.getFileList()
        imageList?.let {
            var listAdapter = ListAdapter(imageList)
            lvList.adapter = listAdapter
        }
    }
}
```

---

> 需求四：选择页面需要支持按时间或大小进行排序 ⏬

---

# 接口修改

## 增加排序字段及排序类型

```kotlin
interface FileDataSource<T:BaseFileInfo>{
    fun getFileList(orderKey:String,orderDesc:Boolean):List<T>
}
```

---

> 🙈 需求五：选择页面需要同时支持排序规则及按文件类型过滤 ⏬ **&** 📔

---

<!-- _class: invert -->

## <!--fit--> 4️⃣ 向着稳定的方向依赖&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 不依赖不稳定的依赖

---

# 条件动态配置

```kotlin
class Condition{
    //字段
    val field:String = ""
    //类型
    val type:OptType = OptType.NULL
    //值
    val value:String = ""
}

interface FileDataSource<T:BaseFileInfo> {
    fun getFileList(conditions: List<Condition>):List<T>
}
```

---

<!-- _class: invert -->

# <!--fit-->&emsp; ❓ 结束 &emsp; 

---

> 🔪 需求六：除了聊天，OA、考勤等其他模块也需要支持选择文件功能 💣💣💣

---

# 讨论：支持多模块调用，你会怎样进行设计:question:

---

# 建造者模式

```kotlin
  ChooseFileActivity.Builder(this)
            .setCondition(mutableListOf())
            .setDataSource(RemoteFileSource())
            .onCreate()
```

---

<!-- _class: invert -->

# <!--fit-->一切围绕着变化

# <!--fit-->由变化驱动，反过来让系统演进的更容易应对变化

