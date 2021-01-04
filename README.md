# javaScript设计模式

### 单例模式

**定义：** 保证一个类仅有一个实例，并提供一个访问它的全局访问点。

**说明：** 要实现一个标准的单例模式并不复杂，无非是用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例时，直接返回之前创建的对象。当然我们要把创建对象和管理单例的职责分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。

**使用场景：** 单例模式是一种常用的模式，有一些对象我们往往只需要一个，比如线程池、全局缓存、浏 览器中的 window 对象等。在 JavaScript 开发中，单例模式的用途同样非常广泛。试想一下，当我 们单击登录按钮的时候，页面中会出现一个登录浮窗，而这个登录浮窗是唯一的，无论单击多少 次登录按钮，这个浮窗都只会被创建一次，那么这个登录浮窗就适合用单例模式来创建。

**核心代码：**

``` javascript
    /*
     * 单例模式
     * @params {Function} 创建实例的方法
     * @return 唯一的实例
     */
    var getSingle = function (fn) {
      var result
      return function () {
        return result || (result = fn.apply(this, arguments))
      }
    }	
```

**例子：**

```javascript
    /*
     * 单例模式
     * @params {Function} 创建实例的方法
     * @return 唯一的实例
     */
    var getSingle = function (fn) {
      var result
      return function () {
        return result || (result = fn.apply(this, arguments))
      }
    }

    var createObj = function () {
      return {
        name: '我是单例模式,只创建一次'
      }
    }

    var createSingleObj = getSingle(createObj)

    console.log(createSingleObj()) // { name: '我是单例模式，只创建一次' }
    console.log(createSingleObj() === createSingleObj()) // true
```

### 策略模式

**定义：** 定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

**说明：** 定义一系列的算法，把它们各自封装成策略类，算法被封装在策略类内部的方法里。在客户对 Context 发起请求的时候，Context 总是把请求委托给这些策略对象中间的某一个进行计算。

**使用场景：** 在程序设计中，我们也常常遇到类似的情况，要实现某一个功能有多种方案可以选择。比如一个压缩文件的程序，既可以选择 zip 算法，也可以选择 gzip 算法；不同绩效计算奖金；表单校验等等。

**核心代码&&例子：**

``` javascript
    /*
     * 策略模式
     */
    var strategies = { // 策略对象
      'A': function (params) {
        return params + 'A'
      },
      'B': function (params) {
        return params + 'B'
      },
      'C': function (params) {
        return params + 'C'
      }
    }

    var useStrategy = function (strategy, params) {
      return strategies[strategy](params)
    }

    console.log(useStrategy('A', '你好')) // 你好A
    console.log(useStrategy('C', '你好')) // 你好C
```

### 代理模式

**定义：** 代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问。

**说明：** 代理模式的关键是，当客户不方便直接访问一个对象或者不满足需要的时候，提供一个替身对象来控制对这个对象的访问，客户实际上访问的是替身对象。替身对象对请求做出一些处理之后，再把请求转交给本体对象。我们在编写业务代码的时候，往往不需要去预先猜测是否需要使用代理模式。当真正发现不方便直接访问某个对象的时候，再编写代理也不迟。

**使用场景：** 虚拟代理图片的预加载；缓存代理用于ajax异步请求数据；保护代理拦截不符合要求的请求等等。

#### 虚拟代理

**图片预加载技术例子：** 图片预加载是一种常用的技术，如果直接给某个 img 标签节点设置 src 属性， 由于图片过大或者网络不佳，图片的位置往往有段时间会是一片空白。常见的做法是先用一张 loading 图片占位，然后用异步的方式加载图片，等图片加载好了再把它填充到 img 节点里，这种 场景就很适合使用虚拟代理。

