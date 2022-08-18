# 数组方法

## 1.数组基本方法（一）

<!--全都改变原数组-->	

### 1.1 push() （末尾增加）

 	push()  从数组后边加入元素 (从后插入元素)

​	 push()返回值  新数组的长度

```javascript
        var arr = [1, 2, 3, 4, 5];
        var result = arr.push(6);
        console.log(arr); //[1, 2, 3, 4, 5, 6]
        console.log(result); //6 
```

### 1.2 pop() （末尾删除）

​	pop() 从数组末尾删除一个元素 

​    pop()返回值：删除的元素

```javascript
        var arr = [1, 2, 3, 4, 5];
        var result = arr.pop();
        console.log(arr); //[1, 2, 3, 4]
        console.log(result); //5
```
###  1.3 unshift() （首位增加）

​	unshift() 从数组前边插入元素 (从前插入)

​    unshift() 返回值 ：新数组的长度

```javascript
        var arr = [1, 2, 3, 4, 5];
        var result = arr.unshift(6);
        console.log(arr); //[6, 1, 2, 3, 4, 5]
        console.log(result); //6
```
### 1.4 shift() （首位删除）

​	 shift()  删除首位元素

​    shift()  返回值： 删除的元素

```javascript
    	var arr = [1, 2, 3, 4, 5];
   		var result = arr.unshift(6);
    	console.log(arr); //[6, 1, 2, 3, 4, 5]
    	console.log(result); //6
```
## 2.数组方法(二)

### 2.1 concat() （拼接）

<!--不改变原数组-->

​    concat() 连接数组,返回值是拼接之后的结果

```javascript
        var arr1 = [1, 2];
        var arr2 = [3, 4];
        var nameArr = arr1.concat(arr2);
        console.log(nameArr); // [1, 2, 3, 4]
```
### 2.2 join() （数组->字符串）

<!--不改变原数组-->

​    join() 将数组转换为字符串，返回值是转换后的字符串

​	() 可以指定元素之间的连接符，不填默认逗号连接

```javascript
		var arr = [1, 2, 3];
        var str = arr.join('-');
        var str1 = arr.join();
        var str2 = arr.join('');
        console.log(str);//1-2-3
        console.log(str1);//1,2,3
        console.log(str2);//123
```
###  2.3 split() （字符串->数组）

<!--不改变原数组-->

​	split() 将字符串分割为数组，返回值是分割后的数组

```javascript
        var email = "abc@163.com;cc@126.com;frg@qq.com";
        // 指定分隔符
        var emailArr = email.split(';');
        console.log(emailArr);//['abc@163.com', 'cc@126.com', 'frg@qq.com']
```
### 2.4 splice() （增删改）

<!--改变原数组-->

​    splice 返回值是删除元素的数组(增加时数组为空)

```javascript
        //增加
		//splice(a,0,b)
		//在索引为a的位置开始删除0个元素，添加元素b
		var arr1 = [1, 2, 3];
        var result1 = arr.splice(2, 0, 4);
        console.log(arr1); //[1, 2, 4, 3]
        console.log(result1);//[]

		//删除
		//splice(a,b)
		//在索引为a的位置开始，删除b个元素
        var arr2 = [1, 2, 3];
        var result2 = arr2.splice(0,2);
        console.log(arr2); //[3]
        console.log(result2);// [1, 2]

		//替换
		//splice(a,b,c)
		//在索引为a的位置开始，删除b个元素,添加元素c
        //添加元素和删除元素的个数没有关系 ，添加元素可以任意添加
        var arr3 = ['1', '2', '3', '4'];
        var result3 = arr3.splice(0, 1 ,'5','6');
        console.log(arr3); // ['5', '6', '2', '3', '4']
        console.log(result3); //['1']
```
## 3.数组方法（三）

### 3.1 forEach() （遍历）

