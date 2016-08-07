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

>提示:`ES6`明确规定,如果区块中存在`let`和`const`命令,这个区块对这些命令声明的变量,从一开始就形成了封闭作用域.凡是在声明之前就使用这些变量,就会报错.总之,在代码块内,使用`let`命令声明变量之前,该变量都是不可用的.这在语法上,称为“暂时性死区”（**temporal dead zone**,简称`TDZ`）.

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
解构赋值虽然很方便,但是解析起来并不容易.对于编译器来说,一个式子到底是模式,还是表达式,没有办法从一开始就知道,必须解析到（或解析不到）等号才能知道.由此带来的问题是,如果模式中出现圆括号怎么处理.`ES6`规定,只要有可能导致解构的歧义,就不得使用圆括号.建议只要有可能,就不要在模式中放置圆括号.
 - 变量声明语句中,不能带有圆括号.
 - 函数参数中,模式不能带有圆括号.
 - 赋值语句中,不能将整个模式,或嵌套模式中的一层,放在圆括号之中.


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

 >提示: 何部署了`Iterator`接口的对象,都可以用`for...of`循环遍历.`Map`结构原生支持`Iterator`接口.
  
 -  输入模块的指定特定的方法

``` javascript
const  {f1,f2,...} = require("source");
```

###  String扩展

#### 遍历器接口

``` javascript
let str = 'hello es6';

for(let codePoint of str){
    console.log(codePoint); //h e l l o  e s 6
}
```

#### at
用于替代`charAt`方法,可以识别码点大于0xFFFF的字符.

``` javascript
let str = 'hello es6';
str.at(0);  //h
```

#### includes,startsWith,endsWith

``` javascript
let str = 'hello es6';
console.log(str.includes('hel'));       //true
console.log(str.startsWith('h'));       //true
console.log(str.startsWith('hello'));   //true
console.log(str.endsWith('es6'));       //true
console.log(str.endsWith('6'));         //true

//fun(str,startIndex)
console.log(str.includes('hel',2));     //false
console.log(str.includes('llo',2));     //true
```


#### repeat

``` javascript
//repeat(copyCount)
let str = 'hello es6 ';
console.log(str.repeat(3));     //hello es6 hello es6 hello es6 
console.log(str.repeat(3.5));   //hello es6 hello es6 hello es6 
console.log(str.repeat('3.5')); //hello es6 hello es6 hello es6 
console.log(str.repeat(-0.9));  //''
console.log(str.repeat(NaN));   //''
console.log(str.repeat('str')); //''
console.log(str.repeat(-2));    //Invalid count value
```


#### 模板字符串

模板字符串中的空格和换行都会被保留
``` javascript
//repeat(copyCount)
let str = `我是大傻瓜
我是大傻瓜`;

console.log(str);
//我是大傻瓜
//我是大傻瓜
```

直接输出模板而不再使用`+`号连接
``` javascript
<ul id='list'>
</ul>

<ul id='list1'>
</ul>

var name = 'ziyi2',
    age = 23;

$('#list').html(
    '<li>' + name + '</li>' + 
    '<li>'+ age + '</li>'
);

$('#list1').html(`
    <li>${name}</li>
    <li>${age}</li>
`);
```

在模板字符串中嵌入变量需要放在`${}`中
``` javascript
let name = 'Bob',
    time = 'tody';
console.log(`Hello ${name}, how are you ${time}`);  //Hello Bob, how are you tody
```
在`${}`中可以是任意的`JavaScript`表达式,可以进行运算也可以引用对象的属性

``` javascript
let name = 'ziyi2',
    age = 23;

function f(){
    return name + age;
}


$('#list').html(`
    <li>${name + age}</li>
    <li>${f()}</li>
`.trim());
```

报错情况
``` javascript
let name = 'ziyi2',
    msg = `Hello, ${name}`,     //Hello,ziyi2
    hello = `Hello, ${age}`;    //age is not defined
```

模块字符串可以嵌套

``` javascript
let infos = [{name:'ziyi2',age:23},{name:'ziyi3',age:24},{name:'ziyi4',age:25}];

const temp = infos => ` 
    ${infos.map(info => `
        <li>${info.name}</li>
        <li>${info.age}</li>
    `
    ).join('')}
`;

$('#list').html(temp(infos));
```

#### 标签模板

