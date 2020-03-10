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

## 方法应该只有一个抽象级别

当在你的函数中有多于一个抽象级别时， 你的函数通常做了太多事情。 拆分函数将会提升重用性和测试性。

**不好的：**
```
 void parseBetterJSAlternative(String code){
        String[] REGECES={};
        String[] statements=code.split(" ");
        String[] tokens={};
        for(String regex: Arrays.asList(REGECES)){
               for(String statement:Arrays.asList(statements)){
                   //...
                }
            }
        String[] ast={};
        for(String token:Arrays.asList(tokens)){
                //lex ...
            }

        for(String node:Arrays.asList(ast)){
                //parse ...
            }
        }
```

**好的：**
```
 String[] tokenize(String code){
        String[] REGECES={};
        String[] statements=code.split(" ");
        String[] tokens={};
        for(String regex: Arrays.asList(REGECES)){
            for(String statement:Arrays.asList(statements)){
                    //tokens push
                }
            }
        return tokens;
        }

        String[] lexer(String[] tokens){
        String[] ast={};
        for(String token:Arrays.asList(tokens)){
                //ast push
        }
        return ast;
        }

        void parseBetterJSAlternative(String code){
        String[] tokens=tokenize(code);
        String[] ast=lexer(tokens);
        for(String node:Arrays.asList(ast)){
                //parse ...
            }
        }
```
 
---

## 移除冗余代码

竭尽你的全力去避免冗余代码。 冗余代码是不好的， 因为它意味着当你需要修改一些逻辑时会有多个地方
需要修改。

想象一下你在经营一家餐馆， 你需要记录所有的库存西红柿， 洋葱， 大蒜， 各种香料等等。 如果你有多
个记录列表， 当你用西红柿做一道菜时你得更新多个列表。 如果你只有一个列表， 就只有一个地方需要更
新！

你有冗余代码通常是因为你有两个或多个稍微不同的东西， 它们共享大部分， 但是它们的不同之处迫使你使
用两个或更多独立的函数来处理大部分相同的东西。 移除冗余代码意味着创建一个可以处理这些不同之处的
抽象的函数/模块/类。

让这个抽象正确是关键的， 这是为什么要你遵循 *Classes* 那一章的 SOLID 的原因。 不好的抽象比冗
余代码更差， 所以要谨慎行事。 既然已经这么说了， 如果你能够做出一个好的抽象， 才去做。 不要重复
你自己， 否则你会发现当你要修改一个东西时时刻需要修改多个地方。

**不好的：**
```
 void showDeveloperList(List<Developer> developers){
            for(Developer developer:developers){
                render(new Data(developer.expectedSalary,developer.experience,developer.githubLink));
            }
        }

 void showManagerrList(List<Manager> managers){
            for(Manager manager:managers){
                render(new Data(manager.expectedSalary,manager.experience,manager.portfolio));
            }
        }
```

**好的：**
```
 void showList(List<Employee> employees){
            for(Employee employee:employees){
                Data data=new Data(employee.expectedSalary,employee.experience,employee.githubLink);
                String portfolio=employee.portfolio;
                if("manager".equals(employee)){
                    portfolio=employee.portfolio;
                }
                data.portfolio=portfolio;
                render(data);
            }
        }
```
---

## 移除僵尸代码

僵死代码和冗余代码同样糟糕。 没有理由在代码库中保存它。 如果它不会被调用， 就删掉它。 当你需要
它时， 它依然保存在版本历史记录中。

**不好的：**
```
    void  oldRequestModule(String url) {
        // ...
    }

    void  newRequestModule(String url) {
        // ...
    }

    String  req = newRequestModule;
    inventoryTracker("apples", req, "www.inventory-awesome.io");

```

**好的：**
```
    void  newRequestModule(String url) {
        // ...
    }

    String  req = newRequestModule;
    inventoryTracker("apples", req, "www.inventory-awesome.io");
```
---
## 单一职责原则 (SRP)

正如代码整洁之道所述， “永远不要有超过一个理由来修改一个类”。 给一个类塞满许多功能， 就像你在航
班上只能带一个行李箱一样， 这样做的问题你的类不会有理想的内聚性， 将会有太多的理由来对它进行修改。
最小化需要修改一个类的次数时很重要的， 因为如果一个类拥有太多的功能， 一旦你修改它的一小部分，
将会很难弄清楚会对代码库中的其它模块造成什么影响。

