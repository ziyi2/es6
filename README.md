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


## 对象的扩展

### 属性简写

``` javascript
let a = '1';
let b = {a};
console.log(b); //Object { a="1"}
```


``` javascript
function f(x,y){
    console.log({x,y}); // console.log({x:x,y:y});
}
f(1,2); //Object { x=1,  y=2}
```

>提示: `{}` 内的是属性名,同时这个属性名作为变量的值作为属性值.需要注意的是`{}`的属性名总是字符串.


### 方法简写

``` javascript
var o = {

    sum(x,y) {  //sum: function(x,y) {
        console.log(x + y);
    },

    mult(x,y) {
        console.log(x * y);
    }
};

o.sum(1,2); //3
```

如果是`Generator`函数

``` javascript
var gen = {
    *m() {
        yield 'hello world';
    }
};
```

对象字面量的属性名可以是`JavaScript`表达式

``` javascript
var prop = 'ziyi2';
function f(){
    return 'prop';
}

let obj = {
    [prop + '1']: 'ziyi21',
    [f() + '2']: 'prop2'
};

console.log(obj);   //Object { ziyi21="ziyi21",  prop2="prop2"}
```

>提示:  方法作为一个变量的属性也可以使用`JavaScript`表达式.


### 方法的name属性

### Object.is
与 `===` 的作用基本一致,同时使`NaN`等于自身, `+0`和`-0`不相等.

``` javascript
console.log(+0 === -0);         //true
console.log(NaN === NaN);       //false
console.log(Object.is(+0,-0));  //false
console.log(Object.is(NaN,NaN));//true
```

### Object.assign

第一个参数是目标对象,之后的参数都是源对象
``` javascript
let target = {a:1};
let source1 = {b:2};
let source2 = {c:3};
Object.assign(target,source1,source2);
console.log(target);    //Object { a=1,  b=2,  c=3}
```
>提示: 类似于`extend`函数的作用,如果有同名属性,目标对象的属性值会被覆盖.

``` javascript
let target = {a:1};
let source1 = {a:2};
let source2 = {a:3};
Object.assign(target,source1,source2);
console.log(target);    //Object {a=3}
```

>提示: 该方法只拷贝源对象的自身属性(不拷贝继承属性),也不拷贝不可枚举的属性. 

该方法实现的是浅拷贝,而不是深拷贝,例如如果源对象某个属性的值是对象,那么目标对象拷贝得到的这个是这个对象的引用

``` javascript
let x = {a:{b:2}, c:3};
let y = {};

Object.assign(y,x);

x.a.b = 111;
x.c = 222;
console.log(y.a.b); //111 变了
console.log(y.c);   //3
```

补充一下深拷贝和浅拷贝
``` javascript
//浅拷贝
let x = {a:1,b:2};

let y = x;
x.a = 2;            //x对象变了y对象也变了
console.log(y.a);   //2 x对象和y对象引用同一个地址


//深拷贝
let c = {};

for(let key in x){
    c[key] = x[key];
}

x.a = 3;
x.b = 4;            //x和c对象不是引用同一个地址空间
console.log(c);     //Object { a=2,  b=2}
```

当处理数组时,会把数组的索引当成属性来执行

``` javascript
let arr = [1,2,3,4,5];
let arr1 = [2,1];
Object.assign(arr,arr1);
console.log(arr);   //[2, 1, 3, 4, 5]
```
#### 用途
- 为对象添加属性

``` javascript
class Person {
    constructor(name,age) {
        Obeject.assign(this,{name,age});
    }
}
```

- 为对象添加方法

``` javascript
class Person {
    constructor(name,age) {
        Obeject.assign(this,{name,age});
    }
}


Object.assign(Person.prototype, {
    fun1() {

    },
    fun2() {

    }
});

//类似于
Person.prototype.fun1 = function() {};
Person.prototype.fun2 = function() {};
```

- 克隆对象

``` javascript
function clone(obj){
    return Object.assign({},obj);
}

//拷贝带源对象的继承值
function clone1(obj){
    let proto = Object.getPrototypeOf(obj);
    return Object.assign(Object.create(proto),obj);
}
```

合并返回新对象

``` javascript
let merge = (...args) => Object.assign({},...args);
```

### 属性的可枚举性
-  `for...in`循环：只遍历对象自身的和继承的可枚举的属性
-  `Object.keys()`：返回对象自身的所有可枚举的属性的键名
-  `JSON.stringify()`：只串行化对象自身的可枚举的属性

`Object.assign()`会忽略不可枚举属性.操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用`for...in`循环，而用`Object.keys()`代替.

### 属性的遍历
- `for...in`
只遍历对象自身的和继承的可枚举的属性（不含`Symbol`属性）.
- `Object.keys()`
包括对象自身的（不含继承的）所有可枚举属性（不含`Symbol`属性）. `Object.getOwnPropertyNames`
包含对象自身的所有属性（不含`Symbol`属性，但是包括不可枚举属性).
`Object.getOwnPropertySymbols(obj)`
包含对象自身的所有`Symbol`属性.
- `Reflect.ownKeys(obj)`
返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

遍历的次序规则
 - 首先遍历所有属性名为数值的属性，按照数字排序
 - 其次遍历所有属性名为字符串的属性，按照生成时间排序
 - 最后遍历所有属性名为Symbol值的属性，按照生成时间排序

### `__proto__`,Object.setPrototypeOf,Object.getPrototypeOf
#### `__proto__`
`__proto__`属性（前后各两个下划线），用来读取或设置当前对象的prototype对象。目前，所有浏览器（包括IE11）都部署了这个属性.

#### Object.setPrototypeOf
用来设置一个对象的`prototype`对象.它是`ES6`正式推荐的设置原型对象的方法.格式如下

```
Object.setPrototypeOf(obj,prototype)
```

#### Object.getPrototypeOf

### Objetct.keys,Objetct.values,Objetct.entries

- `Objetct.keys`
遍历对象的（不含继承的）可遍历的属性名,返回的是数组
- `Objetct.values (ES7)`
遍历对象的（不含继承的）可遍历的属性值,返回的是数组
- `Objetct.entries (ES7)`
遍历对象的（不含继承的）可遍历的属性的键值对,返回的是数组

### Symbol