紧跟在函数后面,该函数将被调用来处理这个模板字符串,该功能称为"标签模板"功能.这其实就是函数的一种特殊调用形式,"标签"是指函数,而模板字符串是函数的参数.

``` javascript
alert  `Hello`;
alert('Hello'); 

console.log `Hello`;    //['Hello']
```

但是如果模板字符串中还有变量,函数就会先处理成多个参数,第一个是字符串数组,第二个开始就是变量...

``` javascript
let name = 'ziyi2';
let age = 23;


function tag(s,v_name,v_age){
    console.log(s);         //['Hello','your age is','']
    console.log(v_name);    //ziyi2
    console.log(v_age);     //23
}

tag `Hello ${name}, your age is ${age}`;
```

>提示: `tag`函数实际上以这样的形式调用, `tag(['Hello','your age is',''],name,age);`

另外一种写法
``` javascript
let name = 'ziyi2';
let age = 23;


function tag(s,...args){
    console.log(s);             //['Hello','your age is','']
    console.log(args[0]);       //ziyi2
    console.log(args[1]);       //23

    console.log(arguments[0]);  //['Hello','your age is','']
    console.log(arguments[1]);  //ziyi2
    console.log(arguments[2]);  //23
}

tag `Hello ${name}, your age is ${age}`;
```

这种方式还可以假象代码以其他方式运行,例如

``` javascript
jsx `
    //以jsx代码的方式运行   
`


java `
    //以java代码的方式运行  
`

xml `
    //以xml代码的方式运行   
`
```

以这种方式运行的参数中,刚刚说了第一个参数是字符串数组,这个数组还有一个属性`raw`,这个参数是对字符串数组进行了转义处理过的数组

``` javascript
let name = 'ziyi2';
let age = 23;


function tag(s,...args){
    console.log(s);             //['Hello','your age is','']
    console.log(s.raw);         

    console.log(args[0]);       //ziyi2
    console.log(args[1]);       //23

    console.log(arguments[0]);  //['Hello','your age is','']
    console.log(arguments[1]);  //ziyi2
    console.log(arguments[2]);  //23
}

tag `Hello ${name},\n your age is ${age}`;
```

### String.raw

``` javascript
console.log(String.raw `Hi\nHi`);   //Hi\nHi
```

## Number扩展

### Number.isFinite,Number.isNaN
>提示: 与传统方法`isFinite`和`isNaN`的区别在于传统方法先转换为`Number`再判断,而新方法只对数值有效.

### Number.parseInt,Number.parseFloat
>提示: 将全局的`parseInt`和`parseFloat`方法移植到了`Number`对象上,更加模块化.


### Number.isInteger

``` javascript
console.log(Number.isInteger(2));   //true
console.log(Number.isInteger('2')); //false
console.log(Number.isInteger(2.0)); //true
console.log(Number.isInteger(2.1)); //false
```

### Number.EPSILON

引入一个极小的常量

``` javascript
console.log(Number.EPSILON);        // 2.220446049250313e-16

if(0.1 + 0.2 - 0.3 < Number.EPSILON){
    console.log(0.1 + 0.2 - 0.3);   //5.551115123125783e-17
    console.log(true);              //true
} 
```

### Math.trunc

去除小数部分
``` javascript
console.log(Math.trunc(4.23423423423)); //4
```

### Math.sign
判断正负数,0,NaN,-0

### Math.cbrt
计算一个数的立方根

### Math.hypot
计算所有参数的平方和的平方根

### 指数运算

``` javascript
console.log(2 ** 3);    //8
let a =  2;
console.log(a **= 3);   //8
```

## Array扩展
### Array.from
把类数组转化为数组,例如类似数组的对象和可遍历的对象

``` javascript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '100': 'c',
    length: 3
};

let arr = Array.from(arrayLike);
console.log(arr);   // ["a", "b", undefined]
```

`NodeList`集合就是类数组对象,还有`arguments`对象也是,都可以转化为真正的数组

``` javascript
<p>1</p>
<p>2</p>
<p>3</p>

let lists = document.querySelectorAll('p');

Array.from(lists).forEach(function(item,index,list){
    console.log(item.innerHTML);
});

lists.forEach(function(item){   //lists.forEach is not a function
    console.log(item.innerHTML);
});

```

>提示: `lists`是类数组,本身没有数组的`forEach`方法.

`Iterator`接口的数据结构,`Array.from`都能将其转为数组.