**不好的：**
```
 class UserSettings {
        User user;

        void changeSettings(UserSettings settings) {
            if (this.verifyCredentials()) {
                // ...
            }
        }

        void verifyCredentials() {
            // ...
        }
    }
```

**好的：**
```
    User user;
    UserAuth auth;

    public UserSettings(User user) {
        this.user = user;
        this.auth = new UserAuth(user);
    }

    void changeSettings(UserSettings settings) {
        if (this.auth.verifyCredentials()) {
            // ...
        }
    }

```
---
# 命名问题

- Chinglish，英文水平参差不齐
- 方法参数太多，命名随意，无法判断参数副作用
- 滥用单词缩写
- 实现修改了，命名没有修改
- 害怕散弹式修改，不敢重命名或者修改参数

---

## 使用有意义并且可读的变量名称

**不好的：**
```
 String yyyymmdstr = new SimpleDateFormat("YYYY/MM/DD").format(new Date());
```

**好的：**
```
 String currentDate = new SimpleDateFormat("YYYY/MM/DD").format(new Date());
```
---

## 使用可搜索的名称

我们要阅读的代码比要写的代码多得多， 所以我们写出的代码的可读性和可搜索性是很重要的。 使用没有
意义的变量名将会导致我们的程序难于理解， 将会伤害我们的读者， 所以请使用可搜索的变量名。

**不好的：**
```
// 艹， 86400000 是什么鬼？
 setTimeout(blastOff, 86400000);

```

**好的：**
```
// 将它们声明为全局常量。
 public static final int MILLISECONDS_IN_A_DAY = 86400000;
 setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```
---

## 使用解释性的变量
**不好的：**
```
 String address = "One Infinite Loop, Cupertino 95014";
 String cityZipCodeRegex = "/^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$/";

 saveCityZipCode(address.split(cityZipCodeRegex)[0],
 address.split(cityZipCodeRegex)[1]);
```

**好的：**
```
  String address = "One Infinite Loop, Cupertino 95014";
  String cityZipCodeRegex = "/^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$/";

  String city = address.split(cityZipCodeRegex)[0];
  String zipCode = address.split(cityZipCodeRegex)[1];

  saveCityZipCode(city, zipCode);
```
---

### 避免心理映射

显示比隐式更好

**不好的：**
```
 String [] l = {"Austin", "New York", "San Francisco"};

        for (int i = 0; i < l.length; i++) {
            String li = l[i];
            doStuff();
            doSomeOtherStuff();
            // ...
            // ...
            // ...
            // Wait, what is `$li` for again?
            dispatch(li);
        }
```

**好的：**
```
 String[] locations = {"Austin", "New York", "San Francisco"};

        for (String location : locations) {
            doStuff();
            doSomeOtherStuff();
            // ...
            // ...
            // ...
            dispatch(location);
        }
```
---

### 函数参数 (两个以下最理想)

限制函数参数的个数是非常重要的， 因为这样将使你的函数容易进行测试。 一旦超过三个参数将会导致组
合爆炸， 因为你不得不编写大量针对每个参数的测试用例。

没有参数是最理想的， 一个或者两个参数也是可以的， 三个参数应该避免， 超过三个应该被重构。 通常，
如果你有一个超过两个函数的参数， 那就意味着你的函数尝试做太多的事情。 如果不是， 多数情况下一个
更高级对象可能会满足需求。

当你发现你自己需要大量的参数时， 你可以使用一个对象。

**不好的：**
```
void createMenu(String title,String body,String buttonText,boolean cancellable){}
```

**好的**：
```
 class MenuConfig{
            String title;
            String body;
            String buttonText;
            boolean cancellable;
        }
        void  createMenu(MenuConfig menuConfig){}
```
---


# if/else/for嵌套

- 缩进不统一，怕影响 blame 不敢改
- 分支丢失，缺少 else

---

### 封装条件语句

**不好的：**
```
    if(fsm.state.equals("fetching")&&listNode.isEmpty(){
            //...
        }
```

**好的：**
```
    void shouldShowSpinner(Fsm fsm, String listNode) {
            return fsm.state.equals("fetching")&&listNode.isEmpty();
        }

    if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
            // ...
        }
```
---

### 避免负面条件

**不好的：**
```
    void isDOMNodeNotPresent(Node node) {
            // ...
        }

    if (!isDOMNodeNotPresent(node)) {
            // ...
        }
```

**好的：**
```
    void isDOMNodePresent(Node node) {
                // ...
        }

    if (isDOMNodePresent(node)) {
            // ...
        }
```
---

