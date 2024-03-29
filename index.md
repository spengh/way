## JavaScript模块化编程介绍
### 导读<br>
编码的美妙就在于未知带来的惊喜以及控制带来的满足，而Javascript最是能体现这种美妙的语言。有人说它像一匹野马，不受缰络束缚又身强肌壮，自由与力量让它生来强大又难于驾驭（似乎强大的东西通常都较难驾驭），也正是这种本性造就了它的魅力。正是这种心动的着迷，促使人去探索。想写下自己的一些学习过程，一为记录，二也希望能让读到的你借由本人粗浅认知窥探到Javascript的美。限于本人水平有限，如有错误，敬请指正。<br>
本文主要以模块化为主线，试图从Javascript为什么需要模块化开始，介绍Javascript模块化编程的优势以及模块化编程具体方法。本文默认所有读者均有一定编程基础，了解基本编程概念。<br>

### 模块化，让一切清晰有序<br>
随着互联网的深入发展，网页应用变得越来越像桌面应用一样庞大复杂，因此嵌入网页的Javascript代码也随之变得越来越繁杂，很大程度上增加了程序的维护成本，同时也让扩展变得困难。由于Javascript代码可以嵌在页面任何位置，也可以通过外部文件引入，当程序逐渐变大时，这种随意性会更增加程序难度，当维护人员阅读这样的代码时，往往无从下手。<br>
面对这样的问题，我们希望Javascript程序也能像桌面应用那样能使用软件工程的方法进行管理。尽量使编码人员只专注于自己的业务逻辑，以良好封装的部件形式为其它业务提供服务，部件与部件之间相互隔离，通过导入的方式引用其它逻辑。为实现这样的目的，在Javascript社区的努力下，模块化编程的思想逐渐产生。<br>
模块，即实现特定功能的一组业务逻辑组合，每个模块独立存在（不与其它模块耦合）。模块化编码，即将一组实现某个功能的逻辑代码放在一个文件中统一组织管理，通过对外暴露方法提供该模块功能。当其它业务需要使用该功能时，直接引用模块，引用方不需要关系模块内部实现细节，以达到专注自己业务的目的。由此，提高程序可维护性（因为每个模块的都是独立的单一功能实现，维护一个独立的模块比起一团凌乱的代码总要轻松些）、避免了全局变量污染（即命名空间污染。在Javascript中直接定义的变量、函数都是是全局环境中的。）、增强代码复用性（模块化设计的代码天然带有复用性）。模块化，让一切清晰有序！<br>

