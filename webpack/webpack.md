1.基础知识-Symbol.Symbol.toStringTag是一个内置的symbol。通常作为对象的属性键使用，对应的属性值应该为字符串类型。
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toStringTag

2.基础知识-按位与

3.webpack打包后生成代码的执行逻辑
第一种情况：__webpack_require__方法作为文件入口，那么递归执行__webpack_require__方法然后将引用的module执行后返回。

4.commonjs如何加载es6模块
//index.js
const title = requie('title.js');
console.log(title.title);
console.log(title.age);
下面是一个es6模块
// title.js
export default 'title_name';
export const age = 'title_age';

生成代码对应title，其中index没变化
title: function xxx (module,__webpack_exports__,_webpack_require__) {
    _wepback_require.r(__webpack_exports__);
    _wepback_require.d(__webpack_exports__,"age",function() {
        return age;
    });
    __webpack_exports__["default"] = "title_name";
    const age = "title_age";
}
//其中内部代码
_wepback_require.r = function (exports) {
    Object.defineProperty(exports,Symbol.toStringTag,{value:"Module"});
    Object.defineProperty(exports,"__esModule",{value:true});
    //如果exports身上有一个__esModule=true，表示这个模块打包前是一个es6模块。
    //出现了import 或者export关键字的话，那么这个模块就是一个es6模块
    //webpack里面你不管什么模块，都会变成commonjs模块。
}
_wepback_require.d = function (exports,name,getter) {
    Object.defineProperty(exports,name,{enumerable:true,get:getter})
    // 定义一个可枚举的属性。
}


es6加载commonjs
//index
import name,{age} from 'title.js';
console.log(name);
console.log(age);
//title
module.exports = {
    name:'title_name',
    age:'title_age';
}
生成代码对应index,其中title不变
title: function xxx (module,__webpack_exports__,_webpack_require__) {
     _wepback_require.r(__webpack_exports__);
     var _title_ = _webpack_require__('title');
     var _title_default = _webpack_require__.n(_title_);
     console.log(_title_default.a);
     console.log(_title_default['age']);
}
其中内部代码
获取默认导出为了兼容非es6module commonjs 
__webpack_require__.n = function(module) {
		var getter = module && module.__esModule ?
 			function getDefault() { return module['default']; } : // 如果是一个es6模块，那么它的默认导出会挂在导出对象的default属性上。
 			function getModuleExports() { return module; }; // 如果是一个common.js模块，则直接导出对象
             // 给getter函数定一个一个a属性，属性的getter方法getter函数本身
		__webpack_require__.d(getter, 'a', getter);

 		return getter;
 	};
所以，如果想要获取值，那么调用方法就是:_title_default.a


按需加载中，异步模块加载完后，执行逻辑

__webpack_require__.e(/* import() | chunk-lottie */ "vendors~chunk-lottie").then(__webpack_require__.t.bind(null, "xaRr6F", 7));
其中
1.toString(2).padStart(4,'0')转换为2进制

__webpack_require__.t = function(value, mode) {
/******/ 		if(mode & 1) value = __webpack_require__(value);
/******/ 		if(mode & 8) return value;
/******/ 		if((mode & 4) && typeof value === 'object' && value && value.__esModule) return value;
/******/ 		var ns = Object.create(null);
/******/ 		__webpack_require__.r(ns);
/******/ 		Object.defineProperty(ns, 'default', { enumerable: true, value: value });
/******/ 		if(mode & 2 && typeof value != 'string') for(var key in value) __webpack_require__.d(ns, key, function(key) { return value[key]; }.bind(null, key));
/******/ 		return ns;
/******/ 	};