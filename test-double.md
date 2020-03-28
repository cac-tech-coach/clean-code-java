---
marp: true
theme: default
class: lead
paginate: true
---
<!-- _class: invert -->

# <!--fit--> 遗留代码重构&测试
# 番外篇：测试替身

![bg brightness:0.3](http://5b0988e595225.cdn.sohucs.com/images/20190911/718243ebcaca488eaa50fc2637e3e477.jpeg)

CAC@OPPO by 黄俊彬 & 覃宇

---

# 讨论：要让被测的代码工作，有哪些条件很难满足:question:

---

# 以一段 Retrofit 请求为例...

```java
interface MyService {
  @GET("/user")
  Observable<User> getUser();
}

Retrofit retrofit = new Retrofit.Builder()
    // 服务可能挂掉，或者还没实现，或者网络延时、中断
    .baseUrl("https://example.com/")
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .build();

MyService myService = retrofit.create(MyService.class)

myService.getUser()
        // 异步代码不稳定
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        // 可能与界面代码耦合
        .subscribe(user -> view.load(user));
```

---

# 难以满足的依赖条件

- 还没有实现的代码，比如先定义了接口，但实现还没有写
- 可用性不受控制，比如不稳定的第三方服务、网络
- 数据一直在变化，比如生产环境的数据数据库
- 使用起来成本太高，比如I/O延时、定时任务、物理环境
- 执行时间不稳定，比如异步代码
- 传递依赖太多，创建没完没了

> 可以使用**安全重构**先**解除依赖**，但有没有其他同样成本较低的方法

---

# 什么是测试**替身**

替换**被测系统**的**依赖**的**等价**实现

![bg right fit 80%](http://xunitpatterns.com/Test%20Double.gif)

---

## 还是以这个 Retroft 请求为例

```java
interface MyService {
  @GET("/user")
  Observable<User> getUser();
}

Retrofit retrofit = new Retrofit.Builder()
    // 服务可能挂掉，或者还没实现，或者网络延时、中断
    .baseUrl("https://example.com/")
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .build();

MyService myService = retrofit.create(MyService.class)

myService.getUser()
        // 异步代码不稳定
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        // 可能与界面代码耦合
        .subscribe(user -> view.load(user));
```
> 依赖：Retrofit、网络连接、[https://example.com/]()、View、异步

---

## 1. 使用自定义的 MyService 实现

```java
// 以下是测试代码
// 自定义“假”实现，按照预期返回 User 实例
MyService stubService = new MyService() {
    @Override Observable<User> getUser() {
        return Observable.from(new User());
    }
}

// 使用“假”实现
stubService.getUser()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(user -> view.load(user));

assertThat(view)...
```
> 解除的依赖：Retrofit、网络连接、https://example.com/

---

## 2. 直接测试回调

```java
// 以下是测试代码
User userResponse = null；

// 使用“假”实现
stubService.getUser()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(user -> userResponse = user));

assertThat(user)...
```
> 解除的依赖：View

---

## 3. 替换 Retrofit 的网络请求行为

```java
interface MyService {
  @GET("/user")
  Observable<User> getUser();
}

Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://example.com/")
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .build();

// 以下是测试代码
// 使用 Retrofit 提供的 BehaviorDelegate 自定义“假”实现
public class MyServiceMock implements MyService {
    private final BehaviorDelegate<MyService> delegate;
    public MyServiceMock(BehaviorDelegate<MyService> delegate) {
        this.delegate = delegate;
    }

    public Observable<String> user() {
        return delegate.returningResponse(Observable.from(new User())).user();
    }
}
```

---

## 3. 替换 Retrofit 的网络请求行为(续)

```java
// 使用 Retrofit 提供的 MockRetrofit 组装“假”实现和“假”网络行为
NetworkBehavior behavior = NetworkBehavior.create();
MockRetrofit mockRetrofit = new MockRetrofit.Builder(retrofit)
                .networkBehavior(behavior)
                .build();

BehaviorDelegate<MyService> delegate = mockRetrofit.create(MyService.class);
MyService mockService = new MyServiceMock(delegate);

// 使用 “假”实现
mockService.getUser()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(user -> view.load(user));
assertThat(view)...
```

---

## 3.替换 Retrofit 的网络请求行为(续)

```java
// Retrofit 提供的 BehaviorDelegate 还可以模拟网络失败
behavior.setDelay(0, MILLISECONDS);
        behavior.setVariancePercent(0);
        behavior.setFailurePercent(failurePercent);

// 使用 “假”实现
mockService.getUser()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(user -> view.load(user));

assertThat(view)...
```
> 解除的依赖：https://example.com/、网络连接

---

## 4. 解决异步的稳定性问题

```java
// 以下是测试代码
// RxJava 提供给的“假”异步实现 TestSubscriber
TestSubscriber<User> testSubscriber = TestSubscriber.create();

mockService.name().subscribe(testSubscriber);
testSubscriber.assertValue(expectedUser);
testSubscriber.assertCompleted();
```
> 解除的依赖：异步

---

## 5. 使用本地的“假”服务代替真实服务

```java
// 以下是测试代码
// 让 Retrofit 用本地服务代替 https://example.com/
MockWebServer mockWebServer = new MockWebServer();
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl(mockWebServer.url("/"))
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .build();
MyService myService = retrofit.create(MyService.class)
```

---
## 5. 使用本地的“假”服务代替真实服务(续)
```json 
// 存放在本地测试得 Response 文件
{"name":"qinyu", "phnoe":"11123456789"}
```

```java
// 读取本地文件模拟“假”的 Response
MockResponse response = new MockResponse()
        .setResponseCode(HttpURLConnection.HTTP_OK)
        .setBody(readContentFromFilePath());
mockWebServer.enqueue(response);

myService.getUser()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(user -> view.load(user));

assertThat(view)...
```
> 解除的依赖：网络连接、https://example.com/
---

## 小结：测试替身的种类

![bg right fit 80% ](http://xunitpatterns.com/Types%20Of%20Test%20Doubles.gif)

---

**Dummy**，为了让测试可以进行（编译通过），不会在测试中使用，一般用来填充参数
**Stub**，事先准备好返回数据，在测试中的调用时使用这些数据代替真实调用的返回值
**Spy**，记录如何测试中调用的方法的执行情况，包括调用次数、调用顺序、调用时传入参数
**Fake**，是一种完整的实现，但和生产环境实现不同，但更轻量、更简单，如内存数据库 
**Mock**，使用库自动生成的替身，可以完成 Stub 和 Spy 的功能。

---

## 讨论：上面使用的这些手段分别是哪种替身:question:

1. 使用自定义的 MyService 实现
2. 直接测试回调
3. 替换 Retrofit 的网络请求行为
4. 解决异步的稳定性问题
5. 使用本地的“假”服务代替真实服务

---

## 我们的看法
1. 使用自定义的 MyService 实现（Stub）
2. 直接测试回调（Spy）
3. 替换 Retrofit 的网络请求行为（Stub）
4. 解决异步的稳定性问题（Stub+Spy/Mock）
5. 使用本地的“假”服务代替真实服务（Fake）

---

<!-- _class: invert -->
# <!--fit-->  测试替身的局限性

---

![bg contain 80%](https://docs.microsoft.com/zh-cn/archive/msdn-magazine/2007/september/images/cc163358.fig02.gif)

---

# 讨论：如何保证替身和依赖的行为是**等价**的:question:

---

# 替身始终是**假**的
# 做得越真**投入越大**
# 用得越多**问题越多**

---

### 建议：选择值得信任的外部依赖

- 接口行为有自动化测试保障
- 接口文描述清晰
> 作为技术选择的必要条件

---

### 建议：选择有保障的 Mock 库

- Mockito（使用得最多，可读性最好）
- Robolectric（使用最新 Android 源码在 JVM 上编译，基本上等同原生行为）
- 各种来源库自带的 Mock/Test Utils
- 不建议使用：Powermock

---

### 建议：内部依赖一定要双方共同维护接口“契约”

- 接口 Producer 提供自动化测试和执行结果
- 接口 Consumer 及时根据变化更新 Mock 的行为

---

### 建议：不用或者少用，重构是优先选择

1. 不要“模拟”不确定的行为
2. 不要“模拟”被测类，只模拟它们的依赖
3. 不要模拟传递依赖，只模拟直接依赖