``` javascript
let namesSet = new Set(['ziyi2','ziyi3']);

namesSet.forEach(function(item){    
    console.log(item);          //ziyi2,ziyi3
});

console.log(namesSet.length);   //undefined

console.log(Array.from(namesSet).length);   //2
```

`(...)`扩展运算符也可以将某些数据转为数组

``` javascript
function f(){
    let args = [...arguments];
    args.forEach(function(arg){
        console.log(arg);       //1,2,3,4,5
    });

    arguments.forEach(function(item){   //arguments.forEach is not a function
        console.log(item);
    })

    console.log(args.length);   //5

}

f(1,2,3,4,5);
```

`Array.from`方法还支持类似数组的对象.所谓类似数组的对象,本质特征只有一点,即必须有`length`属性.因此,任何有`length`属性的对象,都可以通过`Array.from`方法转为数组,而此时扩展运算符就无法转换.

``` javascript
let obj = {
    length: 3
};

console.log(Array.from(obj)); //[undefined, undefined, undefined]
```

>提示: 扩展运算符转换不了这个对象.

 `Array.from`还有第二个参数
 

``` javascript
function f(){
    console.log(Array.from(arguments, x => x*x));       //[1, 4, 9, 16]
    //类似于
    console.log(Array.from(arguments).map(x => x*x));   //[1, 4, 9, 16]
}

f(1,2,3,4);
```

稀疏数组转化为非稀疏数组

``` javascript
console.log(Array.from([1,,2,,3], item => item || 0));  //[1, 0, 2, 0, 3]
```

第一个参数指定了第二个参数运行的次数
``` javascript
console.log(Array.from({length:3}, () => 'ziyi2')); //["ziyi2", "ziyi2", "ziyi2"]
```

### Array.of

``` javascript
let arr = Array.of(1,2,3,4);    //[1, 2, 3, 4]
console.log(arr);

arr = Array.of(3);
console.log(arr);               //[3]

arr = Array();
console.log(arr);               //[]

arr = Array(3);
console.log(arr);               //[undefined, undefined, undefined]

arr = Array(3,3);
console.log(arr);               //[3, 3]
```

>提示: `Array.of`就是为了避免`Array`的方法因为参数不同而导致的不同结果.`Array.of`基本上可以替代`Array`或`new Array`. 它的行为非常统一.


### Array.copyWithin
将制定位置的成员赋值到其他位置`Array.prototype.copyWithin(targetIndex,start=0,end=this.length);`

- targetIndex 
从该位置开始替换数据(会覆盖原有位置的元素)
- start
从该位置读取数据
- end
到该位置前停止读取数据

``` javascript
let arr = [1,2,3,4,5].copyWithin(0,4);  //[5,2,3,4,5]
console.log(arr);

arr = [1,2,3,4,5].copyWithin(0,3);  //[4,5,3,4,5]
console.log(arr);
```

### Array.find,Array.findIndex

用于找出符合条件的数组的成员.

``` javascript
let item = [1,2,3,4].find((n) => (console.log(n), n>1));    //1 2
console.log(item);  //2
```
>提示: 执行`n`遍,直到第一个返回`true`的项则停止遍历并返回这个项.如果遍历完了仍然没有找到符合条件的值,则返回`undefined`. `find`和`forEach`等方法一样,回调函数中都有三个参数`item,index,arr`.

用于找出符合条件的数组的成员的位置.

``` javascript
let item = [1,2,3,4].findIndex((n) => (console.log(n), n>1));   //1 2
console.log(item);  //1  arr[1] = 2
```
>提示: 如果没有找到匹配的数组项,则返回`-1`.这两个方法都可以接受第二个参数,用来绑定回调函数的`this`对象.

### Array.fill

可以抹去之前的数据
``` javascript
console.log([1,2,3].fill(4));       //[4, 4, 4]
console.log(new Array(3).fill(4));  //[4, 4, 4]
```

>提示: 初始化的时候应该非常有用吧!

### Array.entries,Array.keys,Array.values

``` javascript
let arr = ['a','b','c'];

for(let key of arr.keys()){
    console.log(key);   //0 1 2
}

for(let item of arr.values()){
    console.log(item);  //error?
}


for(let [key,value] of arr.entries()){
    console.log([key,value]);   //[0, "a"] [1, "b"] [2, "c"] 
}   
```

### Array.includes
`ES7`的方法,`Babel`已经支持,该方法判断参数是否存在于数组中