#### 概述
`ES5`中的属性只能是字符串,在别人封装的对象的基础上,增加新的方法可能会出现重复,所以在`ES6`中新增了一种数据类型`Symbol`,也就是说在`ES6`中对象的属性可以是字符串也可以是`Symbol`类型.

>提示: `ES5`中的数据类型有`Undefined`,`Null`,`Boolean`,`String`,`Number`,`Object`,在`ES6`中新增了`Symbol`.

凡是属性名是Symbol类型,这些属性就都是独一无二的.

``` javascript
let s = Symbol();
let s1 = Symbol();

obj = {};
obj[s] = '1';
obj[s1] = '2';

console.log(s === s1);  //false
```

>提示: `Symbol`和`String`一样.由于它是基本数据类型,所以不需要使用`new`关键字.

即使传入的参数一样,也还是唯一的

``` javascript
let s = Symbol('ziyi2');
let s1 = Symbol('ziyi2');

console.log(s === s1);  //false
```
`Symbol`类型的数据不能隐式转换为字符串,所以在对象中使用属性的时候不能使用`.`运算符,因为`.`运算符后面跟的默认都是字符串.

``` javascript
let s = Symbol('ziyi2');
let s1 = Symbol('ziyi2');
 
var obj = {};
obj.s = '1';    //.后面的s其实是字符串
console.log(obj['s']);  

obj[s] = '2';

for(let key in obj){    //不能遍历Symbol属性
    console.log(key);   //s 这个是字符串属性
}

console.log(Object.getOwnPropertySymbols(obj));     //[Symbol(ziyi2)] 这个是Symbol属性
console.log(s + '12334');   //can't convert symbol to string
console.log(s.toString() + '1234'); //Symbol(ziyi2)1234
```

>提示: 只能显示转化为字符串,由于`.`属性后面跟的是字符串,所以不能用`Symbol`变量用作对象的`.`属性.所以`Symbol`值必须放在方括号之中.

``` javascript
let s = Symbol('ziyi2');
 
obj = {

    [s](arg) {
        console.log('1');
    }
};

obj[s]('1');    //1
```
>提示:  `Symbol`用来表示对象的属性只能采用`[]`表示法.

可以用来定义不同的常量

``` javascript
const status = {
    success: Symbol('success'),
    error: Symbol('error')
};

function f(sta){
    switch(sta){
        case status.success:
            console.log('1');
            break;

        case status.error:
            console.log('2');
            break;

        default:
            break;      
    }
}

f(status.success);  //1
f(status.error);    //2 
```

#### 属性遍历

- `Object.getOwnPropertySymbols`
- `for...in`
- `Object.getOwnPropertyNames`
- `Reflect.ownKeys`
- `Object.keys`
>提示: 注意`Object.keys`和`for...in`的区别.

####  Symbol.for,Symbol.keyFor
如果想使用同一个`Symbol`值,`Symbol.for`方法接受一个字符串作为参数,然后先搜索有没有该参数作为名称的`Symbol`值,有则返回,没有则创建一个该字符串为名称的`Symbol`值.

```javascript
let s = Symbol('foo');
let s1 = Symbol.for('foo');
console.log(s === s1);  //false
let s2 = Symbol.for('foo');
console.log(s1 === s2); //true
```

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的`Symbol`。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.keyFor`用于返回登记的值

``` javascript
let s = Symbol('foo');
let s1 = Symbol.for('foo');
console.log(s === s1);  //false
let s2 = Symbol.for('foo');
console.log(s1 === s2); //true

console.log(Symbol.keyFor(s));  //undefined
console.log(Symbol.keyFor(s1)); //foo
```

防止导出的对象的属性被覆盖

``` javascript
const s = Symbol.for('foo');

function F(name){
    this.name = name;
}

if(!global[s]){
    global[s] = new F('ziyi2');
}

module.exports = global[s]; //导出的全局变量的该属性不会被修改
```

#### 其他方法
- Symbol.hasInstance 
- Symbol.isConcatSpreadable
- Symbol.species
- Symbol.match
- Symbol.replace
- Symbol.search
- Symbol.split
- Symbol.iterator
- Symbol.toPrimitive 
- Symbol.toStringTag
- Symbol.unscopables

### Proxy和Reflect

#### Proxy
用于修改某系操作的默认行为,可以理解为对目标对象做了拦截,访问对象之前,先执行拦截,所以可以对外界的访问行文进行过滤和改写.

生成`Proxy`实例,`target`表示要拦截的目标对象,`hander`用来定制拦截行为,注意`hander`也是一个对象

``` javascript
let proxy = new Proxy(target,handler);
```

``` javascript
let proxy = new Proxy({},{  //{}是对象
    get: function(target,property){
        return 11;
    }
});

console.log(proxy.name);    //11
console.log(proxy.age);     //11
```

>提示: `hanlder`对象里的拦截函数都有两个参数,一个是目标对象`target`,一个是所要访问的属性`property`. 以上的拦截函数`get`总是返回11,所要访问任何属性都得到11.要使得`Proxy`起作用，必须针对`Proxy`实例（上例是`proxy`对象）进行操作，而不是针对目标对象（上例是空对象）进行操作.


如果`handler`没有设置任何拦截，那就等同于直接通向原对象.

``` javascript
let person = {
    name:'ziyi2',
    age: 11
};

let proxy = new Proxy(person,{});   //handler是一个空对象

proxy.name = 'ziyi3';       //相当于修改了person对象的name属性
proxy.home = 'Hangzhou';    //相当于给person对象创建了一个home属性并赋值

console.log(person.name);   //ziyi3
console.log(person.home);   //Hangzhou
```
>提示:  此时访问`proxy`就等同于访问`person`.

Proxy实例也可以作为其他对象的原型对象

``` javascript
let proxy = new Proxy({},{
    get(target,property) {
        return 35;
    }
}); 

let obj = Object.create(proxy); //proxy是obj的原型对象

console.log(obj.name);  //35 
```


#### 拦截方法

- `get(target,prop,receiver)`
拦截对象属性的读取,最后一个参数后续说明.
- `set(target,prop,value,receiver)`
拦截对象属性的设置,返回一个布尔值.
...

- `construct(target,args)`
拦截`Proxy`实例作为构造函数调用的操作，比如`new proxy(...args)`.


``` javascript
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);
      if (index < 0) {
        propKey = String(target.length + index);
      }
      return Reflect.get(target, propKey, receiver);
    }
  };

  let target = [];
  target.push(...elements);
  return new Proxy(target, handler);
}