``` javascript
    /*
     * 虚拟代理
     * 图片预加载的实现
     */
    var image = (function () { // 展示图片
      var imgNode = document.createElement('img')
      document.body.appendChild(imgNode)
      return function (src) {
        imgNode.src = src
      }
    })()

    var proxyImage = (function () { // 代理请求图片
      var img = new Image()
      img.onload = function () { // 图片请求完毕，直接展示
        image(this.src)
      }
      return function (src) {
        image('<本地图片地址>')
        img.src = src
      }
    })()

    proxyImage('<服务器的图片地址>')
```

#### 缓存代理

**说明：** 缓存代理可以为一些开销大的运算结果提供暂时的存储，在下次运算时，如果传递进来的参 数跟之前一致，则可以直接返回前面存储的运算结果。下面的例子是同步的，如果是异步的话，可以通过回调函数把结果存放到代理对象缓存中。比如：ajax异步请求数据。

**核心代码&&例子：**

``` javascript
    /*
     * 缓存代理
     * 乘积的缓存实现
     */
    var mult = function () { // 乘积函数
      var result = 1
      for(var i = 0, len = arguments.length; i < len; i++){
        result *= arguments[i]
      }
      return result
    }

    var proxyMult = (function () { // 代理请求图片
      var cache = {}
      return function () {
        var args = Array.prototype.join.call(arguments, ',')
        if (args in cache) {
          return cache[args]
        }
        return cache[args] = mult.apply(this, arguments)
      }
    })()

    console.log(proxyMult(1, 2, 3, 4, 5)) // 120 //计算出来的结果
    console.log(proxyMult(1, 2, 3, 4, 5)) // 120 //使用缓存获取的结果
```

### 迭代器模式

**定义：** 迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

**说明：** 迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。迭代器模式是一种相对简单的模式，简单到很多时候我们都不认为它是一种设计模式。目前 的绝大部分语言都内置了迭代器。我们平时用的Array.property.forEach()，for...in...，for...of...这些都是迭代器。

**使用场景：** 循环访问聚合对象中的各个元素

**例子：**

``` javascript
    /*
     * 迭代器模式
     */
    var each = function (array, callback) { // 迭代器
      for (var i = 0, len = array.length; i < len; i++) {
        callback.call(array[i], i, array[i])
      }
    }

    var fn = function (index, item) { // 数组的下标 index 数组的项 item
      console.log(index, item)
    }
    each(['value1', 'value2', 'value3'], fn) // 0 "value1" 1 "value2" 2 "value3"
```

**例子：** JavaScript 中的 `Array.prototype[@@iterator]()` 会返回一个迭代器对象，类似下面的例子，@@iterator 这个是 javascript 内置实现的

``` javascript
    /*
     * 迭代器模式
     * ES5语法模拟JavaScript中的迭代器
     */
    var createIteratorObj = function (items) { // 生成迭代器对象
      var i = 0
      return {
        next: function () {
          var done = (i >= items.length)
          var value = !done ? items[i++] : undefined

          return { // next() 方法返回结果对象 value是值，done为true的时候代表迭代结束
            value: value,
            done: done
          }
        }
      }
    }
    var iterator = function (iteratorObj) { // 迭代器
      var item = null
      do {
        item = iteratorObj.next() // 返回 {value: xxx, done: xxx}
        console.log(item.value)
      }while (!item.done)
    }

    var iteratorObj = createIteratorObj(['value1', 'value2', 'value3'])
    iterator(iteratorObj) // value1 value2 value3 undefined
```

### 发布-订阅模式

**定义：** 发布—订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知。