​    forEach(item,index,arr) 可以实现对数组的遍历
​    使用时传递参数进去 (回调函数)
​    // 第一个参数 当前遍历的元素
​    // 第二个参数 当前遍历元素的索引
​    // 第三个参数 正在遍历的数组

```javascript
		var arr = [1,2];
        arr.forEach(function(item,index,arr) {
            console.log(item);
            console.log(index);
            console.log(arr);
        })
        
        //输出结果
        // 1
        // 0
        // [1, 2]
        // 2
        // 1
        // [1, 2]
```

### 3.2 reverse()  （翻转）

<!--改变原数组-->

```javascript
  		var arr = [1, 2, 3, 4, 5];
        var temp = arr.reverse();
        console.log(arr); //[5, 4, 3, 2, 1]
        //返回值也是翻转后的数组
        console.log(temp); //[5, 4, 3, 2, 1]
```

### 3.3 slice()  (截取数组)

<!--不改变原数组-->

​	silce()返回值是截取到的数组

​    slice(a,b)
​	a为开始索引，b为结束索引，不包含结束索引位置上的元素
​	slice(a)
​	结束索引不指定，默认截取到最后

```javascript
		var arr = [1, 2, 3, 4, 5];
        var temp = arr.slice(1, 4);
        console.log(temp); //[2, 3, 4] 
        console.log(arr.slice(2)); //[3, 4, 5] 

        // 克隆数组
        var arr1 = arr.slice(0);
        console.log(arr1);  //[1, 2, 3, 4, 5]
        //截取末尾的元素
        console.log(arr.slice(-1)); //[5]
```
### 3.4 includes() （搜索是否含有某元素）

 	   arr.includes(item, from)
        从索引 from 开始在数组中搜索 item，如果找到则返回 true（如果没找到，返回 false）
        from不填，代表在整个数组中搜索

```javascript
		var arr = [1, 2, 3, 4, 5];
        console.log(arr.includes(1)); //true
        console.log(arr.includes(1,1)); //false
```

### 3.5 indexOf() (获取某个元素在数组中的索引) 

​	 indexOf 可以获取某个元素在数组当中的索引，如果没有返回 -1 

 	indexOf() 从前向后匹配，匹配到就会停止

​	  lastIndexOf() 从后向前匹配，匹配到就会停止

```javascript
        var arr = [1, 2, 3, 4, 5, 1];
        console.log(arr.indexOf(4)); //3
        console.log(arr.indexOf(9)); //-1
        console.log(arr.indexOf(1)); // 0
        console.log(arr.lastIndexOf(1)); // 5
```
### 3.6 sort() (排序)

<!--改变原数组-->

​	方式一 不带参数  根据数组元素的unicode编码值进行排序

```javascript
        var arr = [11, 122, 53, 34, 50, 15];
        arr.sort();
        console.log(arr); // [11, 122, 15, 34, 50, 53]
```

​    方式二  带参数 

​    参数是一个函数，用于指定排序规则

​    如果返回一个大于 0 的值，则元素会交换位置

​    如果返回一个小于 0 的值，则元素位置不变

​    如果返回一个 0，则认为两个元素相等，则不交换位置

```javascript
	 	var arr = [11, 122, 53, 34, 50, 15]; 		
		arr.sort(function (m, n) {
            return m - n; //从小到大排序
           // return n - m; //从大到小排序
        })

        console.log(arr); // [11, 15, 34, 50, 53, 122]
                          //[122, 53, 50, 34, 15, 11]
```
# 字符串方法

<!--以下方法都不改变原数组-->

<!--使用以下一个字符串进行示例-->

<!--  var str = "how are you? And you?" -->

```javascript
		//根据索引获取值
        console.log(str[2]); //w 
        console.log(str.length); //21

        //字符串无法通过索引进行修改；
        str[2] = 'A'; //无效
        console.log(str); //how are you? And you?
```



### 1. charAt() （获取指定位置的字符）