let arr = createArray('a', 'b', 'c');
console.log(arr[-1]); //c
```

`construct`方法用于拦截`new`命令

``` javascript
var p = new Proxy(function(){},{
    construct(target,args) {
        return {value:args[0]*10};
    }
});

console.log(new p(2).value);    //20
```

>提示:`construct`方法返回的必须是一个对象，否则会报错

#### Reflect
将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上.未来的新方法将只部署在Reflect对象上.

修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`

 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为.

``` javascript
let obj = {
    name: 'ziyi2'
};

console.log('name' in obj);             //true
console.log(Reflect.has(obj,'name'));   //true
```

``` javascript
let obj = {
    name: 'ziyi2'
};


console.log('name' in obj);             //true
console.log(Reflect.has(obj,'name'));   //true


Reflect.set(obj,'age',23);
console.log(obj.age);                   //23
```

`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为

``` javascript
Proxy(target, {
  set: function(target, name, value, receiver) {
    var success = Reflect.set(target,name, value, receiver);
    if (success) {
      log('property ' + name + ' on ' + target + ' set to ' + value);
    }
    return success;
  }
});
```

上面代码中，`Proxy`方法拦截`target`对象的属性赋值行为。它采用`Reflect.set`方法将值赋值给对象的属性，然后再部署额外的功能

#### Reflect对象的方法
- `Reflect.get(target, name, receiver)`
查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`.如果`name`属性部署了读取函数，则读取函数的`this`绑定`receiver`

``` javascript
var obj = {
  get foo() { return this.bar(); },
  bar: function() { ... }
};

// 下面语句会让 this.bar()
// 变成调用 wrapper.bar()
Reflect.get(obj, "foo", wrapper);
```

- `Reflect.set(target, name, value, receiver)`
...

### Set和Map
#### Set

ES6提供了新的数据结构`Set`.它类似于数组，但是成员的值都是唯一的，没有重复的值.`Set`本身是一个构造函数，用来生成`Set`数据结构.

``` javascript
let s = new Set();
let arr = [1,2,3,3,3,3,3,3,4,5,6,7,8,9];
arr.map(x => s.add(x)); //add方法向Set结构加入成员
for(let i of s){
    console.log(i);     //1,2,3,4,5,6,7,8,9 不会加入重复的值
}
console.log([...s]);    //[1,2,3,4,5,6,7,8,9]
let s1 = new Set(arr);  //接受一个数组参数
console.log([...s1]);   //[1,2,3,4,5,6,7,8,9]
console.log(s1.size);   //9

//数组去重
[...new Set(arr)];
```

>提示: `Set`不能加重复的`NaN`,`Set`不能加入重复的值,加入的值不会发生类型转换.

判断是否重复,也就是是否相等,使用`===`
``` javascript
let s = new Set();
s.add('5');
s.add(5);
console.log([...s]);    //["5", 5]
console.log(s.size);    //2
```

#####  Set的方法和属性
属性
- `size` 
成员总数
- `constructor`
构造函数

方法
- `add`
添加Set成员,返回Set结构本身
- `delete`
返回一个布尔值,表示是否删除成功
- `has`
判断是否是Set的成员,返回布尔值
- `clear` 
清除所有成员,没有返回值

``` javascript
let s = new Set();
s.add('5');
s.add(5);

console.log([...s]);    //["5", 5] 转化为数组
console.log(s.size);    //2

let arr = Array.from(s);
console.log(arr);       //["5", 5] 转化为数组

//数组去重方法二

function dedupe(arr){
    return Array.from(new Set(arr));
}

console.log(dedupe([1,1,2,3,4,5,5]));   //[1, 2, 3, 4, 5]
```

##### 遍历
 - `keys()`
 - `vaules()`
 - `entries()`
 - `forEach()`

``` javascript
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```

其实还可以使用`for...of`遍历`Set`.`Set`结构的实例的`forEach`方法，用于对每个成员执行某种操作，没有返回值.

#### WeakSet
类似于`Set`略.

#### Map
Map是比对象更灵活的一种数据结构,对象本身就就是由键值对组成的,但是属性只能是字符串,Map的键则可以用对象等更复杂方式来表示.

把对象`o`当做`m`的一个键

``` javascript
let m = new Map();
let o = {};

m.set(o,'o object');
console.log(m.get(o));  //o object
console.log(m.has(o));  //true

console.log(m.delete(o));   //true
console.log(m.has(o));  //false
```

接受一个数组作为参数,该数组的成员仍然是一个个数组,每个数组中都有两个元素表示键和值

``` javascript
let m = new Map([['name','ziyi2'],['age','23']]);
console.log(m.size);        //2
console.log(m.get('name')); //ziyi2
console.log(m.has('name')); //true
```

如果键相同,那么后面的就会把前面的覆盖掉
``` javascript
let m = new Map([['name','ziyi2'],['name','23']]);
console.log(m.size);        //1
console.log(m.get('name')); //23
console.log(m.has('name')); //true

m.set('b','ziyi2');
console.log(m.get('b'));    //ziyi2

m.set(['a'],"ziyi2");
console.log(m.get(['a']));  //undefined
```

>提示: 最后一个是数组,其实是两个不同的地址,所以没办法获取值,就跟对象是一样的

##### 方法和属性
类似于`Set`.

##### 遍历方法
类似于`Set`.也可以使用`...`扩展运算符.

#### WeakMap
略.

### Iterator和for...of循环
`ES5`中的表示集合的属性类型只有`Array`和`Object`,`ES6`中又新增了`Map`和`Set`.需要一种统一的接口机制，来处理所有不同的数据结构,遍历器（`Iterator`）就是这样一种机制.它是一种接口，为各种不同的数据结构提供统一的访问机制.任何数据结构只要部署`Iterator`接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）.

`Iterator`的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是`ES6`创造了一种新的遍历命令`for...of`循环，`Iterator`接口主要供`for...of`消费.

`Iterator`的遍历过程:

（1）创建一个指针对象，指向当前数据结构的起始位置.也就是说，遍历器对象本质上，就是一个指针对象.
（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员.
（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员.
（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置.

每一次调用`next`方法，都会返回数据结构的当前成员的信息.具体来说，就是返回一个包含`value`和`done`两个属性的对象.其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束.