**说明：** 发布—订阅模式的优点非常明显，一为时间上的解耦，二为对象之间的解耦。它的应用非常 广泛，既可以用在异步编程中，也可以帮助我们完成更松耦合的代码编写。发布—订阅模式还可 以用来帮助实现一些别的设计模式，比如中介者模式。从架构上来看，无论是 MVC 还是 MVVM， 都少不了发布—订阅模式的参与，而且 JavaScript 本身也是一门基于事件驱动的语言。 当然，发布—订阅模式也不是完全没有缺点。创建订阅者本身要消耗一定的时间和内存，而 且当你订阅一个消息后，也许此消息最后都未发生，但这个订阅者会始终存在于内存中。另外， 发布—订阅模式虽然可以弱化对象之间的联系，但如果过度使用的话，对象和对象之间的必要联系也将被深埋在背后，会导致程序难以跟踪维护和理解。特别是有多个发布者和订阅者嵌套到一 起的时候，要跟踪一个 bug 不是件轻松的事情。 在 JavaScript 开发中，我们一般用事件模型来替代传统的发布—订阅模式。

**例子：** 

``` javascript
    /*
     * 发布-订阅者模式
     * 需要手动安装的对象
     */
    var event = { // 发布-订阅者事件对象
      clientList: [], // 订阅信息列表
      listen: function (key, fn) { // 订阅事件
        if (!this.clientList[key]) {
          this.clientList[key] = []
        }
        this.clientList[key].push(fn)
      },
      trigger: function () { // 发布事件
        var key = Array.prototype.shift.call(arguments) // 取出第一个事件名参数，剩余的是数据参数
        var fns = this.clientList[key]
        if (!fns || fns.length === 0) { // 没有绑定订阅信息
          return false
        }
        for ( var i = 0, fn; fn = fns[i]; i++ ) { // 遍历对应订阅事件的回调函数
          fn.apply(this, arguments)
        }
      },
      remove: function (key, fn) { // 取消订阅事件
        var fns = this.clientList[key]
        if (!fns) { // key对应的消息没有被订阅，则直接返回
          return false
        }
        if (!fn) { // 没有传入具体的回调函数，则取消对应key的所有订阅
         return fns && (fns.length = 0)
        }
        // 只需要取消相应的订阅回调函数
        for (var len = fns.length - 1; len >= 0; len--) {
          var _fn = fns[len]
          if (_fn === fn) {
            fns.splice(len, 1) // 删除订阅的回调函数
          }
        }
      }
    }
    
    var installEvent = function (obj) { // 给对象安装发布-订阅功能
      for (var i in event) { // 遍历发布-订阅者事件对象
        obj[i] = event[i]
      }
    }

    // 使用
    var obj = {}
    installEvent(obj) // 为obj安装发布-订阅功能
    var myFn1 = function (params) {
      console.log(params)
    }
    var myFn2 = function (params) {
      console.log(params)
    }
    obj.listen('myClick', myFn1) // 添加订阅
    obj.listen('myClick', myFn2)
    obj.trigger('myClick', 'success') // 发布订阅 输出：success success
    obj.remove('myClick', myFn1) // 取消订阅
    obj.trigger('myClick', 'afterRemove') // 发布订阅 输出：afterRemove
```

**核心代码 && 例子：** 

``` javascript
    /*
     * 发布-订阅者模式
     * 全局的订阅-发布对象
     */
    var event = (function () {
      var clientList = [] // 订阅信息列表

      var listen = function (key, fn) { // 订阅事件
        debugger
        if (!clientList[key]) {
          clientList[key] = []
        }
        clientList[key].push(fn)
      }

     var trigger = function () { // 发布事件
        var key = Array.prototype.shift.call(arguments) // 取出第一个事件名参数，剩余的是数据参数
        var fns = clientList[key]
        if (!fns || fns.length === 0) { // 没有绑定订阅信息
          return false
        }
        for ( var i = 0, fn; fn = fns[i]; i++ ) { // 遍历对应订阅事件的回调函数
          fn.apply(this, arguments)
        }
      }

      var remove = function (key, fn) { // 取消订阅事件
        var fns = clientList[key]
        if (!fns) { // key对应的消息没有被订阅，则直接返回
          return false
        }
        if (!fn) { // 没有传入具体的回调函数，则取消对应key的所有订阅
         return fns && (fns.length = 0)
        }
        // 只需要取消相应的订阅回调函数
        for (var len = fns.length - 1; len >= 0; len--) {
          var _fn = fns[len]
          if (_fn === fn) {
            fns.splice(len, 1) // 删除订阅的回调函数
          }
        }
      }

      return { // 暴露出去的方法
        listen,
        trigger,
        remove
      }
    })()
    
    // 使用
    var myFn1 = function (params) {
      console.log(params)
    }
    var myFn2 = function (params) {
      console.log(params)
    }
    event.listen('myClick', myFn1) // 添加订阅
    event.listen('myClick', myFn2)
    event.trigger('myClick', 'success') // 发布订阅 输出：success success
    event.remove('myClick', myFn1) // 取消订阅
    event.trigger('myClick', 'afterRemove') // 发布订阅 输出：afterRemove
```

