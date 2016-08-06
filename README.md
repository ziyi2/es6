# ECMAScript 6

## 使用环境
### Chrome
访问[chrome://flags/#enable-javascript-harmony](chrome://flags/#enable-javascript-harmony),开启`enable-javascript-harmony`特性,就可以使用`ES6`了.

### Nodejs
升级最新的Nodejs版本,在Nodejs中使用`ES6`.

### Webpack
可以访问[使用Babel 6和Webpack在浏览器端运行ES6和ES7](http://www.tuicool.com/articles/fmUze2M)查看使用方法.

## let 和 const

`ES5`只有两种声明变量的方式, `var`和`function`. 在`ES6`中有6种声明变量的方法,`var`,`function`,`let`,`const`,`import`和`class`.

### let

`let`具有块级作用域,而`var`没有

``` javascript
{
    let a = 10;
    var b = 1;
}

console.log(b); //1 
console.log(a); //a is not defined  
```
进一步说明`let`仅在块级作用域有效

``` javascript
var a = [],
    b = [];
    
for(var i = 0; i < 10; i++) {
    var c1 = i;
    let c2 = i;
    a[i] = function() {
        console.log(c1);
    };

    b[i] = function(){
        console.log(c2);
    };
}

a[5](); //9
b[5](); //5
```
`let`更像`c`语言中的变量,不再具有**变量提升**能力

``` javascript
var a = 1;
let b = 2;

function variable(){
    console.log(a);     //undefined
    console.log(b);     //b is not defined
    var a = 1;
    let b = 2;
}

variable();
```
暂时性死区

``` javascript
let b = 1;
if(true){
    b = 2;  //b is not defined
    let b;
}
```

>提示:`ES6`明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（**temporal dead zone**，简称`TDZ`）。

``` javascript
if(true){
                                //TDZ Start
    typeof(undefinedVariable);  //undefined
    typeof(b);                  //b is not defined  

    b = 2;                      //b is not defined
    b = b + 1;

    let b;                      //TDZ End
    b = 10;
    console.log(b);
}
```

>提示:此时使用`typeof`就会报错,但是如果变量没有声明反而不会报错而输出`undefined`.



`let`不允许重复声明

``` javascript
{
    var b = 1;
    var b = 2;
    console.log(b);     //2
    let a = 1;
    let a = 2;
    console.log(a);     //Identifier 'a' has already been declared
}
```

再次说明`let`新增了块级作用域

```
function f(){
    let a = 1;      //外层代码块不受内层代码块影响
    if(a){
        let a = 2;  //只在该块中有作用域
    }
    console.log(a); //1
}

f();

function f1(){
    var b = 1;
    if(b){
        var b = 2;  //没有块级作用域
    }
    console.log(b); //2
}

f1();
```

>提示: `ES5`只有全局作用域和函数作用域,而没有块级作用域,一般也都是利用立即执行的匿名函数来模拟块级作用域.

函数的块级作用域

`ES5`中规定函数只能在**顶层作用域**和**函数作用域**中声明,而不能在**块级作用域**中声明,在严格模式下会报错[非严格模式下不会报错],`ES6`中引入了类似于`let`的函数声明语句,该函数也是有作用域的,在**块级作用域**之外不能被引用.

``` javascript
function f(){           //第一次f函数声明
    console.log('f is 1!');
}

if(false){              
    function f(){       //第二次f函数声明
        console.log('f is 2');
    }
}

f();                    //f is 1 ES6
                        //f is 2 ES5
```

>提示:  在`ES5`中,`if`语句块中的`f`函数的声明会被提升到**全局作用域(顶层作用域)**,所以输出的是覆盖了第二次声明的`f`函数.但是在`ES6`中,由于语句块中的函数声明只是在该语句块中起作用(只能提升到该**块级作用域**的顶部),不会对语句块外产生影响,所以输出的是第一次声明的`f`函数.

`ES6`中的函数
- 允许在块级作用域内声明函数(在`ES5`中原则上是不允许的)
- 函数声明类似于`var`,会提升到全局作用域或者函数作用域的顶部
- 会提升到块级作用域的顶部

``` javascript
console.log(f);         //undefined 当做未声明过的变量来处理

if(true){       
    console.log(f);     //function f() 函数声明在块级作用域中提升
    function f(){       
        console.log('f is 2');
    }
}
```


### const


``` javascript
const a = 1;
console.log(a);         //1
a = 2;                  //Assignment to constant variable

const b;                //Missing initializer in const declaration
```
>提示: `const`在声明的同时必须赋值,否则会报错.且不能重新赋值.

`const`同样具有块级作用域

``` javascript
{
    const a = 1;
    console.log(a);    //1
}
console.log(a);        //a is not defined
```

和`let`一样也不可重复声明

```javascript
const a = 1;
const a = 2;   //Identifier 'a' has already been declared

var b = 1;
let b = 2;     //Identifier 'a' has already been declared
const b = 3;
```

和`let`一样,变量不会提升声明,同样存在暂时性死区`TDZ`.

``` javascript
const a = 3;

if(true){
    //TDZ Start
    console.log(a); //a is not defined
    const a = 1;    //TDZ End
}
```

对于引用类型,`const`只能保证变量所指向的地址不变,而不能保证地址所在的值不变.

``` javascript
const a = {};
a.name = 'a';
a.name = 'b';
console.log(a); //Object {name: "b"}

a = {           //另一个对象赋值给a
    name: 'c'
}
console.log(a); //Assignment to constant variable.
```
>提示: `a`的属性值变没关系,只要`a`指向的地址不变,但是对`a`重新赋值,则导致`a`指向的地址变化了,这是不允许的. 数组也是一样.

### 全局对象和属性
`let`,`const`,`class`声明的全局变量将不再是全局对象(`window`,`global`)的属性,`var`和`function`仍然不变.

``` javascript
var a=1;
console.log(window.a);  //1

let b=2;
const c=3;
console.log(window.b);  //undefined
console.log(window.c);  //undefined
```


## 变量的解构赋值

### Array解构

将变量封装成数组的形式,然后将表达式右边的数组和对象中提取的值一一对应的赋值给这些变量,就叫做数组解析.

``` javascript
var [a,b,c] = [1,2,3];
console.log(a); //1
console.log(b); //2
console.log(c); //3
```

还可以以嵌套的形式赋值

``` javascript
var [a,[[b],c]] = [1,[[2],3]];
console.log(b); //2
```
其他的一些例子

``` javascript
let [,,a] = ['1','2','3'];
console.log(a); //3

let [b,,,c] = [1,2,3,4,5,6];    //不完全解构,6没用
console.log(b); //1
console.log(c); //4

const [d,...e] = [1,2,3,4,5,6];
console.log(d); //1
console.log(e); //[2,3,4,5,6]
```

如果解构失败,值就是`undefined`

``` javascript
let [a] = [];
console.log(a); //undefined

const [b,c,d] = [3];
console.log(b); //3
console.log(c); //undefined
console.log(d); //undefined
```

如果右边不是数组的形式,则会报错

``` javascript
const [a] = 1;          //1 is not iterable
const [b] = false;
const [c] = NaN;
const [d] = {};         //本身不具备Iterator接口
const [e] = null;
const [f] = undefined;
```
>提示: 转为对象以后不具备`Iterator`接口,或者本身不具备`Iterator`接口.

默认值

``` javascript
const [a = 1] = [];
console.log(a);         //1

const [b = 1] = [4];    
const [d = 1] = [null];
const [e = 1] = [{}];

const [c = 1] = [undefined];

console.log(b);         //4     默认值失效
console.log(d);         //null  默认值失效
console.log(e);         //Object {  } 默认值失效

console.log(c);         //1     默认值有效
```
>提示: 如果右边的数组中的值不严格等于`undefined`,那么左边的默认值是不会生效的.例如`4`和`null`不严格等于`undefined`,所以默认值都失效了.

如果默认值是一个表达式

``` javascript

function f(){
    console.log('aaa');
}

const [a = f()] = [1];
console.log(a);         //1 默认值失效
    
const [b = f()] = [];   //aaa, f()执行了   
```

默认值引用解析赋值的其他变量,但是变量必须已经声明(从左到右)

``` javascript
let [a,b=a] = [2];
console.log(a); //2
console.log(b); //2

let [c=d,d] = [1,2];
console.log(c); //1 右边的值不等于undefined,左边的默认值失效
console.log(d); //2

let [e=f,f] = [1];
console.log(e); //1
console.log(f); //undefined

let [g=h,h=1] = []; //h is not defined
```

### Object解构

数组是按顺序解析的,对象的属性没有次序,左边需要赋值的变量必须和右边的对象中的属性同名,这样才能一一匹配.

``` javascript
let {a,b} = {b:'2',a:'1'};
console.log(a); //1
console.log(b); //2 

let {c} = {b:'2',a:'1'};
console.log(c); //undefined
```

完整的写法
``` javascript
var {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2
```
>提示:采用这种方式赋值的时候真正被赋值的是后者`v_a`而不是`模式a`.

以下的解构中的`a`和`b`是模式,所以不会报错,但是解构中的`c`是变量,属于重复声明,所以报错.

``` javascript
let a,b;
let {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2

let c;
let{c} = {c:1};     //Identifier 'c' has already been declared
```
以下的`a`和`b`并不是变量,而是一种匹配模式而已
``` javascript
let {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2
console.log(a);     //a is not defined
console.log(b);     //b is not defined

let {c,d} = {c:'1',d:'2'};
console.log(c);     //1
console.log(d);     //2
```

嵌套匹配

```javascript
let obj = {
    a:[
        '1',
        {
            b:'2'
        }
    ]
};

let {
    a:[
        v_a,
        {
            b:v_b
        }
    ]
} = obj;

console.log(v_a);   //1
console.log(v_b);   //2
```

>提示: 和上面的一样,此时的`a`和`b`并不是变量,而是一种匹配模式.

对象的解构也可以有默认值

``` javascript
let {a=1,b} = {b:3};
console.log(a); //1

let {c=3} = {c:null};
console.log(c); //null
```

### String解构

``` javascript
const [a,b,c] = 'abc';
console.log(a); //a
console.log(b); //b
console.log(c); //c

let {length:len} = 'hello';
console.log(len);
```
>提示:  'hello'如果转化为对象有一个length属性,所以可以赋值这个属性

### Number or Boolean解构

先将`number`或者`boolean`转换为**包装**对象
``` javascript
let {parseInt: pI} = 123;
console.log(pI === Number.prototype.parseInt);  //true
```
>提示: 相当于最开始的对象解构的同名属性匹配. 当然由于`null`和`undefined`无法转化对象,对它们解析时会报错.

### Function解构

函数的参数解构
``` javascript
function add({a,b,c}){
    return a + b + c.d;
}
let sum = add({a:1,b:2,c:{d:3}});

console.log(sum);       //6
```

>提示:  如果是`JSON`对象,这种方法就非常有用.当然返回的时候同样可以使用这种方式解构.

``` javascript
//Array.prototype.map(function(item,index,arr));
//该方法返回的是新数组
//把[1,2]和[3,4]各看成数组的元素
let arr = [[1,2],[3,4]].map(([a,b]) => a + b);
console.log(arr);   //[3,7]
```

默认值

``` javascript
function f({x=0,y=1} = {}){
    return [x,y];
}

console.log(f({x:1,y:2}));  //[1,2]
console.log(f({x:1}));      //[1,1]
console.log(f({}));         //[0,1]
console.log(f());           //[0,1]
```
注意如果是设置函数参数的默认值,结果将是完全不一样的

``` javascript
function f({x,y} = {x:0,y:1}){
    return [x,y];
}

console.log(f({x:1,y:2}));  //[1,2]
console.log(f({x:1}));      //[1,undefined]
console.log(f({}));         //[undefined,undefined]
console.log(f());           //[0,1]
```

和前面一样,如果值严格等于`undefined`,就会函数的参数的解构的默认值将会生效

``` javascript
var arr = [1,undefined,3].map((x = 2) => x);
console.log(arr);   //[1,2,3]
```

###  ()问题
解构赋值虽然很方便，但是解析起来并不容易。对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。由此带来的问题是，如果模式中出现圆括号怎么处理。`ES6`规定,只要有可能导致解构的歧义,就不得使用圆括号.建议只要有可能，就不要在模式中放置圆括号。
 - 变量声明语句中，不能带有圆括号。
 - 函数参数中，模式不能带有圆括号。
 - 赋值语句中，不能将整个模式，或嵌套模式中的一层，放在圆括号之中。


### 用途

- 交换变量的值

``` javascript
let [x,y] = [1,2];
[x,y] = [y,x];
console.log(x); //2
console.log(y); //1
```

 -  从函数返回多个值
 
``` javascript
 function f(){
    return [1,2,3];
}

let [a,b,c] = f();
console.log(a); //1
```

``` javascript
function f2(){
    return {
        a:2,
        b:3
    };
}

var {a,b} = f2();
console.log(a);     //2
```
>提示: `ES5`中的函数如果要返回多个值只能是数组或者对象的形式,否则只能返回单个值.


 -  函数参数的定义

``` javascript
function f([x,y,z]){
    return x + y + z;
}

f([1,2,3]);
```

>提示: 传入参数也可以快速的一一对应起来.


 -  提取`JSON`数据

``` javascript
var jsonD = {
    name: 'ziyi2',
    age: '23'
};

let {name,age} = jsonD;

console.log(name);  //ziyi2
console.log(age);   //23
```

>提示: 可以快速的提取`JSON`对象的数据或者给函数传入参数.

 -  函数参数的默认值

>提示: 可以避免在函数中使用 `var arg = arg || 'default arg';` 

 -  遍历Map解构

``` javascript
var map = new Map();
map.set('name','ziyi2');
map.set('age',23);

for(let[key,value] of map){
    console.log(key + '-' + value);     //name-ziyi2 age-23
}

for(let[key] of map){
    console.log(key);                   //name age
}
    
for(let[,value] of map){                //ziyi2 23
    console.log(value);
}
```

 >提示: 何部署了`Iterator`接口的对象，都可以用`for...of`循环遍历。`Map`结构原生支持`Iterator`接口.
  
 -  输入模块的指定特定的方法

``` javascript
const  {f1,f2,...} = require("source");
```

 