```javascript
		 console.log(str.charAt(2)); // w 等同与  console.log(str[2]);
```

### 2. charCodeAt() （获取指定位置元素的Unicode 编码）

```javascript
 		 console.log(str.charCodeAt(2)); //119 
```

### 3. indexOf() (返回字符在字符串中的位置)

​	  indexOf 可以获取某个元素在字符串当中的索引，如果没有返回 -1 

  	indexOf() 从前向后匹配，匹配到就会停止

​	  lastIndexOf() 从后向前匹配，匹配到就会停止

```javascript
 		console.log(str.indexOf('w')); //2 
        console.log(str.indexOf('y')); //8
        console.log(str.lastIndexOf('y')); //17
        console.log(str.indexOf('z')); // -1 

		// 可以间接通过是否返回 -1 判断字符串中是否含有某元素
```

### 4. concat() (连接字符串)

```javascript
        var temp = str.concat('hhhh');
        console.log(temp); //how are you? And you?hhhh
```

### 5. slice() (提取字符串的某个部分)

slice() 参数为开始索引和结束索引，提取部分不包含结束索引位置的元素

```javascript
        var res1 = str.slice(0, 1);
        console.log(res1); //h
```

### 6. substr() 截取字符串

substr() 参数为开始索引和截取个数

```javascript
	    var res2 = str.substr(0, 1);
        console.log(res2); //h
```

###  7. toUpperCase() 转成大写 

```javascript
	 console.log(str.toUpperCase()); //HOW ARE YOU? AND YOU?
```

### 8.toLowerCase() 转成小写

```javascript
 console.log(str.toLowerCase());  //how are you? and you?
```

# ES6

https://www.bookstack.cn/read/es6-3rd/sidebar.md

## 1. 数组方法

### 1. Array.from()

Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

下面是一个类似数组的对象，Array.from将它转为真正的数组

```
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

### 2. Array.of()

Array.of 方法用于将一组值，转换为数组。

```
 Array.of(3, 11, 8) // [3,11,8]
 Array.of(3) // [3]
 Array.of(3).length // 1
```

### 3. *includes()

includes()方法返回一个布尔值，表示某个数组是否包含给定的值

```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```

该方法的第二个参数表示搜索的起始位置，默认为`0`。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为`-4`，但数组长度为`3`），则会重置为从`0`开始。

```
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```

### 4.  *find() 和 findIndex()

**数组实例的find方法，用于找出第一个符合条件的数组成员。**它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。**如果没有符合条件的成员，则返回undefined**。

```js
[1, 4, -5, 10].find((n) => n < 0)
// -5
```

```js
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

上面代码中，find方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。

数组实例的findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，**如果所有成员都不符合条件，则返回-1**。

```js
[1, 5, 10, 15].findIndex(function(value, index, arr) { 
	return value > 9;
}) // 2
```

这两个方法都可以接受第二个参数，用来绑定回调函数的this对象。

```js
function f(v){  return v > this.age;}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26
```



###  5. *filter() (过滤)

​	filter()方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素

​	return后面判断结果，取布尔值，true的话就添入新的filter数组中，false的话，不会添进filter的数组中

​	filter()方法不会过滤空数组

```js
  let arr = [1,2,3,4,5,6,7,8];
  let filterArr = arr.filter(item => item >3 );
  console.log(filterArr)//输出[4,5,6,7,8]
```



### 6. flat() (拉平)

数组的成员有时还是数组，Array.prototype.flat()用于将嵌套的数组“拉平”，变成一维的数组。

该方法返回一个新数组，对原数据没有影响。

```
[1, 2, [3, 4]].flat()// [1, 2, 3, 4]
```

flat()默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将flat()方法的参数写成一个整数，表示想要拉平的层数，默认为1。flat()的参数为2，表示要“拉平”两层的嵌套数组。

```
[1, 2, [3, [4, 5]]].flat()// [1, 2, 3, [4, 5]]
[1, 2, [3, [4, 5]]].flat(2)//[1, 2, 3, 4, 5]
```