**提醒：** 必须先订阅再发布吗？上面的代码如果先发布再订阅的话，发布的消息就会丢失，无法成功订阅到。那如果我们要实现可以先发布再订阅的功能怎么办？为了满足这个需求，我们要建立一个存放离线事件的堆栈，当事件发布的时候，如果此时还没有订阅者来订阅这个事件，我们暂时把发布事件的动作包裹在一个函数里，这些包装函数将被存入堆栈中，等到终于有对象来订阅此事件的时候，我们将遍历堆栈并且依次执行这些包装函数，也就是重新发布里面的事件。当然离线事件的生命周期只有一次，所以刚才的操作我们只能进行一次。

### 命令模式

**定义：** 命令模式中的命令（command）指的是一个执行某些特定事情的指令。

**说明：** 有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么，此时希望用一种松耦合的方式来设计软件，使得请求发送者和请求接收者能够消除彼此之间的耦合关系。

**核心代码 && 例子：** 

宏命令例子：宏命令是一组命令的集合，通过执行宏命令的方式，可以一次执行一批命令，他是命令模式与组合模式的联用产物。想象一下，家里有一个万能遥控器，每天回家的时候，只要按一个特别的按钮，它就会帮我们关上房间门，然后开空调，最后打开电视。

``` javascript 
    /*
     * 命令模式
     * 宏命令例子
     */
    var closeDoorCommand = {
      execute: function () {
        console.log('关门')
      }
    }
    var openFanCommand = {
      execute: function () {
        console.log('开风扇')
      }
    }
    var openTelevisionCommand = {
      execute: function () {
        console.log('开电视机')
      }
    }

    var MacroCommand = function () { // 宏命令
      return {
        commandLists: [], // 命令列表
        add: function (command) {
          this.commandLists.push(command)
        },
        execute: function () {
          var command // 命令 function
          for (var i = 0, len = this.commandLists.length; i < len; i++) {
            command = this.commandLists[i]
            command.execute()
          }
        }
      }
    }

    // 使用
    var macroCommand = MacroCommand()
    macroCommand.add(closeDoorCommand)
    macroCommand.add(openFanCommand)
    macroCommand.add(openTelevisionCommand)
    macroCommand.execute() // 输出：关门 开风扇 开电视机
```

### 组合模式

**定义：** 组合模式就是用小的子对象来构建更大的对象，而这些小的子对象本身也许是由更
小的“孙对象”构成的。

**说明：** 组合模式可以让我们使用树形方式创 建对象的结构。我们可以把相同的操作应用在组合对象和单个对象上。在大多数情况下，我们都可以忽略掉组合对象和单个对象之间的差别，从而用一致的方式来处理它们。 然而，组合模式并不是完美的，它可能会产生一个这样的系统：系统中的每个对象看起来都与其他对象差不多。它们的区别只有在运行的时候会才会显现出来，这会使代码难以理解。此外， 如果通过组合模式创建了太多的对象，那么这些对象可能会让系统负担不起。

**使用场景：** 扫描文件夹、遥控器命令

**核心代码&&例子：** 