### 模块化编码演进<br>
本节试图通过对模块化编程的探索演进，逐步说明如何进行模块化编码。<br>
1. 按照“模块化”的定义，将一组逻辑实现在一个独立文件中管理组织。即尽量避免在Html页面中随意嵌入Javascript代码，而通过独立文件设计业务逻辑。有如下设计：<br>
```javascript
/**
 * 实现加操作
 * @param summand 被加数
 * @param addend 加数
 * @returns 和
 */
function add(summand, addend){
    return summand + addend;
}
/**
 * 实现减法操作
 * @param minuend 被减数
 * @param subtrahend 减数
 */
function minus(minuend, subtrahend){
    return minuend - subtrahend;
}
```
假设该功能在一个文件中，文件名为`arithmetic.js`。当其它业务需要使用算数运算时引入`arithmetic.js`，然后调用`add`方法或`minus`方法。然而，这里存在的问题在于，这里定义的add 和 minus是作为全局函数存在的，这样定义的所有函数均处在同一命名空间，即最顶层命名空间来，在多人协作开发时，当程序越来越复杂庞大，很难避免函数或字段定义名称重复，即产生命名空间污染。<br>
2. 面对上文中存在的问题，结合Javascript对象的定义，改进设计，通过Javascript对象封装函数实现，以对象名称来表达命名空间，函数以对象属性形式定义，从而避免严重的命名空间污染。代码如下：<br>
```javascript
var Arithmetic = {

	/**
	 * 实现加操作
	 * @param summand 被加数
	 * @param addend 加数
	 * @returns 和
	 */
	add: function (summand, addend){
		return summand + addend;
	},
	/**
	 * 实现减法操作
	 * @param minuend 被减数
	 * @param subtrahend 减数
	 */
	minus: function (minuend, subtrahend){
		return minuend - subtrahend;
	}
}
```
改进后，所有函数定义均在`Arithmetic`对象中，调用时通过`Arithmetic.add`或`Arithemic.minus`实现具体算数运算。函数名处在`Arithmetic`空间下。可一定程度上避免命名空间污染。这种设计存在的问题请看下面代码:<br>
```javascript
var Arithmetic = {
    //总数
    _count: 0,

    /**
     * 实现加操作
     * @param summand 被加数
     * @param addend 加数
     * @returns 和
     */
    add: function (summand, addend){
        return summand + addend;
    },
    /**
     * 实现减法操作
     * @param minuend 被减数
     * @param subtrahend 减数
     */
    minus: function (minuend, subtrahend){
        return minuend - subtrahend;
    },
    /**
     * 实现累加操作
     * @param value 要累加的值
     */
    accumulate: function(value){
        this._count += value;
    },
    /**
     * 获取总数
     * @returns {number}
     */
    getCount: function(){
        return this._count;
    }
}
```
这里给`Arithmetic`对象添加了累加操作和获取总数的操作，通过`Arithmetic.accumulate`来实现将一个值累加到总数上，对象内部通过`_count`记录总数，累加完成后通过`Arithmetic.getCount`获取总数。然而，本应作为内部变量的`_count`，由于是以对象属性存在的，因此也同函数一起暴露了出去，外部使用者可以通过`Arithmetic._count = x`这样的语句来修改总数的值，从而破坏了功能的完整性。影响程序安全性及结果正确性。<br>
3. 所幸Javascript提供了函数立即执行的方法，因此基于对象封装函数实现，使用函数立即执行来避免可能存在的安全性及结果正确性的问题。设计如下：
```javascript
(function(){
    //总数
    _count: 0;

    var arithmetic = {};
    /**
     * 实现加操作
     * @param summand 被加数
     * @param addend 加数
     * @returns 和
     */
    arithmetic.add = function (summand, addend){
        return summand + addend;
    };
    /**
     * 实现减法操作
     * @param minuend 被减数
     * @param subtrahend 减数
     */
    arithmetic.minus = function (minuend, subtrahend){
        return minuend - subtrahend;
    };
    /**
     * 实现累加操作
     * @param value 要累加的值
     */
    arithmetic.accumulation = function(value){
        this._count += value;
    };
    /**
     * 获取总数
     * @returns {number}
     */
    arithmetic.getCount = function(){
        return this._count;
    };

    return arithmetic;
})();
```
通过匿名函数封装内部变量，使用立即执行初始化函数，只将需要公开的函数暴露出去。这样可避免外界直接修改内部状态，达到封装效果。<br>
4. 对上述匿名函数立即执行的设计方式进一步进行改进优化，为了实现模块的独立性，与其它实现隔离，可以通过参数控制对象上下文环境，设置需要的资源。具体见如下代码：<br>
```javascript
(function(root, factory){
    factory.apply(root, $);
})(window, function(jQuery){
    //总数
    var _count = 0;

    var arithmetic= {};
    /**
     * 实现加操作
     * @param summand 被加数
     * @param addend 加数
     * @returns 和
     */
    arithmetic.add = function (summand, addend){
        return summand + addend;
    };
    /**
     * 实现减法操作
     * @param minuend 被减数
     * @param subtrahend 减数
     */
    arithmetic.minus = function (minuend, subtrahend){
        return minuend - subtrahend;
    };
    /**
     * 实现累加操作
     * @param value 要累加的值
     */
    arithmetic.accumulation = function(value){
        this._count += value;
    };
    /**
     * 获取总数
     * @returns {number}
     */
    arithmetic.getCount = function(){
        return this._count;
    };

    return arithmetic;
});
```
这里假设模块内部实现用到 JQuery 组件，将 JQuery 组建以参数方式传入。<br>
至此，基本良好的一个模块化设计便完成。其它业务功能以类似方式在单独文件中实现，主业务中根据需要导入具体模块。<br>

