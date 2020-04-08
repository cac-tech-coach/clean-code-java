---
marp: true
theme: default
class: lead
paginate: true
---

<!-- _class: invert -->

# <!--fit--> &emsp; &emsp; 案例展示&emsp; &emsp; 

# <!--fit--> &emsp; &emsp; 某项目聊天软件设计&emsp; &emsp; 

 ---

 > 需求一: 在聊天界面支持选择本地图片进行发送 🖼️

 ---

# 实现

``` 
class ChooseLocalImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        //省略异步回调 ... ...
        var imageList=getLocalImageList()
        var listAdapter=ListAdapter(imageList)
        lvList.adapter= listAdapter
    }

    fun getLocalImageList():List<Image>{
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

``` 
class ChooseRemoteImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_remote_image)
        showImageList()
    }

    private fun showImageList() {
        var imageList=getRemoteImageList()
        var listAdapter=ListAdapter(imageList)
        lvList.adapter= listAdapter
    }

    fun getRemoteImageList():List<Image>{
        //省略异步回调 ... ...
        var imageList= mutableListOf<Image>()
        //从通过API读出图片列表
        //... ...
        return imageList
    }
}

```

---

# 重复设计 :-1:

## Copy-Paste是最快的实现方法，但会产生「重复设计」、散弹式修改

---

<!-- _class: invert -->

## <!--fit--> 1️⃣ 消除重复 &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 对于完全重复的代码进行消除，合二为一，会让系统更加高内聚、低耦合

---

 # 实现 

``` 
class ChooseImageActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        var imageList= listOf<Image>()
        //省略异步回调 ... ...
        if(isLocal){
            imageList=getLocalImageList()
        }else{
            imageList=getRemoteImageList()
        }
        var listAdapter=ListAdapter(imageList)
        lvList.adapter= listAdapter
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

<!-- _class: invert -->

## <!--fit--> 2️⃣ 分离关注点&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 我们经常需要因为同一类原因，修改某个模块/类。而这个模块的其它部分却保持不变

---

# 思考 💡

## 1、显示部分不会经常变

## 2、获取数据源的方式会不断扩展

---

# 分离

``` 
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

# 依赖倒置原则

``` 
class ChooseImageActivity : AppCompatActivity() {

    var imageDataSource:ImageDataSource? = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_image)
        showImageList()
    }

    private fun showImageList() {
        //省略异步回调 ... ...
        var imageList= imageDataSource?.getImageList()
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

``` 
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

<!-- _class: invert -->

## <!--fit--> 3️⃣  向着稳定的方向依赖&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 

> 耦合点越稳定，依赖方受耦合变化影响的概率就越低

---

# 类型参数化

``` 
data class BaseFileInfo(val id:String,val name:String,val path:String,val size:String)

interface FileDataSource<T:BaseFileInfo>{
    fun getFileList():List<T>
}

```

---

# 实现

``` 
class ChooseFileActivity: AppCompatActivity() {

   var fileDataSource: FileDataSource<BaseFileInfo>?=null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_choose_file)
    }

    private fun showFileList() {
        var imageList= fileDataSource?.getFileList()
        imageList?.let {
            var listAdapter= ListAdapter(imageList)
            lvList.adapter= listAdapter
        }
    }
}
```