基本对象可以被组合成更复杂的组合对象，组合对象又可以被组合， 这样不断递归下去，这棵树的结构可以支持任意多的复杂度。在树最终被构造完成之后，让整颗树最终运转起来的步骤非常简单，只需要调用最上层对象的 `execute` 方法。每当对最上层的对象 进行一次请求时，实际上是在对整个树进行深度优先的搜索，而创建组合对象的程序员并不关心这些内在的细节，往这棵树里面添加一些新的节点对象是非常容易的事情。

``` javascript 
    /*
     * 命令模式
     * 展示宏命令中组合模式的强大例子
     */
    var closeDoorCommand = {
      execute: function () {
        console.log('关门')
      }
    }
    var openFanCommand = {
      execute: function () {
        console.log('开风扇')
      }
    }
    var openTelevisionCommand = {
      execute: function () {
        console.log('开电视机')
      }
    }

    var openSoundCommand = {
      execute: function () {
        console.log('开音响')
      }
    }

    var MacroCommand = function () { // 宏命令
      return {
        commandLists: [], // 命令列表
        add: function (command) {
          this.commandLists.push(command)
        },
        execute: function () {
          var command // 命令 function
          for (var i = 0, len = this.commandLists.length; i < len; i++) {
            command = this.commandLists[i]
            command.execute()
          }
        }
      }
    }

    // 使用
    var macroCommand1 = MacroCommand()
    macroCommand1.add(openTelevisionCommand)
    macroCommand1.add(openSoundCommand)

    var macroCommand2 = MacroCommand()
    macroCommand2.add(closeDoorCommand)
    macroCommand2.add(openFanCommand)
    macroCommand2.add(macroCommand1)
    macroCommand1.execute() // 输出：开电视机 开音响
    macroCommand2.execute() // 输出：关门 开风扇 开电视机 开音响
```

### 模板方法模式

**说明：** 模板方法模式是一种只需使用继承就可以实现的非常简单的模式。模板方法模式由两部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。 

**核心代码&&例子：** 

``` javascript 
    /*
     * 模板方法模式
     * 咖啡和茶的经典例子
     */
     var Beverage = function () {}
     Beverage.prototype.boilWater = function () { // 煮水
        console.log('把水煮沸')
     }
     Beverage.prototype.brew = function () { // 冲泡
        throw new Error('子类必须重写brew方法') // 防止没有重写此方法
     }
     Beverage.prototype.pourInCup = function () { // 倒进杯子
        throw new Error('子类必须重写pourInCup方法')
     }
     Beverage.prototype.addCondiments = function () { // 添加调味料
        throw new Error('子类必须重写addCondiments方法')
     }
     Beverage.prototype.customerWantsCondiments = function () { // 自定义是否需要调料 hook
        return true // 默认需要调料
     }
     Beverage.prototype.init = function () {
       this.boilWater()
       this.brew()
       this.pourInCup()
       if (this.customerWantsCondiments()) { // 如果钩子函数返回true，则需要调料
          this.addCondiments()
       }
     }

     var CoffeeWithHook = function () {}
     CoffeeWithHook.prototype = new Beverage() // 继承饮料的类
     CoffeeWithHook.prototype.brew = function () {
       console.log('用沸水冲泡咖啡')
     }
     CoffeeWithHook.prototype.pourInCup = function () {
       console.log('把咖啡倒进杯子')
     }
     CoffeeWithHook.prototype.addCondiments = function () {
       console.log('加糖和牛奶')
     }
     CoffeeWithHook.prototype.customerWantsCondiments = function () {
       return window.confirm('请问需要调料吗？')
     }

     var coffeeWithHook = new CoffeeWithHook() // 创建咖啡的实例
     //确认需要调料 输出：把水煮沸 用沸水冲泡咖啡 把咖啡倒进杯子 加糖和牛奶
     //不需要调料 输出：把水煮沸 用沸水冲泡咖啡 把咖啡倒进杯子
     coffeeWithHook.init() // 初始化实例
```