### 避免条件语句

这看起来似乎是一个不可能的任务。 第一次听到这个时， 多数人会说： “没有 `if` 语句还能期望我干
啥呢”， 答案是多数情况下你可以使用多态来完成同样的任务。 第二个问题通常是 “好了， 那么做很棒，
但是我为什么想要那样做呢”， 答案是我们学到的上一条代码整洁之道的理念： 一个函数应当只做一件事情。
当你有使用 `if` 语句的类/函数是， 你在告诉你的用户你的函数做了不止一件事情。 记住： 只做一件
事情。

**不好的：**
```
 class Airplane{
        int getCurisingAltitude(){
            switch(this.type){
                case "777":
                    return this.getMaxAltitude()-this.getPassengerCount();
                case "Air Force One":
                    return this.getMaxAltitude();
                case "Cessna":
                    return this.getMaxAltitude() - this.getFuelExpenditure();
                }
            }
        }
```

**好的：**
```
    class Airplane {
            // ...
        }

    class Boeing777 extends Airplane {
            // ...
        int getCruisingAltitude() {
            return this.getMaxAltitude() - this.getPassengerCount();
        }
    }

    class AirForceOne extends Airplane {
        // ...
        int getCruisingAltitude() {
            return this.getMaxAltitude();
           }
    }

    class Cessna extends Airplane {
        // ...
        int getCruisingAltitude() {
            return this.getMaxAltitude() - this.getFuelExpenditure();
        }
    }
```
---

# 缺少文档

- 重要的方法没有注释，如关键算法、BUG 修改
- 无意义的注释太多
- 重要的接口缺少文档
- 接口文档没有集中管理，搜索如大海捞针

---

## 仅仅对包含复杂业务逻辑的东西进行注释

注释是代码的辩解， 不是要求。 多数情况下， 好的代码就是文档。

**不好的：**
```
void hashIt(String data) {
        // The hash
        long hash = 0;

        // Length of string
        int length = data.length();

        // Loop through every character in data
        for (int i = 0; i < length; i++) {
            // Get character code.
            char mChar = data.charAt(i);
            // Make the hash
            hash = ((hash << 5) - hash) + mChar;
            // Convert to 32-bit integer
            hash &= hash;
        }
    }
```

**好的：**
```
 void hashIt(String data) {
        long hash = 0;
        int length = data.length();

        for (int i = 0; i < length; i++) {
            char mchar = data.charAt(i);
            hash = ((hash << 5) - hash) + mchar;

            // Convert to 32-bit integer
            hash &= hash;
        }
    }


```
---

## 不要在代码库中保存注释掉的代码

因为有版本控制， 把旧的代码留在历史记录即可。

**不好的：**
```
    doStuff();
    // doOtherStuff();
    // doSomeMoreStuff();
    // doSoMuchStuff();
```

**好的：**
```
    doStuff();
```
---

## 不要有日志式的注释

记住， 使用版本控制！ 不需要僵尸代码， 注释掉的代码， 尤其是日志式的注释。 使用 `git log` 来
获取历史记录。

**不好的：**
```
    /**
     * 2016-12-20: Removed monads, didn't understand them (RM)
     * 2016-10-01: Improved using special monads (JP)
     * 2016-02-03: Removed type-checking (LI)
     * 2015-03-14: Added combine with type-checking (JR)
     */
     
    void combine(String a, String b) {
        return a + b;
    }
```

**好的：**
```
    void combine(String a, String b) {
            return a + b;
    }
```
---

## 避免占位符

它们仅仅添加了干扰，让函数和变量名称与合适的缩进和格式化为你的代码提供视觉结构。

**不好的：**
```
    ////////////////////////////////////////////////////////////////////////////////
    // Scope Model Instantiation
    ////////////////////////////////////////////////////////////////////////////////
    String[] model = {"foo","bar"};

    ////////////////////////////////////////////////////////////////////////////////
    // Action setup
    ////////////////////////////////////////////////////////////////////////////////
    void action(){
        //...
    }

```

**好的：**
```
  String[] model = {"foo","bar"};
        void action(){
            //...
        }
```

# 滥用设计模式

- 单例模式初始化顺序引起 NPE
- 单例带来的内存问题，尤其使用 list 或者 map

## 单例造成的内存泄漏

当调用getInstance时，如果传入的context是Activity的context。只要这个单例没有被释放，那么这个
	Activity也不会被释放一直到进程退出才会释放。