``` javascript
function createIterator(array){
    let nextIndex = 0;
    return {
        next () {
            return nextIndex < array.length ? {value: array[nextIndex ++], done: false} :
                                              {value: undefined, done: true};
        }
    };
}


let it = createIterator([1,2]);
console.log(it.next());     //Object { value=1,  done=false}
console.log(it.next());     //Object { value=2,  done=false}
console.log(it.next());     // Object { value=undefined, done=true}
```

上面的函数的作用就是返回一个遍历器对象,对数组执行这个函数,就会返回该数组的遍历器对象(指针对象)`it`.

在`ES6`中，有些数据结构原生具备`Iterator`接口（比如数组），即不用任何处理，就可以被`for...of`循环遍历，有些就不行（比如对象）.原因在于，这些数据结构原生部署了`Symbol.iterator`属性（详见下文），另外一些数据结构没有.凡是部署了`Symbol.iterator`属性的数据结构，就称为部署了遍历器接口.调用这个接口，就会返回一个遍历器对象.

#### 数据结构的默认Iterator接口
`ES6`规定，默认的`Iterator`接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（`iterable`）。**调用`Symbol.iterator`**方法**，就会得到当前数据结构默认的遍历器生成函数。**

在`ES6`中，有三类数据结构原生具备`Iterator`接口：`Array`、类数组对象、`Set`和`Map`结构。


``` javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

console.log(iter.next()); // { value: 'a', done: false }
console.log(iter.next()); // { value: 'b', done: false }
console.log(iter.next()); // { value: 'c', done: false }
console.log(iter.next()); // { value: undefined, done: true }
```
上面代码中，变量`arr`是一个数组，原生就具有遍历器接口，部署在`arr`的`Symbol.iterator`属性上面。所以，调用这个属性，就得到遍历器对象。

上面提到，原生就部署`Iterator`接口的数据结构有三类，对于这三类数据结构，不用自己写遍历器生成函数，`for...of`循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的`Iterator`接口，都需要自己在`Symbol.iterator`属性上面部署，这样才会被`for...of`循环遍历。

对象（`Object`）之所以没有默认部署`Iterator`接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。


类似数组的对象调用数组的`Symbol.iterator`方法

``` javascript
let iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接引用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //a b c
}

console.log([...iterable]); //["a", "b", "c"]
```

需要注意的是普通对象部署数组的`Symbol.iterator`方法并没有效果.

``` javascript
let iterable = {
    a: 'a',
    b: 'b',
    c: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接引用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //undefined undefined undefined
}

console.log([...iterable]); //[undefined, undefined, undefined]

let  iter = iterable[Symbol.iterator]();
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { done=true,  value=undefined}
```

如果`Symbol.iterator`方法对应的不是遍历器生成函数（即会返回一个遍历器对象），解释引擎将会报错

``` javascript
var obj = {};

obj[Symbol.iterator] = () => 1;

[...obj] //TypeError: (intermediate value).next is not a function
```

有了遍历器接口(`Iterator`接口),数据结构就可以用`for...of`循环遍历,也可以使用`while`循环遍历


``` javascript
let iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接饮用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //a b c
}

console.log([...iterable]); //["a", "b", "c"]

let  iter = iterable[Symbol.iterator]();

let result = iter.next();

while(!result.done){
    let x = result.value;
    console.log(x);   //a b c
    result = iter.next();
}
```

#### 调用Iterator接口的场合
有一些场合会默认调用`Iterator`接口（即`Symbol.iterator`方法）,例如`for...of`循环

##### 解构赋值

对数组和`Set`结构进行解构赋值时,会默认调用`Symbol.iterator`方法

``` javascript
let set = new Set().add('a').add('b').add('c');
let [x,y,z] = set;
let [first,...rest] = set;
console.log(rest);  //["b", "c"]
```

##### `...`扩展运算符
该运算符也会调用默认的`iterator`接口

``` javascript
let str = 'hello';
console.log([...str]);  //["h", "e", "l", "l", "o"]

let arr = ['r','e'];
console.log([...str,...arr]);   //["h", "e", "l", "l", "o", "r", "e"]
```

>提示: 只要某个数据接口部署了`iterator`接口,就可以对它使用扩展运算符,将其转为数组. `let arr = [...iterable];`

##### `yield*`

`yield*`后面跟的是一个可遍历的结构,会调用该结构的遍历器接口

``` javascript
let generator = function* (){
    yield 1;
    yield* [2,3,4];
    yield 5;
};

let iterator = generator();

console.log(iterator.next());   //Object { value=1,  done=false}
console.log(iterator.next());   //Object { value=2,  done=false}
console.log(iterator.next());   //Object { value=3,  done=false}
console.log(iterator.next());   //Object { value=4,  done=false}
console.log(iterator.next());   //Object { value=5,  done=false}
console.log(iterator.next());   //Object { value=undefined,  done=true}
```

##### 其他场合

数组的遍历会调用遍历器接口,所以任何接受数组作为参数的场合,其实都调用了遍历器接口
- `for...of`
- `Array.from`
- `Map,Set,WeakMap,WeakSet`
- `Promise.all`
- `Promise.race`

#### 字符串的Iterator接口
字符串也是一个类数组对象,也原生具有`Iterator`接口

``` javascript
let str = 'hello';

let iterator = str[Symbol.iterator]();

console.log(iterator.next());   //Object { value=h,  done=false}
console.log(iterator.next());   //Object { value=e,  done=false}
console.log(iterator.next());   //Object { value=l,  done=false}
console.log(iterator.next());   //Object { value=l,  done=false}
console.log(iterator.next());   //Object { value=o,  done=false}
console.log(iterator.next());   //Object { value=undefined,  done=true}
```

>提示:调用`Symbol.iterator`方法返回一个遍历器对象,该对象具有`next`方法,从而实现遍历.

#### Iterator接口与Generator函数
`Symbol.iterator`方法的最简单实现

``` javascript
let iterable = {};

iterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};

console.log([...iterable]); //[1,2,3]

//简洁写法


let obj = {
    *[Symbol.iterator]() {
        yield 'hello',
        yield 'world'
    }
};

for(let value of obj){
    console.log(value); //hello world
}

let objIter = obj[Symbol.iterator]();
console.log(objIter.next());    //Object { value="hello",  done=false}
console.log(objIter.next());    //Object { value="world",  done=false}
console.log(objIter.next());    //Object { done=true,  value=undefined} 
```