``` javascript
console.log([1,2,3].includes(3));   // true
```

没有该方法之前,通常使用`indexOf`方法检测是否包含某个值,`indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相等于运算符`（===）`进行判断，这会导致对`NaN`的误判。

``` javascript
console.log([NaN].indexOf(NaN));    //-1
console.log([NaN].includes(NaN));   //true
```
### 稀疏数组

``` javascript
console.log((0 in [undefined,undefined]));  //true
console.log((0 in [,,]));                   //false
```
>提示: 第二个是稀疏数组,第一个不是.

`ES5`对于稀疏数组的处理:
 - `forEach,filter,every,some`都会跳过空位
 - `map`遍历的时候跳过空位,但是返回的时候会保留这个位置的空
 - `join`和`toString`会将空位视为`undefined`,而`undefined`会被处理成空字符串

``` javascript
var arr = [1,,2,,3];
arr.forEach( (item,index) => console.log(item));    //1,2,3
console.log(arr.filter(item => true));              //[1, 2, 3]
console.log(arr.every(item => item >= 1));          //true

console.log(arr.map(item => item*item));            //[1, undefined, 4, undefined, 9]
console.log(arr.join('-'));                         //1--2--3
console.log(arr.toString());                        //1,,2,,3
```

`ES6`明确将空位转为`undefined`,`Array.from`方法会将数组的空位，转为`undefined`

``` javascript
console.log(Array.from([1,,2,,3])); //[1, undefined, 2, undefined, 3]
console.log([...[1,,2,,3]]);        //[1, undefined, 2, undefined, 3]
```

`for...of`循环会遍历空位

``` javascript
let arr = [,,,];

for(let key of arr){
    console.log(key);   //undefined undefined undefined
}
```
>提示: 其他的函数也可以试试是什么情况.

## Function扩展

形参的初始值

``` javascript
function f(x,y='es6'){
    console.log(x + y);
}

f('hello ');            //hello es6
f('hello ','world');    //hello world
f('hello ','');         //hello 
```

构造函数的形参

``` javascript
function Person(name='ziyi2',age=23){
    this.name = name;
    this.age = age;
    
    let name;       //Identifier 'name' has already been declared
}

let p = new Person();
console.log(p);     // Person { name="ziyi2",  age=23}
let p2 = new Person('ziyi3');
console.log(p2);    // Person { name="ziyi3",  age=23}
```
>提示: 函数的形参是默认声明的,所以不能在函数中再次声明,否则会报错.

解构默认值,注意不是函数的形式参数的初始值

``` javascript
function Person({name='ziyi2',age=23}){
    this.name = name;
    this.age = age;
}

var p = new Person({});
console.log(p);         //Person { name="ziyi2",  age=23}
var p1 = new Person({name:'ziyi3'});
console.log(p1);        //Person { name="ziyi3",  age=23}

var p2 = new Person();  //Cannot match against 'undefined' or 'null'.
```

``` javascript
function ajax(url, {method = 'GET', data = {}, type = 'text/plain'}){
    console.log(method);
}

ajax('/add',{});    //GET
ajax('/add');       //Cannot match against 'undefined' or 'null'.
```

>提示: 这种情况下不能省略解构需要的第二个参数,否则就会报错,如果结合解构默认值和函数形式参数的初始值,就可以忽略这个参数了.
 
解构默认值和函数形式参数初始值结合使用

``` javascript
function f({x=0,y=0} = {}) {        //函数的形参初始值是空对象,而结构的默认值是0,0
    console.log([x,y]);
}

function f1({x,y} = {x:1,y:1}) {    //函数的形参的初始值是对象{x:1,y:1},而结构没有默认值
    console.log([x,y]);
}

function f2({x=0,y=0} = {x:1,y:1}) {    
    console.log([x,y]);
}


f();        //[0, 0]    生效的是解构的默认值
f1();       //[1, 1]    生效的是形参的初始值
f2();       //[1, 1]    生效的是形参的初始值,因为这个时候没有解构赋值,所以形参初始值生效

f(2,2);     //[0, 0] 生效的是解构的默认值,传入的参数因为不是对象而无效
f1(2,2);    //[undefined, undefined] 解构没有赋默认值,传入的参数也不是对象
f2(2,2);    //[undefined, undefined] 解构没有赋默认值,传入的参数也不是对象

