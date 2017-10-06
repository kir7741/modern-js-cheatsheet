# Modern JavaScript Cheatsheet
## 介紹
### 動機

本份文件整理了現代Javascript的開發過程中會使用到的備忘錄。

這份指南並不是試圖幫助初學者從頭開始學起Javascript，而是為了幫助那些因為Javascript的新概念，而奮力去熟悉函式庫(例如React)的開發者。

此外，我偶爾會提供一些我個人的小技巧，這可能會帶有點爭議性，但是這僅是我個人在使用後所提供的建議。

> **注意**:這份文件大多數的概念介紹都來自Javascript的語言更新(ES2016，或稱做ES6)，你可以在[此處](http://es6-features.org)瞭解更多。

### 補充資源

### 目錄

## 概念

### 宣告變數 : var, const, let

在Javascript中，有三種方式來宣告變數，彼此間有各自的不同之處，分別是 ```var```, ```let``` 和 ```const```。

#### 簡短解釋

使用 ```const``` 宣告的變數無法被更改, 但是 ```let``` 和 ```var``` 可以.

我建議一律使用 ```const``` 來宣告變數，除非是你之後需要更改或指派變數時才使用 ```let```。


<table>
  <tr>
    <th></th>
    <th>作用域</th>
    <th>能否指派</th>
    <th>能否更改</th>
   <th><a href="#tdz_sample">暫時死區</a></th>
  </tr>
  <tr>
  <tr>
    <th>const</th>
    <td>區塊{}內</td>
    <td>否</td>
    <td><a href="#const_mutable_sample">是</a></td>
    <td>是</td>
  </tr>
  <tr>
    <th>let</th>
    <td>區塊{}內</td>
    <td>是</td>
    <td>是</td>
    <td>是</td>
  </tr>
  <tr>
    <th>var</th>
    <td>函式內</td>
    <td>是</td>
    <td>是</td>
    <td>否</td>
  </tr>
</table>

> **補充**:暫時死區係指在還未宣告變數之前，就先建立作用域， ```var```會因為Hoist的效果而不受暫時死區影響。

#### 範例程式碼

```javascript
const person = "Nick";
person = "John" // 會出現錯誤，person無法被重新指派
```

```javascript
let person = "Nick";
person = "John";
console.log(person) // "John", let宣告的變數可以重新指派
```

#### 詳細解釋

 變數的[*作用域*](#scope_def) 係指 "變數呈現有效的程式碼範圍".

##### var

使用 ```var```宣告的變數是以 *函式的區塊*為作用域，這樣意謂著當變數在函式中被創造時，該函式的所有東西都能夠使用這個變數。 此外，以 *函示作用域*宣告的變數，無法在函式外面被使用。

我建議你將這想像成在 *X 作用域*中創造的變數即是 *X*的屬性

```javascript
function myFunction() {
  var myVar = "Nick";
  console.log(myVar); // "Nick" - myVar 可以在函式中被使用
}
console.log(myVar); // 出現錯誤, myVar 不可在函式外被使用
```

持續專注在變數的作用域，這裡有更細微的範例：

```javascript
function myFunction() {
  var myVar = "Nick";
  if (true) {
    var myVar = "John";
    console.log(myVar); // "John"
    // 實際上, myVar 是在函式的作用域中, 我們僅是覆蓋了它的值， "Nick" 變成 "John"
  }
  console.log(myVar); // "John" - 觀察 if 的區塊如何影響這個變數
}
console.log(myVar); // 出現錯誤, myVar 不可在函式外被使用
```

此外, *var* 宣告的變數會被移動到執行環境的最上方，這就是我們所謂的 [var hoisting](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting).

這裡有份範例:

```js
console.log(myVar) // undefined -- 不會出錯誤
var myVar = 2;
```

在執行環境會被解讀成:

```js
var myVar;
console.log(myVar) // undefined -- 不會出現錯誤
myVar = 2;
```

##### let

```var``` 和 ```let ``` 大致上是一樣的, 但是 ```let``` 宣告的變數

- 是以 *區塊範圍*為作用域
- 被指派值之前是 **不能** 被使用的
- 在同一個作用域之中不能被再次宣告

讓我們藉由之前的範例來觀察區塊作用域的影響：

```javascript
function myFunction() {
  let myVar = "Nick";
  if (true) {
    let myVar = "John";
    console.log(myVar); // "John"
    // 事實上, myVar 在區塊作用域裡面, 我們是在此作用域宣告了一個新的 myVar 變數
    // 這個變數無法在這個區塊範圍外使用 and totally independent
    // 而且完全獨立於我們所創造的第一個 myVar 變數 !
  }
  console.log(myVar); // "Nick", 仔細觀察 if 的區塊是無法影響這第一個變數
}
console.log(myVar); // 出現錯誤, myVar 變數無法在函式外被使用
```

<a name="tdz_sample"></a>現在，讓我們來了解何謂 *let* (和 *const*)宣告的變數無法在被指派前使用：

```js
console.log(myVar) // 出現錯誤 !
let myVar = 2;
```

和 *var* 所宣告的變數相比, 如果在將 *let* 或 *const* 宣告的變數賦予值之前，就嘗試讀取或使用這個變數會發生錯誤。 這個現象通常被稱為[*暫時性死區*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_Dead_Zone_and_errors_with_let) 或簡稱 *TDZ*.

> **注意:** 技術上而言， *let* 和 *const* 所宣告的變數一樣會被 hoist，但它們的值並沒有。因為這些變數在賦予值之前都是無法使用的，所以它們感覺起來並沒有被 hoist，但是這裡卻是有的。如果想知道更多 [這裡有更詳細的解釋](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified)。

此外，你不能再次宣告 *let* 的變數：

```js
let myVar = 2;
let myVar = 3; // 出現錯誤
```

##### const

```const``` 所宣告的變數，其行為就像是 *let* 宣告的變數一樣, 但是前者無法被更改。

總而言之, *const* 宣告的變數:

- 以 *區塊範圍* 為作用域
- 在被指派值之前，不能被讀取或使用
- 在同一作用域裡頭，無法再次宣告
- 值無法再被指派

```js
const myVar = "Nick";
myVar = "John" // 出現錯誤，值無法再次被指派
```

```js
const myVar = "Nick";
const myVar = "John" // 出現錯誤，變數無法被宣告
```

<a name="const_mutable_sample"></a> 但是此處有個細節： ```const```宣告的變數並不是 [**無法更動的**](#mutation_def) ! 具體上， ```const```所宣告出的 *object* 和 *array* 是 **可以** 更改的。

For objects:
```js
const person = {
  name: 'Nick'
};
person.name = 'John' // 這可以正常運作 ! person variable 並非完全被重新指派, 但卻被更動了
console.log(person.name) // "John"
person = "Sandra" // 出現錯誤，因為 const 宣告的變數無法被重新指派
```

For arrays:
```js
const person = [];
person.push('John'); //這可以正常運作 ! person variable 並非完全被重新指派, 但卻被更動了
console.log(person[0]) // "John"
person = ["Nick"] // 出現錯誤，因為 const 宣告的變數無法被重新指派
```

#### 外部補充資源

- [How let and const are scoped in JavaScript - WesBos](http://wesbos.com/javascript-scoping/)
- [Temporal Dead Zone (TDZ) Demystified](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified)


### <a name="arrow_func_concept"></a> 箭頭函式

ES6 JavaScript 開始使用了 *箭頭函式*, 其係指另外一種宣告或使用函式的方法。以下是箭頭函式帶來的好處：

- 更加簡潔
- *this* 被環境所綁定(黑人問號.JPG)
- 隱性回傳(黑人問號.JPG)

#### 程式碼範例

- 簡潔性 and 隱性回傳

```js
function double(x) { return x * 2; } // 傳統 ES 5寫法
console.log(double(2)) // 4
```

```js
const double = x => x * 2; // 同樣的函式以箭頭函式方法撰寫且隱性回傳
console.log(double(2)) // 4
```

- *this* 參考

在箭頭函式裡面, *this* 等同於封閉的執行環境的 *this*。基本上，透過箭頭函式, 在呼叫函式內的函式之前，你不需要再使用 "that = this"這個小技巧。

```js
function myFunc() {
  this.myVar = 0;
  setTimeout(() => {
    this.myVar++;
    console.log(this.myVar) // 1
  }, 0);
}
```

#### 詳細解釋

##### 簡潔姓

箭頭函式在許多面向都比傳統的函式撰寫方法來得簡潔。來看看所有可能的例子：

- 隱性 VS 顯性回傳

 **顯性回傳** 是指函式內部有使用 *return* 這一關鍵字。

```js
  function double(x) {
    return x * 2; // 這個函式顯性回傳了 x * 2, 使用了 *return* 這一關鍵字
  }
```

傳統的函式撰寫方法，return總是顯性的。但在箭頭函式裡，你可以使用 *隱性回傳* 這意謂著你不需要使用關鍵字 *return* 來回傳值。

為使用隱性回傳, 程式碼必須以一行撰寫。

```js
  const double = (x) => {
    return x * 2; // 此處為顯性回傳
  }
```

因為這裡只有回傳一個值，所以我們可以使用隱性回傳。

```js
  const double = (x) => x * 2;
```

為了達成隱性回傳，我們只需要 **移除括號** 還有移除關鍵字 **return** 。這就是為什麼它被稱做*隱性* 回傳, 這裡並沒有關鍵字 *return*  ，但函式確實回傳了 ```x * 2``` 。

> **注意:** 如果你的函式沒有回傳任何的值 (因為 *副作用*), 那麼這個函式不屬於顯性也不屬於隱性回傳。

此外, 如果你想隱性回傳一個 *object* 你 **必須使用小括號包住** ，因為隱性回傳會跟大括號產生衝突：

```js
const getPerson = () => ({ name: "Nick", age: 24 })
console.log(getPerson()) // { name: "Nick", age: 24 } -- object 被箭頭函式隱性回傳
```

- 當只有一個變數時

如果你的函式只有一個變數， 你可以省略變數旁的小括號：

```js
  const double = (x) => x * 2; // 這個箭頭函式只有一個變數
```

變數周圍的小括號可以被省略:

```js
  const double = x => x * 2; // 這個箭頭函式只有一個變數(省略小括號)
```

- 沒有參數的情況：

當沒有參數提供給箭頭函式時，你必須使用小括號, 否則語法會出現錯誤。

```js
  () => { // 有提供小括號，可以正常運作
    const x = 2;
    return x;
  }
```

```js
  => { // 沒有小括號，不能運作
    const x = 2;
    return x;
  }
```

##### *this* 參考資料

想要更細微的瞭解箭頭函式, 你必須了解 [this](#this_def) 在 JavaScript 是如何運作的。

在箭頭函式中, *this* 等同於封閉的執行環境的 *this*。意思是說，箭頭函式並沒有創造一個新的 *this*，而是從外圍環境去抓 *this*。

在不使用箭頭函式的情況下，如果你想在函式中的函式來取得外部函式的 *this* 值，你需要使用 *that = this* 或是 *self = this* 這樣的小技巧。

舉例來說，以 myFunc 函式中的 setTimeout 函式來說明：

```js
function myFunc() {
  this.myVar = 0;
  var that = this; //that = this 小技巧
  setTimeout(
    function() { //在這個函式的作用域中會產生出新的 *this* 
      that.myVar++;
      console.log(that.myVar) // 1

      console.log(this.myVar) // undefined -- 詳見上述的函式宣告(這個函式沒有賦予值給myVar這個變數)
    },
    0
  );
}
```

但在箭頭函是中, *this* 會從外部環境來取得

```js
function myFunc() {
  this.myVar = 0;
  setTimeout(
    () => { // this 從外部環境被取得, 此處代表 myFunc 這個函式
      this.myVar++;
      console.log(this.myVar) // 1
    },
    0
  );
}
```

#### 有用的資源

- [Arrow functions introduction - WesBos](http://wesbos.com/arrow-functions/)
- [JavaScript arrow function - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [Arrow function and lexical *this*](https://hackernoon.com/javascript-es6-arrow-functions-and-lexical-this-f2a3e2a5e8c4)

### 函式的預設值

從 ES2015 JavaScript 的更新開始，你可以依照下面的語法來替你的參數加上預設值： 

```js
function myFunc(x = 10) {
  return x;
}
console.log(myFunc()) // 10 -- 沒有指定參數，所以函式會使用 x 的預設值10
console.log(myFunc(5)) // 5 -- 有指定參數，所以使用帶入的參數

console.log(myFunc(undefined)) // 10 -- 參數為 undefined value ，一樣使用 x 的預設值10
console.log(myFunc(null)) // null -- 參數為 (null)，請往下看詳細解說
```

參數預設值只能被應用在以下兩種情況：

- 沒有傳入參數給函式
- 傳入的參數為 *undefined* 

換句話說，如果你將 *null* 設為預設值 **會無法被應用**.

> **注意:** 預設值可以與解構的參數一起被使用 (看看下一段例子)

#### 外部資源

- [Default parameter value - ES6 Features](http://es6-features.org/#DefaultParameterValues)
- [Default parameters - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)

### 解構 objects and arrays

*解構賦值* 是一個方便的方法，藉由創造新的變數來提取 objects 或 arrays 裡面的值。

舉例而言， *解構賦值* 可以被用來解構函式的參數或是React專案中 *this.props* 的用法。

#### 程式碼範例

- Object

來思考一下以下這個 object：

```js
const person = {
  firstName: "Nick",
  lastName: "Anderson",
  age: 35,
  sex: "M"
}
```

沒有解構的情況：

```js
const first = person.firstName;
const age = person.age;
const city = person.city || "Paris";
```

在解構的情況下可以寫成一行：

```js
const { firstName: first, age, city = "Paris" } = person; // That's it !

console.log(age) // 35 -- 創造出 age 這個新變數且等同於 person.age
console.log(first) // "Nick" -- 創造出 first 這個新變數且等同於 person.firstName
console.log(firstName) // Undefined -- person.firstName 是存在的，但是等於新創造的變數 first
console.log(city) // "Paris" -- 造出 city 這個新變數，但是因為  person.city 沒有被定義，city 等同於預設值"Paris"。
```

**注意 :** 在 ```const { age } = person;```的情況下，*const* 後面的大括號並不是用來宣告物件或是區塊，這僅是 *解構賦值* 的語法。

- Function parameters

*Destructuring* is often used to destructure objects parameters in functions.

Without destructuring

```js
function joinFirstLastName(person) {
  const firstName = person.firstName;
  const lastName = person.lastName;
  return firstName + '-' + lastName;
}

joinFirstLastName(person); // "Nick-Anderson"
```

In destructuring the object parameter *person*, we get a more concise function:

```js
function joinFirstLastName({ firstName, lastName }) { // we create firstName and lastName variables by destructuring person parameter
  return firstName + '-' + lastName;
}

joinFirstLastName(person); // "Nick-Anderson"
```

Destructuring is even more pleasant to use with [arrow functions](#arrow_func_concept):

```js
const joinFirstLastName = ({ firstName, lastName }) => firstName + '-' + lastName;

joinFirstLastName(person); // "Nick-Anderson"
```

- Array

Lets consider the following array:

```js
const myArray = ["a", "b", "c"];
```

Without destructuring

```js
const x = myArray[0];
const y = myArray[1];
```

With destructuring

```js
const [x, y] = myArray; // That's it !

console.log(x) // "a"
console.log(y) // "b"
```

#### Useful resources

- [ES6 Features - Destructuring Assignment](http://es6-features.org/#ArrayMatching)
- [Destructuring Objects - WesBos](http://wesbos.com/destructuring-objects/)
- [ExploringJS - Destructuring](http://exploringjs.com/es6/ch_destructuring.html)

### Array methods - map / filter / reduce

*Map*, *filter* and *reduce* are array methods that are coming from a programming paradigm named [*functional programming*](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0).

To sum it up:

- **Array.prototype.map()** takes an array, does something on its elements and returns an array with the transformed elements.
- **Array.prototype.filter()** takes an array, decides element by element if it should keep it or not and returns an array with the kept elements only
- **Array.prototype.reduce()** takes an array and aggregates the elements into a single value (which is returned)

I recommend to use them as much as possible in following the principles of functional programming because they are composable, concise and elegant.

With those three methods, you can avoid the use of *for* and *forEach* loops in most situations. When you are tempted to do a *for* loop, try to do it with *map*, *filter* and *reduce* composed. You might struggle to do it at first because it requires you to learn a new way of thinking, but once you've got it things gets easier.

#### Sample code

```js
const numbers = [0, 1, 2, 3, 4, 5, 6];
const doubledNumbers = numbers.map(n => n * 2); // [0, 2, 4, 6, 8, 10, 12]
const evenNumbers = numbers.filter(n => n % 2 === 0); // [0, 2, 4, 6]
const sum = numbers.reduce((prev, next) => prev + next, 0); // 21
```

Compute total grade sum for students above 10 by composing map, filter and reduce:

```js
const students = [
  { name: "Nick", grade: 10 },
  { name: "John", grade: 15 },
  { name: "Julia", grade: 19 },
  { name: "Nathalie", grade: 9 },
];

const aboveTenSum = students
  .map(student => student.grade) // we map the students array to an array of their grades
  .filter(grade => grade >= 10) // we filter the grades array to keep those above 10
  .reduce((prev, next) => prev + next, 0); // we sum all the grades above 10 one by one

console.log(aboveTenSum) // 44 -- 10 (Nick) + 15 (John) + 19 (Julia), Nathalie below 10 is ignored
```

#### Explanation

Let's consider the following array of numbers for our examples:

```js
const numbers = [0, 1, 2, 3, 4, 5, 6];
```

##### Array.prototype.map()

```js
const doubledNumbers = numbers.map(function(n) {
  return n * 2;
});
console.log(doubledNumbers); // [0, 2, 4, 6, 8, 10, 12]
```

What's happening here? We are using .map on the *numbers* array, the map is iterating on each element of the array and passes it to our function. The goal of the function is to produce and return a new value from the one passed so that map can replace it.

Lets extract this function to make it more clear, just for this once:

```js
const doubleN = function(n) { return n * 2; };
const doubledNumbers = numbers.map(doubleN);
console.log(doubledNumbers); // [0, 2, 4, 6, 8, 10, 12]
```

```numbers.map(doubleN)``` produces ```[doubleN(0), doubleN(1), doubleN(2), doubleN(3), doubleN(4), doubleN(5), doubleN(6)]``` which is equal to ```[0, 2, 4, 6, 8, 10, 12]```.

> **Note:** If you do not need to return a new array and just want to do a loop that has side effects, you might just want to use a for / forEach loop instead of a map.

##### Array.prototype.filter()

```js
const evenNumbers = numbers.filter(function(n) {
  return n % 2 === 0; // true if "n" is par, false if "n" isn't
});
console.log(evenNumbers); // [0, 2, 4, 6]
```

We are using .filter on the *numbers* array, filter is iterating on each element of the array and passes it to our function. The goal of the function is to return a boolean that will determine whether the current value will be kept or not. Filter then returns the array with only the kept values.

##### Array.prototype.reduce()

The reduce method goal is to *reduce* all elements of the array it iterates on into a single value. How it aggregates those elements is up to you.

```js
const sum = numbers.reduce(
  function(acc, n) {
    return acc + n;
  },
  0 // accumulator variable value at first iteration step
);

console.log(sum) //21
```

Just like for .map and .filter methods, .reduce is applied on an array and takes a function as the first parameter.

This time though, there are changes:

- .reduce takes two parameters

The first parameter is a function that will be called at each iteration step.

The second parameter is the value of the accumulator variable (*acc* here) at the first iteration step (read next point to understand).

- Function parameters

The function you pass as the first parameter of .reduce takes two parameters. The first one (*acc* here) is the accumulator variable, whereas the second parameter (*n*) is the current element.

The accumulator variable is equal to the return value of your function at the **previous** iteration step. At the first step of the iteration, *acc* is equal to the value you passed as .reduce second parameter.

###### At first iteration step

```acc = 0``` because we passed in 0 as the second parameter for reduce

```n = 0``` first element of the *number* array

Function returns *acc* + *n* --> 0 + 0 --> 0

###### At second iteration step

```acc = 0``` because its the value the function returned at the previous iteration step

```n = 1``` second element of the *number* array

Function returns *acc* + *n* --> 0 + 1 --> 1

###### At third iteration step

```acc = 1``` because its the value the function returned at the previous iteration step

```n = 2``` third element of the *number* array

Function returns *acc* + *n* --> 1 + 2 --> 3

###### At fourth iteration step

```acc = 3``` because it's the value the function returned at the previous iteration step

```n = 3``` fourth element of the *number* array

Function returns *acc* + *n* --> 3 + 3 --> 6

###### [...] At last iteration step

```acc = 15``` because it's the value the function returned at the previous iteration step

```n = 6``` last element of the *number* array

Function returns *acc* + *n* --> 15 + 6 --> 21

As it is the last iteration step, **.reduce** returns 21.

#### External Resource

- [Understanding map / filter / reduce in JS](https://hackernoon.com/understanding-map-filter-and-reduce-in-javascript-5df1c7eee464)

### Spread operator "..."

The spread operator ```...``` has been introduced with ES2015 and is used to expand elements of an iterable (like an array) into places where multiple elements can fit.

#### Sample code

```js
const arr1 = ["a", "b", "c"];
const arr2 = [...arr1, "d", "e", "f"]; // ["a", "b", "c", "d", "e", "f"]
```

```js
function myFunc(x, y, ...params) {
  console.log(x);
  console.log(y);
  console.log(params)
}

myFunc("a", "b", "c", "d", "e", "f")
// "a"
// "b"
// ["c", "d", "e", "f"]
```

```js
const { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 }

const n = { x, y, ...z };
console.log(n); // { x: 1, y: 2, a: 3, b: 4 }
```

#### Explanation

##### In iterables (like arrays)

If we have the two following arrays:

```js
const arr1 = ["a", "b", "c"];
const arr2 = [arr1, "d", "e", "f"]; // [["a", "b", "c"], "d", "e", "f"]
```

*arr2* the first element is an array because *arr1* is injected as is into *arr2*. But what we want is *arr2* to be an array of letters. To do so, we can *spread* the elements of *arr1* into *arr2*.

With spread operator

```js
const arr1 = ["a", "b", "c"];
const arr2 = [...arr1, "d", "e", "f"]; // ["a", "b", "c", "d", "e", "f"]
```

##### Function rest parameter

In function parameters, we can use the rest operator to inject parameters into an array we can loop in. There is already an **argument** object bound to every function that is equal to an array of all the parameters passed into the function.

```js
function myFunc() {
  for (var i = 0; i < arguments.length; i++) {
    console.log(arguments[i]);
  }
}

myFunc("Nick", "Anderson", 10, 12, 6);
// "Nick"
// "Anderson"
// 10
// 12
// 6
```

But let's say that we want this function to create a new student with its grades and with its average grade. Wouldn't it be more convenient to extract the first two parameters into two separate variables, and then have all the grades in an array we can iterate over?

That's exactly what the rest operator allows us to do!

```js
function createStudent(firstName, lastName, ...grades) {
  // firstName = "Nick"
  // lastName = "Anderson"
  // [10, 12, 6] -- "..." takes all other parameters passed and creates a "grades" array variable that contains them

  const avgGrade = grades.reduce((acc, curr) => acc + curr, 0) / grades.length; // computes average grade from grades

  return {
    firstName: firstName,
    lastName: lastName,
    grades: grades,
    avgGrade: avgGrade
  }
}

const student = createStudent("Nick", "Anderson", 10, 12, 6);
console.log(student);
// {
//   firstName: "Nick",
//   lastName: "Anderson",
//   grades: [10, 12, 6],
//   avgGrade: 9,33
// }
```

> **Note:** createStudent function is bad because we don't check if grades.length exists or is different from 0. But it's easier to read this way, so I didn't handle this case.

##### Object properties spreading

For this one, I recommend you read previous explanations about the rest operator on iterables and function parameters.

```js
const myObj = { x: 1, y: 2, a: 3, b: 4 };
const { x, y, ...z } = myObj; // object destructuring here
console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 }

// z is the rest of the object destructured: myObj object minus x and y properties destructured

const n = { x, y, ...z };
console.log(n); // { x: 1, y: 2, a: 3, b: 4 }

// Here z object properties are spread into n
```

#### External resources

- [TC39 - Object rest/spread](https://github.com/tc39/proposal-object-rest-spread)
- [Spread operator introduction - WesBos](https://github.com/wesbos/es6-articles/blob/master/28%20-%20Spread%20Operator%20Introduction.md)
- [JavaScript & the spread operator](https://codeburst.io/javascript-the-spread-operator-a867a71668ca)
- [6 Great uses of the spread operator](https://davidwalsh.name/spread-operator)

### Object property shorthand

When assigning a variable to an object property, if the variable name is equal to the property name, you can do the following:

```js
const x = 10;
const myObj = { x };
console.log(myObj.x) // 10
```

#### Explanation

Usually (pre-ES2015) when you declare a new *object literal* and want to use variables as object properties values, you would write this kind of code:

```js
const x = 10;
const y = 20;

const myObj = {
  x: x, // assigning x variable value to myObj.x
  y: y // assigning y variable value to myObj.y
};

console.log(myObj.x) // 10
console.log(myObj.y) // 20
```

As you can see, this is quite repetitive because the properties name of myObj are the same as the variable names you want to assign to those properties.

With ES2015, when the variable name is the same as the property name, you can do this shorthand:

```js
const x = 10;
const y = 20;

const myObj = {
  x,
  y
};

console.log(myObj.x) // 10
console.log(myObj.y) // 20
```

#### External resources

- [Property shorthand - ES6 Features](http://es6-features.org/#PropertyShorthand)