**不好的：**

```
	public class CommUtil {
	    private static CommUtil instance;
	    private Context context;
	    private CommUtil(Context context){
		this.context = context;
	    }

	    public static CommUtil getInstance(Context mcontext){
		if(instance == null){
		    instance = new CommUtil(mcontext);
		}
		return instance;
	    }
```
**好的：**

能使用Application的Context就不要使用Activity的Content，Application的生命周期伴随着整个进程的周期

```
	public class CommUtil {
	    private static CommUtil instance;
	    private Context context;
	    private CommUtil(Context context){
		this.context = context;
	    }

	    public static CommUtil getInstance(Context mcontext){
		if(instance == null){
		    instance = new CommUtil(mcontext.getApplicationContext());
		}
		return instance;
	    }
```

---

# 匿名内部类 & 回调地狱

- 随意使用匿名内部类
- 回调地狱，可读性极差

## 匿名内部类

**不好的：**

异步任务和Runnable都是一个匿名内部类，因此它们对当前Activity都有一个隐式引用。如果Activity在销毁之前，任务还未完成， 那么将导致Activity的内存资源无法回收，造成内存泄漏

```
 
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                SystemClock.sleep(10000);
                return null;
            }
        }.execute();


        new Thread(new Runnable() {
            @Override
            public void run() {
                SystemClock.sleep(10000);
            }
        }).start();
```

**好的：**

使用静态内部类，避免了Activity的内存资源泄漏，当然在Activity销毁时候也应该取消相应的任务AsyncTask::cancel()，避免任务在后台执行浪费资源

```
   static class MyAsyncTask extends AsyncTask<Void, Void, Void> {
        private WeakReference<Context> weakReference;
 
        public MyAsyncTask(Context context) {
            weakReference = new WeakReference<>(context);
        }
 
        @Override
        protected Void doInBackground(Void... params) {
            SystemClock.sleep(10000);
            return null;
        }
 
        @Override
        protected void onPostExecute(Void aVoid) {
            super.onPostExecute(aVoid);
            MainActivity activity = (MainActivity) weakReference.get();
            if (activity != null) {
                //...
            }
        }
    }
    static class MyRunnable implements Runnable{
        @Override
        public void run() {
            SystemClock.sleep(10000);
        }
    }

    new Thread(new MyRunnable()).start();
    new MyAsyncTask(this).execute();
```

---

## 回调嵌套

画一个二维码 (需要在子线程里完成)然后在imageview上显示

**不好的：**

```
new Thread(new Runnable() {
    @Override
    public void run() {
        try {
            Bitmap qrCode = CodeCreator.createQRCode(ShareActivity.this, SHARE_QR_CODE);
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    img_qr_code.setImageBitmap(qrCode);
                }
            });
        } catch (WriterException e) {
            e.printStackTrace();
        }
    }
}).start();
```

**优化版：**
```
 Observable.just(SHARE_QR_CODE)
               .map(new Function<String, Bitmap>() {
                   @Override
                   public Bitmap apply(String s) throws Exception {
                       return  CodeCreator.createQRCode(ShareActivity.this, s);
                   }
               }).subscribe(new Consumer<Bitmap>() {
           @Override
           public void accept(Bitmap bitmap) throws Exception {
               img_qr_code.setImageBitmap(bitmap);
           }
       });
```
**好的：**
```
Observable.just(SHARE_QR_CODE)
                .map(s -> CodeCreator.createQRCode(ShareActivity.this, s))
                .subscribe(bitmap -> img_qr_code.setImageBitmap(bitmap));
```

# 多线程问题

- 只会使用 synchronized 解决同步问题
- 随意 new Thread 或者 new AsyncTask

## new Thread影响

- 每次new Thread新建对象性能差
- 线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源导致死机或oom。
- 缺乏更多功能，如定时执行、定期执行、线程中断

**不好的：**

```

new Thread(new Runnable() {
 
	@Override
	public void run() {
		// TODO Auto-generated method stub
	}
}).start();

```

**好的：**

使用线程池进行管理

- 重用存在的线程，减少对象创建、消亡的开销，性能佳。
- 可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。
- 提供定时执行、定期执行、单线程、并发数控制等功能

```

ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
for (int i = 0; i < 10; i++) {
	final int index = i;
	singleThreadExecutor.execute(new Runnable() {
 
		@Override
		public void run() {
			try {
				System.out.println(index);
				Thread.sleep(2000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	});
}
```