f({x:2});   //[2, 0] 覆盖了形参的初始值{},同时解构的默认值生效了,y为默认值0
f1({x:2});  //[2, undefined],因为传入的是{x:2}对象,覆盖了默认的初始对象{x:1,y:1},同时因为解构没有初始值所以y是undefined     
f2({x:2});  //[2, 0] 覆盖了形参初始值{x:1,y:1},同时解构的默认值生效了

f({});      //[0, 0] 生效的是解构的默认值
f1({});     //[undefined, undefined] {}覆盖了默认的初始对象{x:1,y:1}
f2({});     //[0, 0] 解构的默认值生效了,形参的初始对象也被覆盖了
```

默认的参数最好放在最后,方便赋值

``` javascript
function f(x=1,y){
    console.log([x,y]);
}

f();            //[1, undefined]
f(5);           //[5, undefined]
f(undefined,1); //[1, 1]    
//f(,1);        //Unexpected token ,

function f1(x,y=1){
    console.log([x,y]);
}

f1();           //[undefined, 1]
f1(1);          //[1, 1]
```

### 函数的length属性

``` javascript
function f(x=1,y){
    console.log([x,y]);
}

function f1(x,y=1){
    console.log([x,y]);
}

function f2(x=1,y,z){
    console.log([x,y]);
}

function f3(x,y,z=1){
    console.log([x,y]);
}

function f4(...args){

}

console.log(f.length);  //0 如果默认参数没放在末尾,那么没有指定默认值得参数个数将会出错
console.log(f1.length); //1 函数未指定默认值得参数个数为1
console.log(f2.length); //0
console.log(f3.length); //2
console.log(f4.length); //0 不计算的
```

### 作用域

``` javascript
let x=1;

function f(x=2,y=x){
    console.log(y);
}

f();    //2
```

``` javascript
let x=1;

function f(y=x){
    console.log(y);
}

f();    //1
```

``` javascript
function f1(f0 = x => a){
    let a = 'inner';
    console.log(f0());
}

f1();   //inner
```


``` javascript
var a = 'a_global';
var b = 'b_global';

function f(a,c = () => console.log(a + b)){
    var a = 'a_inner';
    var b = 'b_inner';
    c();                //a_innerb_inner
    console.log(a);     //a_inner
}

f();
```

### 应用

指定函数必须有参数

``` javascript
function throwArgsError(){
    throw new Error('Missing parameter!');
}

function f(a = throwArgsError()){
    console.log(a);
}
    
f(1);   //1
f();    //Error: Missing parameter!
```
>提示: 如果传入参数,形参初始值就生效了,动态运行函数抛出`error`.

指定函数的参数可以省略
``` javascript
function f(a = undefined){
    console.log(a);
}
    
f();    //undefined
```

### rest参数
`ES6`引入了`rest`参数, 以`...变量名`的形式获取函数的多余参数,这样就不需要使用`arguments`对象了.`rest`参数中的变量代表一个数组,所以数组特有的方法都可以用于这个变量.
``` javascript
function f(...args){
    let sum = 0;

    for(let arg of args){
        sum += arg;
    }

    
    console.log(sum);   //15
}

f(1,2,3,4,5);
```

>提示: `rest`参数之后不能再有其他参数,否则会报错.同时函数的`length`属性不包括`rest`参数.

### (...)扩展运算符

`...数组`扩展运算符可以认为是rest参数的逆运算,将一个数组转为用逗号分隔的参数序列. 注意与`rest`参数的区别.

``` javascript
function sum(x,y,z){
    console.log(x+y+z);
}

function sum1(...parms){
    console.log(parms[0] + parms[1] + parms[2]);
}

let arr = [1,2,3];
console.log(...arr);    //1 2 3
sum(...arr);            //6
sum1(1,2,3);            //6
```

#### 替代数组的apply方法
扩展运算符`...`可以展开数组,所以不需要`apply`方法将数组转为函数的参数,例如`ES5`中的写法

``` javascript
function f(x,y,z){
    console.log(x + y + z);
}

let arr = [1,2,3];

f.apply(null,arr);  //6
```
>提示: `apply`和`call`作用相同,只是`apply`的第二个参数传入的是数组,而`call`从第二个开始是要传入函数的实际参数,所以`call`第二个参数开始后面可以有很多参数.两个函数的第一个参数都是传递`this`的指向对象.


`ES6`中的写法

``` javascript
function f(x,y,z){
    console.log(x + y + z);
}

let arr = [1,2,3];

