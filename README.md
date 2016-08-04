# ECMAScript 6

## 使用环境
### Chrome
访问[Chrome配置](chrome://flags/#enable-javascript-harmony),开启`enable-javascript-harmony`特性,就可以使用`ES6`了.

### Nodejs
升级最新的Nodejs版本,在Nodejs中使用`ES6`.

### Webpack
可以访问[使用Babel 6和Webpack在浏览器端运行ES6和ES7](http://www.tuicool.com/articles/fmUze2M)查看使用方法.

## let 和 const

`ES5`只有两种声明变量的方式, `var`和`function`. 在`ES6`中有6种声明变量的方法,`var`,`function`,`let`,`const`,`import`和`class`.

### let

`var`具有块级作用域,而`let`没有

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

>提示:`ES6`明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称`TDZ`）。

``` javascript
if(true){
    typeof(undefinedVariable);  //undefined
    typeof(b);                  //b is not defined  

    //TDZ Start
    b = 2;  //b is not defined
    b = b + 1;

    let b;  //TDZ End
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

`let`新增了块级作用域

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
        var b = 2;  //没有快级作用域
    }
    console.log(b); //2
}

f1();
```

>提示: `ES5`只有全局作用域和函数作用域,而没有块级作用域,一般也都是利用立即执行的匿名函数来模拟块级作用域.

函数的块级作用域

`ES5`中规定函数只能在顶层作用域和函数作用域中声明,而不能在块级作用域中声明,在严格模式下会报错[非严格模式下不会报错],`ES6`中引入了类似于`let`的函数声明语句,该函数也是有作用域的,在块级作用域之外不能被引用.

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

>提示:  在`ES5`中,`if`语句块中的`f`函数的声明会被提升,所以输出的是覆盖了第一次声明的`f`函数.但是在`ES6`中,由于语句块中的函数声明只是在该语句块中起作用,并不会对语句块外产生影响,所以输出的是第一次声明的`f`函数.

ES6中的函数
- 允许在块级作用域内声明函数
- 函数声明类似于var,会提升到全局作用域或者函数作用域的头部
- 会提升到块级作用域的头部

``` javascript
if(true){       
    console.log(f);     //function f() 函数声明在块级作用域中提升
    function f(){       
        console.log('f is 2');
    }
}
```


### const


``` javascript
const STATUS = 1;
console.log(STATUS);    //1
STATUS = 2;             //Assignment to constant variable

const a;    //Missing initializer in const declaration
```
>提示: const在声明的同时必须赋值,否则会报错.且不能重新赋值.

`const`同样具有块级作用域

``` javascript
{
    const STATUS = 1;
    console.log(STATUS);    //1
}
console.log(STATUS);        //STATUS is not defined
```

和`let`一样也不可重复声明

```javascript
const STATUS = 1;
const STATUS = 2;   //Identifier 'STATUS' has already been declared

var STATUS = 1;
let STATUS = 2;     //Identifier 'STATUS' has already been declared
const STATUS = 3;
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