#### 遍历器对象的方法

- next
- return
- throw

>提示: 在`Generator`中讲解.


#### `for...of`

##### Array

`for...of`循环内部调用的是数据接口的`Symbol.iterator`方法.

``` javascript
let arr = [1,2];
let iterator = arr[Symbol.iterator]();

for(let value of arr){  //原生调用iterator接口
    console.log(value); //1 2
}

for(let value of iterator){
    console.log(value); //1 2
}
```

以上两种写法相等,需要注意的是,`for...of`可以替代数组中的`forEach`方法.

同时,在`ES5`中的`for...in`方法可以获取键名,但是没办法获取键值,在ES6中使用`for...of`方法可以获取键值.


``` javascript
let arr = [1,2];

for(let value of arr){  //获取键值
    console.log(value); //1 2
}


for(let key in arr){    //获取键名(数组中的索引,对象中的属性名)
    console.log(key);   //0 1
}
```
同时,`for...of`在数组的遍历器接口中只返回具有数字索引的属性,这一点跟`for...in`循环也有差异

``` javascript
let arr = [1,2];

arr.name = 'arr';


for(let value of arr){  //只遍历数组的索引
    console.log(value); //1 2
}


for(let key in arr){    //当然是遍历所有属性名
    console.log(key);   //0 1 name
}
```

##### Set和Map
`Set`和`Map`和`Array`一样,原生具有`Iterator`接口,可以直接使用`for...of`循环.


``` javascript
let name = new Set(['ziyi2','ziyi3']);

for(let e of name){ 
    console.log(e);  //ziyi2 ziyi3
}

let person = new Map();

person.set('name','ziyi2');
person.set('age',23);

for(let [key,value] of person){
    console.log(key + ':' + value); //name:ziyi2 age:23
}

```

>提示: Set结构遍历时,返回的是一个值,而Map结构遍历时,返回的是一个具有键值对的数组.

##### 计算生成的数据结构
以下三个方法调用后返回遍历器对象

- entries 
遍历`[键名,键值]`组成的数组.`Map`结构的`Iterator`接口,默认就是调用`entries`方法.
- keys
遍历所有的键名
- values
遍历所有的键值

``` javascript
let arr = [1,2];
arr.name = 'arr';

for(let key of arr.keys()){ //arr.keys()返回的是数组的遍历器对象
    console.log(key);       //0 1
}

console.log(arr.keys().next()); //Object { value=0,  done=false}

for(let value of arr){
    console.log(value);     //1,2
}   

//该方法类似于以上遍历
for(let value of arr.values()){ //arr.values is not a function
    console.log(value);
}


for(let pair of arr.entries()){
    console.log(pair);  //[0, 1] [1, 2]
}
```

##### 类数组对象

`DOM NodeList`对象,`arguments`对象都是类数组对象.

```javascript
let ps = document.querySelectorAll('p');


for(let p of ps){   //给所有的p元素添加class属性
    p.classList.add('test');    
}

for(let key in ps){
    console.log(key);   //0,1,2,3,4,item,length
}
```

当然并不是所有的类数组对象都具有`iterator`接口

``` JavaScript
let obj = {
    0: 'a',
    1: 'b',
    length:2
};


for(let key in obj){
    console.log(key);   //0,1,length
}

for(let value of obj){
    console.log(value); //obj is not iterable
}
```

此时有两种方法可以使它拥有`iterator`接口

``` JavaScript
let obj = {
    0: 'a',
    1: 'b',
    length:2,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //部署Symbol.iterator属性
};


for(let value of obj){
    console.log(value); //a b
}


let arr1 = {
    0: 'a',
    1: 'b',
    length: 2
};

for(let value of Array.from(arr1)){ //将类数组对象转化为数组
    console.log(value); //a b
}
```
##### 比较

`for...in`循环的缺点
- 遍历数组的键名时以字符串'0','1'.'2'作为键名
- 不仅遍历数字键名,还会遍历其他键,甚至包括原型链上的键(此时使用Object.keys()比较合适).
- 以任意顺序遍历键名

`for...of`优点

- 可以与`break`,`continue`,`return`配合使用
- 提供了遍历所有数据结构的统一操作接口

``` JavaScript
let obj = {
    0: 'a',
    1: 'b',
    length:2
};

for(let value in obj){
    console.log(value); //0
    break;              //中断遍历,也可以! 
}

for(let value of Array.from(obj)){
    console.log(value); //a

    if(value === 'a'){
        break;          //中断遍历
    }
}
```

### Generator

`Generator`函数是`ES6`提供的一种异步编程解决方案，语法行为与传统函数完全不同。`Generator`函数是一个状态机，封装了多个内部状态。执行`Generator`函数会返回一个遍历器对象，也就是说，`Generator`函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历`Generator`函数内部的每一个状态。
形式上，`Generator`函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`语句，定义不同的内部状态（`yield`语句在英语里的意思就是“产出”）。


``` JavaScript
function* personGen(){
    yield 'ziyi2';      //此时有ziyi2,xx3和ending三个状态
    yield 'xx3';
    return 'ending';
}

let p = personGen();    //Generator函数返回的是一个遍历器对象

console.log(p.next());  //Object { value="ziyi2",  done=false}
console.log(p.next());  //Object { value="xx3",  done=false}
console.log(p.next());  //Object { value="ending",  done=true}
console.log(p.next());  //Object { value=undefined,  done=true}
```
调用`Generator`函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一节介绍的遍历器对象`Iterator Object`.必须调用遍历器对象的`next`方法，使得指针移向下一个状态,每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`语句（或`return`语句）为止。换言之，`Generator`函数是分段执行的，`yield`语句是暂停执行的标记，而`next`方法可以恢复执行。

第一次调用，`Generator`函数开始执行，直到遇到第一个`yield`语句为止。`next`方法返回一个对象，它的`value`属性就是当前`yield`语句的值`ziyi2`，`done`属性的值`false`，表示遍历还没有结束。

第二次调用，`Generator`函数从上次`yield`语句停下的地方，一直执行到下一个`yield`语句。`next`方法返回的对象的`value`属性就是当前`yield`语句的值`xx3`，`done`属性的值`false`，表示遍历还没有结束。