f(...arr);  //6
```

一个实际的例子

``` javascript
let arr = [1,2,2,3,4,5,6,7,7,9];
console.log(Math.max(1,2,2,3,4,5,6,7,7,9)); //9
console.log(Math.max.apply(null,arr));      //9
console.log(Math.max(...arr));              //9
```
>提示: `Math.max`方法传入的参数不能是数组.

``` javascript
let arr = [1,2];
let arr1 = [3,4];
Array.prototype.push.apply(arr,arr1);
console.log(arr);   //[1, 2, 3, 4]

arr.push.apply(arr,arr1);
console.log(arr);   //[1, 2, 3, 4, 3, 4]

arr.push(...arr1);
console.log(arr);   //[1, 2, 3, 4, 3, 4, 3, 4]
```

#### 扩展运算符的应用

- 数组合并新写法

``` javascript
let arr = [1,2];
let arr1 = [3,4];
var arr2 = arr.concat(arr1);
console.log(arr2);              //ES5 [1, 2, 3, 4]
console.log([...arr,...arr1]);  //ES6 [1, 2, 3, 4]
```

- 与解构赋值结合

``` javascript
let list = [1,2,3,4,5];
let [a,...rest] = list;

//ES6
console.log(a);     //1
console.log(rest);  //[2, 3, 4, 5]

//ES5
let b = list[0];
let c = list.slice(1);
console.log(b);     //1
console.log(c);     //[2, 3, 4, 5]
```

- 实现了`Iterator`接口的对象
详细的查看`Array.from`内容.

- `Map`和`Set`,`Generator`

``` javascript
let map = new Map([
    [1,'ziyi2'],
    [2,'ziyi3'],
]);

let arr = [...map.keys()];
console.log(arr);           //[1, 2]


var go = function* (){
    yield 1;
    yield 2;
    yield 3;
};

console.log([...go()]);     //[1, 2, 3]

let person = {
    name: 'ziyi2',
    age: 34
};

console.log([...person]);   //person is not iterable
```

- name属性

### `=>` 函数

``` javascript
let f= v => v;
console.log(f(1));  //1

//等同于
function f(v){
    return v;
}
```

如果不需要形参或者多个参数,就用`()`代替参数部分

``` javascript
let f= () => 5;
console.log(f());       //5

let f1= (x,y) => x+y;
console.log(f1(3,4));   //7
```

>提示: 单条语句时省略了`return`关键字.

如果代码块多余一条语句,就要用`{}`包裹起来,并且不能省略返回时的`return`关键字

``` javascript
let f= (x) => {
    let a = 1;
    return a + x;
};

console.log(f(5));  //6
```

注意事项
- `this`体内的`this`对象就是定义时所在的对象(死固定的),而不是运行时那个调用此方法的对象

``` javascript
function f(){
    setTimeout(() => {
        console.log(this.name);
    },100);
}

function f1(){
    setTimeout(function(){
        console.log(this.name);
    },100);
}


var name = 'window';
f.call({name:'ziyi2'});     //ziyi2
f1.call({name:'ziyi2'});    //window
```
>提示: 100ms后执行的函数,在`f`中使用了箭头函数后,`this`就指向了`f`中绑定的对象`{name:'ziyi2'}`,而不使用箭头函数则是全局对象.

在箭头函数中不存在`this,arguments,super,new.target`.都是指向包含它的函数对象的相应的该值!

``` javascript
function f(){
    setTimeout(() => {
        console.log(arguments);
    });
}

function f1(){
    setTimeout(function () {
        console.log(arguments);
    });
}

f(1,23,3,4);    //[1, 23, 3, 4]
f1(1,23,3,4);   //[] 
```
>提示: 箭头函数本身不存在变量`arguments`,所以该变量是`f`函数的属性.由于箭头函数没有`this`,所以也不能使用`call,apply,bind`等方法改变`this`的指向.

- 不可以当做构造函数
- 不可以使用`arguments`对象
- 不可以使用`yield`命令,不能用作`Generator`函数

### 嵌套的箭头函数

``` javascript
const plus = a => a + 1;
const mult = a => a * a;
console.log(mult(plus(5))); //36
```

### 函数绑定

箭头函数可以绑定this对象,而不会随着运行时调用对象的变化而变化.`ES7`为了减小显示绑定(`call,apply,bind`)的用法,提出了**函数绑定**.

### 尾调用优化
略

