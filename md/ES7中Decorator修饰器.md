#ES7中javascript修饰器
##什么是修饰器
修饰器（Decorator）是ES7的一个提案，它的出现能解决两个问题：

* 不同类间共享方法
* 编译期对类和方法的行为进行改变
用法也很简单，就是在类或方法的上面加一个@符。

##修饰类
    @setProp 
    class User {}
	function setProp(target) {
	   target.age = 30
	}
	console.log(User.age)
	
这个例子要表达的是对User类使用setProp这个方法进行修饰，用来增加User类中age的属性，setProp方法会接收3个参数，我们现在接触第一个，target代表User类本身。

##修饰类(自定义参数值)
	@setProp(20)
	class User {}

	function setProp(value) {
	  	return function (target) {
        	target.age = value
    	}
	}
	console.log(User.age)

此例和上面功能基本一致，唯一差别在于值是参考修饰函数传过来的

##修饰方法
	class User {
    	@readonly
    	getName() {
        	return 'Hello World'
    	}
	}
	
	// readonly修饰函数，对方法进行只读操作
	function readonly(target, name, descriptor) {
	    descriptor.writable = false
	    return descriptor
	}
	
	let u = new User()
	// 尝试修改函数，在控制台会报错
	u.getName = () => {
	    return 'I will override'
	}
上例中，我们对User类中的getName方法使用readonly修饰器进行修饰，使得方法不能被修改。第一个参数我们已经知道了，参数name为方法名，也就是readonly，参数descriptor是个啥东西呢，看到这行descriptor.writable = false，我们大家猜的也差不多了，这三个参数对应的就是Object.defineProperty的三个参数，我们来看一下：
	
	Object.defineProperty(obj,"test",{
		configurable: true,
		value:'xxx'
	})
	
我们设置descriptor.writable = false就是让函数不可以被修改，如果我们写成

	descriptor.value = 'function ({ 
		console.log('Hello decorator') 
	}'
那么，输出就是Hello World了，而是Hello decorator，是不是已经意识到修饰器的好处了。现在我们来看看实际工作中，我们用到修饰器的例子

###检查登录demo
这个例子在实际的开发中常用得到，我们一些操作前，必须得判断用户是否登录，比较点赞、结算、发送弹幕...按照之前的写法，我们必须在每一个方法中判断用户的登录情况，然后再进行业务的操作，很显然前置条件和业务又混到了一起，用修饰器，就可以完美的解决这一问题，代码如下：
	
	class User {
	    // 获取已登录用户的用户信息
	    @checkLogin
	    getUserInfo() {
	        /**
	         * 之前，我们都会这么写：
	         *      if(checkLogin()) {
	         *          // 业务代码
	         *      }
	         *  这段代码会在每一个需要登录的方法中执行
	         *  还是上面的问题，执行的前提和业务又混到了一起
	         */
	        console.log('获取已登录用户的用户信息')
	    }
	    // 发送消息
	    @checkLogin
	    sendMsg() {
	        console.log('发送消息')
	    }
	}

	// 检查用户是否登录，如果没有登录，就跳转到登录页面
	function checkLogin(target, name, descriptor) {
	    let method = descriptor.value
	
	    // 模拟判断条件
	    let isLogin = true
	
	    descriptor.value = function (...args) {
	        if (isLogin) {
	            method.apply(this, args)
	        } else {
	            console.log('没有登录，即将跳转到登录页面...')
	        }
	    }
	}
	let u = new User()
	u.getUserInfo()
	u.sendMsg()
	
不管是修改函数的参数值，还是增加属性，还是执行的先决条件，我们都可以使用修饰器，这种编程的方式，就是面对切面编程