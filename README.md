javascript
========
this的用法：
1.作为对象方法调用,this是对象
	var point= {
		x:0,
		moveTo:function(x){
			this.x=x;
		}
	}
	point.moveTo(1);
	即使是动态绑定到对象上的函数，也遵循这一原理
	function hello(thing) {
	  console.log(this + " says hello " + thing);
	}
	 
	person = { name: "Brendan Eich" }
	person.hello = hello;
	 
	person.hello("world") // still desugars to person.hello.call(person, "world")
	 
	hello("world") // "[object DOMWindow]world"
2.作为"全局"函数调用,this是window
	function func(x){
		this.x =x;
	}
	func(5);
	print x;//x此时为5，是一个全局变量
3.作为内部函数调用,this仍然是window
	var point = { 
	 x : 0, 
	 y : 0, 
	 moveTo : function(x, y) { 
	     // 内部函数
	     var moveX = function(x) { 
	     this.x = x;//this 绑定到了哪里？
	    }; 
	    // 内部函数
	    var moveY = function(y) { 
	    this.y = y;//this 绑定到了哪里？
	    }; 

	    moveX(x); 
	    moveY(y); 
	    } 
	 }; 
	 point.moveTo(1, 1); 
	 point.x; //==>0 
	 point.y; //==>0 
	 x; //==>1 
	 y; //==>1
4.因为3的问题，用了that变量来解决这一问题
	var point = { 
	 x : 0, 
	 y : 0, 
	 moveTo : function(x, y) { 
	      var that = this; 
	     // 内部函数
	     var moveX = function(x) { 
	     that.x = x; 
	     }; 
	     // 内部函数
	     var moveY = function(y) { 
	     that.y = y; 
	     } 
	     moveX(x); 
	     moveY(y); 
	     } 
	 }; 
	 point.moveTo(1, 1); 
	 point.x; //==>1 
	 point.y; //==>1
5.函数作为构造函数调用
	对function Point(x,y){
		this.x = x;
		this.y = y;
	}
	当用new调用时，是构造函数,this是构造的对象；否则是普通函数，this是window
6.使用apply或call调用函数，可切换this
	function Point(x, y){ 
	    this.x = x; 
	    this.y = y; 
	    this.moveTo = function(x, y){ 
	        this.x = x; 
	        this.y = y; 
	    } 
	 } 

	 var p1 = new Point(0, 0); 
	 var p2 = {x: 0, y: 0}; 
	 p1.moveTo(1, 1); 
	 p1.moveTo.apply(p2, [10, 10]);
	 分别将p1和p2作为this调用moveTo方法
	 apply和call的区别是apply取参数数组为输入，call取零散参数
7. according to http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/
	function hello(thing) {
	  console.log("Hello " + thing);
	}
	 
	// this:
	hello("world")
	 
	// desugars to:
	hello.call(window, "world");
	or 
	hell0.call(undefined,"world"); (when using strict in ES5 spec)
8. Function.prototype.bind方法的来历：
	曾经用一个trick来让函数绑定到一个固定的对象上：
	var person = {
	  name: "Brendan Eich",
	  hello: function(thing) {
	    console.log(this.name + " says hello " + thing);
	  }
	}
	 
	var boundHello = function(thing) { return person.hello.call(person, thing); }
	 
	boundHello("world");
	写成通用的格式就是：
	var bind = function(func, thisValue) {
	  return function() {
	    return func.apply(thisValue, arguments);
	  }
	}
	 
	var boundHello = bind(person.hello, person);
	boundHello("world") // "Brendan Eich says hello world"
	bind返回的是函数，做到了一个对象和一个函数的绑定，执行bind返回的函数，该内部函数的this总是该对象。
	后来ES5引入了Function.prototype.bind方法来替代这个惯例用法：
	var person = {
	  name: "Alex Russell",
	  hello: function() { console.log(this.name + " says hello world"); }
	}
	 
	$("#some-div").click(person.hello.bind(person));
	这个用法对传callback时候很有用，因为它可以把成员函数变成普通函数
9.jQuery内部实现，用了很多匿名callback函数，this默认都为window,不太有用，因此用了call方法把this换成有用的对象。
10. 当使用eval执行javascript代码时，this是eval调用者


git remote set-url origin git@github.com:AlunYou/learning.git
解决git push 403错误

