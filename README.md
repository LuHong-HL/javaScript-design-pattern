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