### 模块化规范<br>
模块化编码，使我们可以方便的在需要时引用别人开发的功能，同时，增强了程序的可维护性和清晰性，为系统良好发展提供保障。为最大化模块化编码带来的好处，需要遵循相应规范。目前主流规范有四种：CommonJS、AMD、CMD、ECMAScript6。<br>
1. CommonJS规范。CommonJS是一套服务端端模块的规范（貌似也有浏览器端的实现）。CommonJS规范是为了解决Javascript的作用域问题，以模块化的方法实现的。每个模块只在自己的命名空间中执行。该规范规定，模块必须通过`module.exports`导出对外的变量和函数，通过`require`来引入其它模块输出。Node.js 就是使用该规范实现的。一个使用CommonJS规范编写的Javascript代码示例如下：<br>
```javascript
(function(){
    //总数
    var _count = 0;

    /**
     * 实现加操作
     * @param summand 被加数
     * @param addend 加数
     * @returns 和
     */
    module.exports.add = function (summand, addend){
        return summand + addend;
    };
    /**
     * 实现减法操作
     * @param minuend 被减数
     * @param subtrahend 减数
     */
    module.exports.minus = function (minuend, subtrahend){
        return minuend - subtrahend;
    };
    /**
     * 实现累加操作
     * @param value 要累加的值
     */
    module.exports.accumulation = function(value){
        this._count += value;
    };
    /**
     * 获取总数
     * @returns {number}
     */
    module.exports.getCount = function(){
        return this._count;
    };
})();
```
假设该模块文件名为`module_arithmetic.js`，则在使用该模块的地方通过如下方式引用：<br>
```javascript
//导入模块
var arithmetic = require("./module_arithmetic.js");
//调用函数获取总数
arithmetic.getCount();
```
然后，CommonJS是同步加载的，浏览器端在执行时需要将每一个Javascript文件（模块文件）从服务端下载到本地，此时，同步加载就显示出了不足。<br>
2. AMD规范。AMD规范通过允许指定回调函数的形式，支持了异步加载。其实现就是require.js。AMD规范中定义了如下两个API：<br>
* define(id, [depends], callback)
* require([module], callback)

其中define用来定义一个模块。require用来加载一个模块。一个AMD规范的例子如下：<br>
```javascript
define('arithmetic',[],function(){
    //总数
    var _count = 0;

    /**
     * 实现加操作
     * @param summand 被加数
     * @param addend 加数
     * @returns 和
     */
    var add = function (summand, addend){
        return summand + addend;
    };
    /**
     * 实现减法操作
     * @param minuend 被减数
     * @param subtrahend 减数
     */
    var minus = function (minuend, subtrahend){
        return minuend - subtrahend;
    };
    /**
     * 实现累加操作
     * @param value 要累加的值
     */
    var accumulate = function(value){
        this._count += value;
    };
    /**
     * 获取总数
     * @returns {number}
     */
    var getCount = function(){
        return this._count;
    };
	return {
		ad: add,
		min: minus,
		acc: accumulate,
		count: getCount
	};
});
```
引用该模块方式为：<br>
```javascript
require(['arithmetic'], function(arithmetic){
	console.log(arithmetic.getCount());
});
```
AMD实现了异步加载，但是在使用`require.js`时，必须提前加载所有的依赖，而无法做到在需要的时候才加载。从某一方面讲，这提高了开发成本。<br>
3. CMD规范。CMD规范从模块书写格式和基本交互格式上定义模块化规范。它提倡延迟加载引用模块，所以克服了AMD必须提前加载所有依赖的的缺点。CMD规范出自`sea.js`。它的模块定义形式为：<br>
	define(factory)

factory为函数时，默认会传入三个参数：`require`,`exports`,`module`，`require`为加载依赖模块函数，`exports`为导出当前模块接口对象。<br>
factory为字符串或对象时，表示该模块本身就是该字符串或对象。<br>
CMD规范的一个例子为：<br>
```javascript
define(function(){
    //引入模块
    var arithmetic = require('./arithmetic.js');
    var df = arithmetic.addf(5, 5);
    //导出接口
    exports.show = function(){
    	alert(df);
    };
});
```
4. ECMAScript6 规范。ECMAScript6中引入了`class`和`module`的概念。并通过`export`和`import` 关键字来进行模块定义。见如下代码示例：<br>
```javascript
(function(){
       /**
        * 实现加操作
        * @param summand 被加数
        * @param addend 加数
        * @returns 和
        */
	export function add(summand, addend){
            return summand + addend;
    	};
	/**
     	 * 实现减法操作
     	 * @param minuend 被减数
     	 * @param subtrahend 减数
     	 */
    	export function minus (minuend, subtrahend){
            return minuend - subtrahend;
    	};
})();
```
假设该模块文件名为`es-arithmetic.js`，则引用该模块方式为：<br>
```javascript
import add from 'es-arithmetic';
add(5, 5);
```
注：如果要从一个模块导入多个函数则多个函数以大括号包裹。<br>
```javascript
    import {add, minus} from 'es-arithmetic';
```
整体加载模块方式看如下示例：<br>
```javascript
module arithmetic from 'es-arithmetic';
arithmetic.add(5, 5);
arithmetic.minus(10, 5);
```
以上为本人针对Javascript模块化编程的一些粗浅理解。模块化也在人们对更优美编码的追求中正不断完善改进，更高的生产效率是我们的不懈奋进的准则。

by 沈小凹