一直执行到`return`语句（如果没有`return`语句，就执行到函数结束）。`next`方法返回的对象的`value`属性，就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句，则`value`属性的值为`undefined`），`done`属性的值`true`，表示遍历已经结束。

`ES6`没有规定，`function`关键字与函数名之间的星号，写在哪个位置。下面写法都是可以的,一般采用第二种

``` JavaScript
function * f(){};
function* f(){};
function *f(){};
function*f(){};
```

#### yield
由于`Generator`函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`语句就是暂停标志。

- 遇到`yield`语句，就暂停执行后面的操作(比如有一个加法操作,没有遇到该`yield`之前是不会执行该语句的,等于手动的'惰性求值')，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
- 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`语句。
- 如果没有再遇到新的`yield`语句，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
- 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

`yield`语句与`return`语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。一个函数里面，只能执行一次（或者说一个）`return`语句，但是可以执行多次（或者说多个）`yield`语句。正常函数只能返回一个值，因为只能执行一次`return`；`Generator`函数可以返回一系列的值，因为可以有任意多个`yield`。从另一个角度看，也可以说`Generator`生成了一系列的值，这也就是它的名称的来历（在英语中，`generator`这个词是“生成器”的意思）。

`Generator`函数可以不用`yield`语句，这时就变成了一个单纯的暂缓执行函数

``` JavaScript
function * fGen(){
    console.log('此时执行');
}

let f = fGen();     //此时并没有输出
f.next();           //此时执行
```

`yield`语句不能用在普通函数里,否则会报错


```JavaScript
function * fGen(){
    (function(){        //不能把yield放在普通函数里
        yield 'ziyi2';
        yield 'xx3';
        return 'ending'
    })();
}


let f = fGen(); //error 报错
```

#### Iterator接口关系
对象的`Symbol.iterator`方法相当于一个遍历器生成函数,调用该函数会返回该对象的遍历器对象. 由于`Generator`函数就是遍历器生成函数,因此把`Generator`赋值给对象的`Symbol.iterator`属性,从而使得该对象具有`Iterator`接口.

``` javascript
let myIterable = {};

myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
};

console.log([...myIterable]);   //[1, 2, 3]
```

当然`Generator`函数执行后返回的遍历器对象本身也具有`Symbol.iterator`属性,执行后和自身相同

``` javascript
function* gen(){
    yield 1;
    yield 2;
};

let g = gen();

let s = g[Symbol.iterator]();

let bool = s ===  g;
console.log(bool);  //true

console.log(g.next());  //Object { value=1,  done=false}
console.log(s.next());  //Object { value=2,  done=false}
```

#### next方法的参数
#### for...of循环
一旦`next`方法的返回对象的`done`属性为`true`,`for...of`循环就会中止,且不包含该返回的对象,所以`return`语句返回的值就不会被遍历

``` javascript
function *f(){
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    return 6;
}

for(let value  of f()){
    console.log(value); //1 2 3 4
}
```

>提示: 使用`for...of`语句时不需要再使用`next`方法.

`for...of`,扩展运算符`...`,解构赋值和`Array.from`方法内部调用的,都是遍历器接口,所以可以将Generator函数返回的Iterator对象作为参数.

``` javascript
function *f(){
    yield 1;
    yield 2;
    yield 3;
    return 4;
    yield 5;

}


console.log([...f()]);          //[1, 2, 3]
console.log(Array.from(f()));   //[1, 2, 3]

let [...arr] = f();
console.log(arr);               //[1, 2, 3]

let [x,y,z] = f();
console.log(x);                 //1
console.log(y);                 //2
console.log(z);                 //3


for(let value of f()){
    console.log(value);         //1 2 3
}   

```

通过Generator函数为原生的对象加上`Iterator`接口

``` JavaScript
function* objEntries(obj){
    let keys = Reflect.ownKeys(obj);    //返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

    for(let key of keys){
        yield [key,obj[key]];
    }
}


let person = {one:'ziyi2',two:'ziyi3',three:'ziyi4'};

for(let [key,value] of objEntries(person)){
    console.log(`${key}:${value}`);
}

//one:ziyi2
//two:ziyi3
//three:ziyi4
```

以上原生的`person`对象是不具备`Iterator`接口的,通过`Generator`函数为它加上遍历器接口,就可以使用`for...of`遍历了.当然加上遍历器的另外一个方法是将`Generator`函数加到对象`Symbol.iterator`属性上面.

``` javascript
function* objEntries(){
    //let keys = Reflect.ownKeys(this); //返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

    let keys = Object.keys(this);       //返回对象自身的所有可枚举的属性的键名

    for(let key of keys){
        yield [key,this[key]];
    }
}


let person = {one:'ziyi2',two:'ziyi3',three:'ziyi4'};

person[Symbol.iterator] = objEntries;

for(let [key,value] of person){
    console.log(`${key}:${value}`);
}

//one:ziyi2
//two:ziyi3
//three:ziyi4
```
#####  Generator.prototype.throw
 `Generator`函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在`Generator`函数体内捕获.

``` javascript
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
console.log(i.next());  //Object {value: undefined, done: false}

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```

上面代码中，遍历器对象`i`连续抛出两个错误。第一个错误被`Generator`函数体内的`catch`语句捕获。`i`第二次抛出错误，由于`Generator`函数内部的`catch`语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就被抛出了`Generator`函数体，被函数体外的`catch`语句捕获。

`throw`方法可以接受一个参数，该参数会被`catch`语句接收，建议抛出`Error`对象的实例

``` javascript
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log( e);    //Error: 出错了!
  }
};


let i = g();
i.next();
i.throw(new Error('出错了!'));
```

>提示: 不要混淆遍历器对象的`throw`方法和全局的`throw`命令。上面代码的错误，是用遍历器对象的`throw`方法抛出的，而不是用`throw`命令抛出的。后者只能被函数体外的`catch`语句捕获。


如果`Generator`函数内部没有部署`try...catch`代码,那么遍历器对象抛出的异常将会被外部的`catch`代码块捕获

``` javascript
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获',e);  
  }
};

let i = g();
i.next();

try {
    throw new Error('a');   
    throw new Error('b');
} catch(e){
    console.log('外部捕获',e);
}

//外部捕获 Error: a
```
函数体外的`catch`语句块捕获了抛出`a`错误以后,就不会在继续`try`代码块里面剩余的语句了.