如果不管有多少层嵌套，都要转成一维数组，可以用Infinity关键字作为参数。

```
[1, [2, [3]]].flat(Infinity)// [1, 2, 3]
```

如果原数组有空位，flat()方法会跳过空位。

```
[1, 2, , 4, 5].flat()// [1, 2, 4, 5]
```

### 7. Array.at() (返回对应下标的值)

es6中为数组实例增加了at()方法，接受一个整数作为参数,支持负索引。
这个方法不仅可用于数组， 也可用于字符串和类型数组（ TypedArray）。
如果参数位置超出了数组范围，at()返回undefined。

```
const arr = [100, 120, 18, 130, 4];
console.log(arr.at(1)) //120
console.log(arr.at(-1)) //4
console.log(arr.at(-6)) //undefined
```

### 8.entries()，keys() 和 values()

ES6 提供三个新的方法——entries()，keys()和values()——用于遍历数组。

可以使用for...of循环进行遍历

keys()是对键名的遍历  (数组的键名是固定的（默认总是0、1、2...）)

values()是对键值的遍历

entries()是对键值对的遍历。

```js
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1
for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'
for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

## 2.字符串方法

### 1. includes(), startsWith(), endsWith()

includes()：返回布尔值，表示是否找到了参数字符串。
startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```
let s = 'Hello world!';
s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

这三个方法都支持第二个参数，表示开始搜索的位置。

```
let s = 'Hello world!';
s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

上面代码表示，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

### 2. padStart()，padEnd()

ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。

padStart()用于头部补全，padEnd()用于尾部补全。

```
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'
'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

上面代码中，padStart()和padEnd()一共接受两个参数

第一个参数是字符串补全生效的最大长度

第二个参数是用来补全的字符串。



如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。

```
'xxx'.padStart(2, 'ab') // 'xxx''xxx'.padEnd(2, 'ab') // 'xxx'
```

如果用来补全的字符串与原字符串，两者的长度之和超过了最大长度，则会截去超出位数的补全字符串。

```
'abc'.padStart(10, '0123456789')// '0123456abc'
```

如果省略第二个参数，默认使用空格补全长度。

```
'x'.padStart(4) // '   x''x'.padEnd(4) // 'x   '
```

padStart()的常见用途是为数值补全指定位数。下面代码生成 10 位的数值字符串。

```
'1'.padStart(10, '0')  // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```

另一个用途是提示字符串格式。

```
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

### 3. trimStart()，trimEnd()

ES2019 对字符串实例新增了trimStart()和trimEnd()这两个方法。它们的行为与trim()一致，trimStart()消除字符串头部的空格，trimEnd()消除尾部的空格。它们返回的都是新字符串，不会修改原始字符串。

```
const s = '  abc  ';
s.trim() // "abc"
s.trimStart() // "abc  "
s.trimEnd() // "  abc"
```

上面代码中，trimStart()只消除头部的空格，保留尾部的空格。trimEnd()也是类似行为。

除了空格键，这两个方法对字符串头部（或尾部）的 tab 键、换行符等不可见的空白符号也有效。

## 3. 对象方法

### 1.Object.assign()

Object.assign方法用于对象的合并

Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。

```
const target = { a: 1 };
const source1 = { b: 2 };
const source2 = { c: 3 };
Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

