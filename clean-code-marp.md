---
marp: true
theme: gaia
paginate: true
backgroundImage: url('./assets/hero-background.jpg')
---
<!--
_class: lead
-->

![bg right:50% 80%](./assets/code-review.jpg)
# <!-- fit -->**Clean Code**

2020.3.12

---

# 过长的方法 & 过大的类

- 代码冗余，不是最优实现
- 不敢删除遗留代码，怕出问题
- 复制粘贴造成的帝王类
- 类职责太多
- 业务逻辑放在了 UI 中

---

# 命名问题

- Chinglish，英文水平参差不齐
- 方法参数太多，命名随意，无法判断参数副作用
- 滥用单词缩写
- 实现修改了，命名没有修改
- 害怕散弹式修改，不敢重命名或者修改参数

---

# if/else/for嵌套

- 缩进不统一，怕影响 blame 不敢改
- 分支丢失，缺少 else

---

# 缺少文档

- 重要的方法没有注释，如关键算法、BUG 修改
- 无意义的注释太多
- 重要的接口缺少文档
- 接口文档没有集中管理，搜索如大海捞针

---

# 滥用设计模式

- 单例模式初始化顺序引起 NPE
- 单例带来的内存问题，尤其使用 list 或者 map

---

# 匿名内部类 & 回调地狱

- 随意使用匿名内部类
- 回调地狱，可读性极差



---

# 多线程问题

- 只会使用 synchronized 解决同步问题
- 随意 new Thread 或者 new AsyncTask