``` javascript
let g = function* () {
    while(true) {
        yield;
        console.log('内部捕获',e);
    }
};


let i = g();
console.log(i.next());  //Object { done=false,  value=undefined}

try {
    i.throw('a');
    i.throw('b');
} catch (e) {
    console.log('外部捕获',e);
}

//外部捕获 a

```

>提示: 如果外部和函数内部都没有部署`try...catch`代码块,那么程序将会报错,中断执行.

``` javascript
let gen = function* () {
    try {
        yield console.log('a');
    } catch (e) {
        console.log('error');
    }

    yield console.log('b');
    yield console.log('c');
};


let g = gen();

g.next();       //a
g.throw();      //error 
g.next();       //b
g.next();       //c
console.log(g.next());  //Object { done=true,  value=undefined}
g.throw();      //uncaught exception: undefined
```

```javascript
let gen = function* () {
    try {
        yield console.log('a');
    } catch (e) {
        console.log('error');
    }

    yield console.log('b');
    yield console.log('c');
};


let g = gen();

g.next();       //a
g.next();       //b

try {
    throw new Error();
} catch(e) {
    g.next();   //c
}

```

`Generator`函数体外抛出的错误,可以在函数体内捕获,反过来,`Generator`函数体内抛出的错误,也可以被函数体外的`catch`捕获

``` javascript
let gen = function* () {
    let x = yield 3;
    let y = x.toUpperCase();
    yield y;
};

let it = gen();

console.log(it.next());   //Object { value=3,  done=false}

try {
    it.next(43);
} catch (err) {
    console.log(err);     //x.toUpperCase is not a function
}

```

`yield`句本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`语句的返回值.

``` javascript
let gen = function* () {
    let x = yield 3;        //本身没有返回值,返回的总是undefined
    let y = x.toUpperCase();
    yield y;
};

let it = gen();

console.log(it.next());     //Object { value=3,  done=false}
console.log(it.next());     //TypeError: x is undefined 
```

一旦`Generator`执行过程中抛出错误,且没有被内部捕获,就不会再执行下去了,如果还调用`next`,将返回`{value:undeinfed,done:true}`

``` javascript
let gen = function* () {
    yield 3;
    console.log('throw an error');
    throw new error('error:next is done');
    yield 2;
    yield 3;
}

let g = gen();

try {
    console.log(g.next());   //Object { value=3,  done=false}
} catch(e) {
    console.log('first next error' );
}

try {
    console.log(g.next());
} catch(e) {
    console.log('second next error' );  //throw an error second next error
}

try {
    console.log(g.next());  // Object { done=true,  value=undefined}
} catch(e) {
    console.log('third next error' );
}
```

#####  Generator.prototype.return 

`return`方法用来终结遍历状态,就好像在`Generator`函数中遇到了`return`

``` javascript
let gen = function* () {
    yield 1;
    yield 2;
    yield 3;
}

let g = gen();

console.log(g.next());      // Object { value=1,  done=false}
console.log(g.return('4')); // Object { value="4",  done=true}
console.log(g.next());      // Object { done=true,  value=undefined}
```

>提示: 如果`return`方法不传入参数,那么返回的是`undefined`


如果`Generator`函数内部有`try...finally`代码块

``` javascript
let gen = function* () {
    yield 1;
    try {
        yield 2;
        yield 3;
    } finally {
        yield 4;
        yield 5;
    }
    yield 6;
}

let g = gen();

console.log(g.next());      //Object { value=1,  done=false}
console.log(g.next());      //Object { value=2,  done=false}
console.log(g.return('7')); //Object { value="7",  done=true}
console.log(g.next());      //Object { value=4,  done=false}
console.log(g.next());      //Object { value=5,  done=false}
```

>提示:调用`return`后,先执行`finally`中的代码块,执行完后再执行`return`方法

##### `yield*`

在`Generator`函数内部要调用`Generator`函数是不行的,此时可以用`yield*`来代替

``` javascript
function* f() {
    yield 1;
    yield 2;
    yield* g();
}

function* g() {
    yield 3;
    yield 4;
}


for(let value of f()){
    console.log(value); //1 2 3 4
}
```

如果`yield`后面是一个遍历器对象,那么需要`yield`命令后面加上`*`,表明它返回的是一个遍历器对象,否则返回的不是遍历值,而是该遍历器对象

``` javascript
function* f() {
    yield 1;
    yield 2;
    yield* g();         //返回遍历值            
    yield g();          //这里返回的是遍历器对象
}

function* g() {
    yield 3;
    yield 4;
}


for(let value of f()){
    console.log(value); //1 2 3 4  Generator {}
}
```

遍历递归效果

``` javascript
let f = (function* () {
    yield 'hello';
    yield 'bye';
}());


let g = (function* () {
    yield 'hello forward';
    yield* f;
    yield 'bye end';
}());

for(let value of g) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```

以上代码等同于

``` javascript
var f = (function* () {
    yield 'hello';
    yield 'bye';
}());

let g_copy = (function* () {
    yield 'hello forward';
    for(let value of f) {
        yield value;
    }
    yield 'bye end';

}());

for(let value of g_copy) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```

由于数组原生支持`Iterator`接口,所以可以被`yield*`遍历

``` javascript
let g = (function* () {
    yield 'hello forward';
    yield* ['hello','bye'];
    yield 'bye end';

}());

for(let value of g) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```

>提示: 任何具有`Iterator`接口的数据接口,都可以被`yield*`遍历

如果`Generator`函数中调用的`Generator`函数具有`return`语句,那么就可以在这个被调用的`Generator`函数中返回数据给调用`Generator`的`Generator`函数

``` javascript
function* f() {
    yield 2;
    yield 3;
    return 'return value';
}

function* g() {
    yield 1;
    let r = yield* f();     //r是f()中的返回值 注意与yield中的返回值的区别
    console.log('r:' + r); 
    yield 4;
}

let g1 = g();


console.log(g1.next()); // Object { value=1,  done=false}
console.log(g1.next()); // Object { value=2,  done=false}
console.log(g1.next()); // Object { value=3,  done=false}
console.log(g1.next()); // r:return value  Object { value=4,  done=false}
console.log(g1.next()); // Object { done=true,  value=undefined}
```
>提示: 注意与`yield`中的返回值的区别,`yield`句本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`语句的返回值。