```
const target = { a: 1, b: 1 };
const source1 = { b: 2, c: 2 };
const source2 = { c: 3 };
Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

由于undefined和null无法转成对象，所以如果它们作为参数，就会报错。

```
Object.assign(undefined) // 报错Object.assign(null) // 报错
```

如果undefined和null不在首参数，就不会报错。

```
let obj = {a: 1};
Object.assign(obj, undefined) === obj // true
Object.assign(obj, null) === obj // true
```

**注意点**

浅拷贝

```
const obj1 = {a: {b: 1}};
const obj2 = Object.assign({}, obj1);
obj1.a.b = 2;
obj2.a.b // 2
```

### 2. Object.keys()，Object.values()，Object.entries()

ES5 引入了Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```
var obj = { foo: 'bar', baz: 42 };
Object.keys(obj)
// ["foo", "baz"]
```

Object.values方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

```
const obj = { foo: 'bar', baz: 42 };
Object.values(obj)
// ["bar", 42]
```

Object.entries()方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

```
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj)
// [ ["foo", "bar"], ["baz", 42] ]
```

ES2017 引入的跟Object.keys配套的Object.values和Object.entries，作为遍历一个对象的补充手段

供for...of循环使用。

```js
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };
for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}
for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}
for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```



# 遍历方法

## 1.for in (对象)

for in遍历对象是通过属性来进行遍历来的

```js
for(let i in obj) {
    // i 是key 
	console.log(i,obj[i])
}
/*
name karwin
age 3 
*/ 
```

字符串和数组、对象都可以遍历

**扩展：**

for in遍历数组、字符串是通过遍历下标来进行遍历的，

```javascript
let arr = [10, 20, 30];
let str = 'abc'
let obj = {
	name:'karwin',
	age: '3'
}

for(let i in arr){
    //i 是下标
    console.log(arr[i]); 
}
/*
0 10 
0 20 
0 30 
*/ 

for(let i in str){
    //i 是下标
    console.log(str[i]);
}
/*
0 a
0 b 
0 c 
*/ 
```

## 2. for of(数组)

ES6新增 

for of 方法遍历实质上是直接遍历的值

```JS
let arr = [10, 20, 30];
for (let item of arr) {
	console.log(item) // 10 20 30
}
```

遍历数组的其他属性，需要借助object的key，value，entries

```js
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1
for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'
for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

**扩展：**

字符串和对象可以遍历

```js
let str = 'abc'
let obj = {
	name:'afei',
	age: '24'
}

for (let item of str) {
	console.log(item) // a b c
}
for (let item of obj) {
	console.log(item) // afei 24
}

```

遍历对象（这里说对象不严谨，准确的来说是遍历集合）的其他属性，需要借助object的key，value，entries

```js
let a = {
    "b":'1',
    "c":'2',
    "d":'3'
}
for (let s of Object.keys(a)) {
    // b c d
    console.log(s)
}
for (let s of Object.values(a)) {
    // 1 2 3
    console.log(s)
}
for (let s of Object.entries(a)) {
    // ["b":'1'],["c":'2'],["d":'3']
    console.log(s)
}
```



## 3. forEach(数组)

   forEach(item,index,arr) 可以实现对数组的遍历
​    使用时传递参数进去 (回调函数)
​    // 第一个参数 当前遍历的元素
​    // 第二个参数 当前遍历元素的索引
​    // 第三个参数 正在遍历的数组

```javascript
		var arr = [1,2];
        arr.forEach(function(item,index,arr) {
            console.log(item);
            console.log(index);
            console.log(arr);
        })
        
        //输出结果
        // 1
        // 0
        // [1, 2]
        // 2
        // 1
        // [1, 2]
```

## 4. for 

字符串和数组都可以遍历

```js
let arr = [10, 20, 30];
let str = 'abc'
for(let i = 0; i < str.length; i++){
    console.log(str[i])   // 10 20 30
}
for(let i = 0; i < arr.length; i++){
    console.log(arr[i])   // a b c
}
```



## 5.总结

1. 在循环对象属性的时候使用 for...in，在遍历数组的时候的时候使用 for...of
2. for...in 循环出的是 key，for...of 循环出的是 value 
3. for in会遍历数组所有的可枚举属性，包括原型。所以for in更适合遍历对象，不要使用for in遍历数组。
4. for...of 不能循环普通的对象（如通过构造函数创造的），需要通过和 Object.keys()搭配使用