``` javascript
function* iterTree(tree) {
    if(Array.isArray(tree)) {
        // tree.forEach(function(item,index,arr){
        //  yield* iterTree(item);  //不能放在普通函数内!!!!
        // })
        
        for(let value of tree){
            yield* iterTree(value);
        }
        

    } else {
        yield tree;
    }
}


const tree = [[1,2,3],4,5,[6,[7,8]]];

for(let value of iterTree(tree)) {
    console.log(value); //1 2 3 4 5 6 7 8
}
```

##### Generator作为对象的属性

```javascript
let obj = {

    f: function* () {
        yield 1;
        yield 2;
        yield 3;
    },

    *g() {              //简写方式 *表示这个属性是Generator函数
        yield 1;
        yield 2;
    }
}


for(let value of obj.f()){
    console.log(value); //1 2 3
}

for(let value of obj.g()){
    console.log(value); //1 2
}
```

`Generator`函数总是返回一个遍历器，`ES6`规定这个遍历器是`Generator`函数的实例，也继承了`Generator`函数的`prototype`对象上的方法

``` javascript
function* g() {}
g.prototype.say = () => 'hi';
let obj = g();  //返回的遍历器obj是g的实例
console.log(obj.say()); //hi
```

>提示: 不能把`Generator`函数当做普通的构造函数,并且不能和new命令结合


##### 状态机

```javascript
//ES5
let ticking = true;

let clock = () => {
    if(ticking) {
        console.log('Tick!');
    } else {
        console.log('Tock!');
    }

    ticking = !ticking;
}

clock();    //'Tick!'
clock();    //'Tock!'
clock();    //'Tick!'

//ES6
let status = function*() {
    while(true) {
        console.log('Tick!');
        yield;
        console.log('Tock!');
        yield;
    }
}


let x = status();

x.next();   //'Tick!'
x.next();   //'Tock!'
x.next();   //'Tick!'
```


##### 协程

协程（coroutine）是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”。协程既可以用单线程实现，也可以用多线程实现。前者是一种特殊的子例程，后者是一种特殊的线程。

（1）协程与子例程的差异

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

从实现上看，在内存中，子例程只使用一个栈（stack），而协程是同时存在多个栈，但只有一个栈是在运行状态，也就是说，协程是以多占用内存为代价，实现多任务的并行。

（2）协程与普通线程的差异

不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

由于ECMAScript是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。

Generator函数是ECMAScript 6对协程的实现，但属于不完全实现。Generator函数被称为“半协程”（semi-coroutine），意思是只有Generator函数的调用者，才能将程序的执行权还给Generator函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

如果将Generator函数当作协程，完全可以将多个需要互相协作的任务写成Generator函数，它们之间使用yield语句交换控制权。

##### 应用
- 异步操作的同步化表达

```javascript
function* ajax() {
    let result = yield request('/url'); //result就是next传入的参数response
    let resp = JSON.parse(result);
    console.log(resp.value);
}


function request(url) {
    makeAjax(url,(response => {
        it.next(response);
    }))
}

let it = ajax();
it.next();
```

使用`yield`语句可以手动逐行读取文件

```javascript
function* numbers() {
  let file = new FileReader("numbers.txt");
  try {
    while(!file.eof) {
      yield parseInt(file.readLine(), 10);  //转换为十进制
    }
  } finally {
    file.close();
  }
}
```

- 解决回调金字塔

回调金字塔
```javascript
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value4
      });
    });
  });
});
```

采用`Promise`写法

```javascript
Q.fcall(step1)
  .then(step2)
  .then(step3)
  .then(step4)
  .then(function (value4) {
    // Do something with value4
  }, function (error) {
    // Handle any error from step1 through step4
  })
  .done();
  ```

`Generator`写法

```javascript
function* longRunningTask() {
  try {
    var value1 = yield step1();
    var value2 = yield step2(value1);
    var value3 = yield step3(value2);
    var value4 = yield step4(value3);
    // Do something with value4
  } catch (e) {
    // Handle any error from step1 through step4
  }
}
```

然后，使用一个函数，按次序自动执行所有步骤

```javascript
scheduler(longRunningTask());

function scheduler(task) {
  setTimeout(function() {
    var taskObj = task.next(task.value);
    // 如果Generator函数未结束，就继续调用
    if (!taskObj.done) {
      task.value = taskObj.value
      scheduler(task);
    }
  }, 0);
}
```

注意，`yield`语句是同步运行，不是异步运行（否则就失去了取代回调函数的设计目的了）。实际操作中，一般让`yield`语句返回`Promise`对象。

```javascript
var Q = require('q');

function delay(milliseconds) {
  var deferred = Q.defer();
  setTimeout(deferred.resolve, milliseconds);
  return deferred.promise;
}

function* f(){
  yield delay(100);
};
```

- 作为数据结构

``` javascript
function *doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}

for (task of doStuff()) {
  // task是一个函数，可以像回调函数那样使用它
}

```

### Promise
#### Promise概述
- 对象的状态不受外界影响
`Promise`对象代表一个异步操作,有三种状态,`Pending(待决)`,`Resolved(成功)`和 `Rejected(失败)`.只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态,这也是Promise这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

- 一旦状态改变，就不会再变，任何时候都可以得到这个结果

`Promise`对象的状态改变，只有两种可能：从`Pending`变为`Resolved`和从`Pending`变为`Rejected`,只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果


`Promise`也有一些缺点。首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`Pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
如果某些事件不断地反复发生，一般来说，使用`stream`模式是比部署`Promise`更好的选择

#### 用法


```javascript
let promise = new Promise((resolve,reject) => {

    //异步操作
    
    // f(function(){
    //  if(err){
    //      reject(error);
    //  } 
    //  resolve(value);
    // })
    
    //操作成功
    resolve(value); //status:Pending -> Resolved

    //操作失败
    reject(error);  //status:Pending -> Rejected    

});

//Promise实例生成以后，可以用then方法分别指定Resolved状态和Reject状态的回调函数。
promise.then(value => {
    //success
},error => {
    //failed
})

```

>提示: 在`Promise`对象中的`resolve`和`reject`会将参数(这里是`value`和`error`)传递出去,而`then`中的成功和失败方法都会接受相应的参数


