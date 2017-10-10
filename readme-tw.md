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

- 函式參數

*解構賦值* 常常被用來解構函式裡的物件參數。

沒有解構的情況：

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
function joinFirstLastName({ firstName, lastName }) { // 我們藉由解構 Person 這個物件來創造出兩個新變數
  return firstName + '-' + lastName;
}

joinFirstLastName(person); // "Nick-Anderson"
```

解構賦值搭配 [箭頭函式](#arrow_func_concept)會有不錯的效果：

```js
const joinFirstLastName = ({ firstName, lastName }) => firstName + '-' + lastName;

joinFirstLastName(person); // "Nick-Anderson"
```

- 陣列

來思考一下以下的陣列：

```js
const myArray = ["a", "b", "c"];
```

沒有解構的情況：

```js
const x = myArray[0];
const y = myArray[1];
```

解構賦值的情況下：

```js
const [x, y] = myArray; // 就是這樣 !

console.log(x) // "a"
console.log(y) // "b"
```

#### 有用的資源

- [ES6 Features - Destructuring Assignment](http://es6-features.org/#ArrayMatching)
- [Destructuring Objects - WesBos](http://wesbos.com/destructuring-objects/)
- [ExploringJS - Destructuring](http://exploringjs.com/es6/ch_destructuring.html)

### 陣列的方法- map / filter / reduce

*Map*, *filter* 和 *reduce* 都是來自於 [*functional programming*](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0)這份開發範例的陣列方法。

總而言之：

- **Array.prototype.map()** 針對一組陣列，對陣列的元素進行操作，並回傳一組操作完成的陣列。
- **Array.prototype.filter()** 針對一組陣列，依據條件來決定該保留哪個元素，並且回傳一組含有被保留元素的陣列。
- **Array.prototype.reduce()** 針對一組陣列，並且將元素總和成一個值。

我推薦盡可能地遵循函式編程的原則去使用這些方法，因為這些方法具有可組合、簡潔、優雅等的特性。

使這些方法後，你在大多數的情況下都可以避免使用 *for* 和 *forEach* 的迴圈。 開始使用時可能需要花些力氣去了解它們，因為你必須去學習一些新的觀念，一旦你了解之後，事情就會變得簡單很多。

#### 程式碼範例

```js
const numbers = [0, 1, 2, 3, 4, 5, 6];
const doubledNumbers = numbers.map(n => n * 2); // [0, 2, 4, 6, 8, 10, 12]
const evenNumbers = numbers.filter(n => n % 2 === 0); // [0, 2, 4, 6]
const sum = numbers.reduce((prev, next) => prev + next, 0); // 21
```

利用 map, filter 和 reduce 的組合算出成績大於等於10分的總合

```js
const students = [
  { name: "Nick", grade: 10 },
  { name: "John", grade: 15 },
  { name: "Julia", grade: 19 },
  { name: "Nathalie", grade: 9 },
];

const aboveTenSum = students
  .map(student => student.grade) // map 後得到 [10,15,19,9]
  .filter(grade => grade >= 10) // filter 後得到 [10,15,19]
  .reduce((prev, next) => prev + next, 0); // 依序總和

console.log(aboveTenSum) // 44 -- 10 (Nick) + 15 (John) + 19 (Julia), Nathalie below 10 is ignored
```

#### 詳細解說

讓我們以下列這組陣列為例子：

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

#### 詳細說明

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

### Object 屬性縮寫

當指派一個變數做為 object 的屬性時，如果變數名稱和屬性名稱相同，可以參考下列的寫法： 
```js
const x = 10;
const myObj = { x };
console.log(myObj.x) // 10
```

#### 詳細解釋

通常 (pre-ES2015) 的情況下，當你宣告一個新 *object literal* ，並且使用變數來做為物件屬性的值，你可能會寫出下面這種程式碼：

```js
const x = 10;
const y = 20;

const myObj = {
  x: x, // 指派變數 x 給 myObj.x
  y: y //  指派變數 y 給 myObj.y
 };

console.log(myObj.x) // 10
console.log(myObj.y) // 20
```

就如同你所看到的，這樣相當的繁瑣，因為 myObj 屬性的名稱和欲指派給屬性的變數名稱都是一樣的。

使用 ES2015 寫法，當變數的名稱和屬性的名稱相同，你可以這樣縮寫：

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

#### 外部資源

- [Property shorthand - ES6 Features](http://es6-features.org/#PropertyShorthand)

### Promises

Promise 是一個物件，可以從非同步函式回傳同步。([ref](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261#3cd0)).

Promises 可以用來避免 [迴圈地獄](http://callbackhell.com/)，並且越來越常在現代的 Javascript 專案中使用。

#### 範例程式碼

```js
const fetchingPosts = new Promise((res, rej) => {
  $.get("/posts")
    .done(posts => res(posts))
    .fail(err => rej(err));
});

fetchingPosts
  .then(posts => console.log(posts))
  .catch(err => console.log(err));
```

#### 詳細說明

當你執行 *Ajax repquest* 時，回傳的過程不是同步的，因為你想要的資源會花一些時間來取得。你請求的資源甚至會因為某些原因(404) 而無法取得。

為了處理這樣的情況， ES2015 提供了*promises* 這個語法。 Promises 有以下三種狀態：

- 進行中(pending)
- 完成(Fulfilled)
- 拒絕(Rejected)

假設我們想要利用 promises 來處理 Ajax request 來獲得 X 這個資源。

##### 創造 promise

首先，我們要來創造一個 promise。 我們會使用 JQuery 的get 方法來執行 Ajax request 以獲得 X。

```js
const xFetcherPromise = new Promise( // 使用 "new" 這個關鍵字來創造一個 promise 並且將它儲存在變數裡
  function(resolve, reject) { // Promise 的建構式接受一個函式為參數，而這個函式裡面又有兩個參數，分別代表成功和失敗
    $.get("X") // 啟動 Ajax request
      .done(function(X) { // 一旦請求完成...
        resolve(X); // ... resolve 代表執行成功的程序，並把 X 做為參數
      })
      .fail(function(error) { // 如果請求失敗...
        reject(error); // ... reject 代表請求失敗的程序，並把錯誤的訊息做為參數
      });
  }
)
```

就如同上述看到的例子，Promise 這個物件帶有一個擁有兩個參數 **resolve** 和 **reject** 的 *執行* 函式。這兩個參數是函式，並且會把 promise 的 *pending* 狀態分別移至 *完成(fulfilled)* 和 *失敗(rejected)* 的狀態。

當實例創造時，Promise 是處於進行中 (pending) 的狀態，而且Promise的 *執行* 函式會立刻被執行。一旦執行函式 *成功(resolve)* 或 *失敗(reject)* ， Promise 會呼叫處理器來處理接下來的事情。

##### Promise 的處理器方法

為了得到 Promise 的結果(或失敗訊息)，我們必須接著使用一些處理方式：

```js
xFetcherPromise
  .then(function(X) {
    console.log(X);
  })
  .catch(function(err) {
    console.log(err)
  })
```

如果 Promise 成功了, *resolve* 這個函式會被執行，並且 ```then``` 的函式參數也會被執行。

如果 Promise 失敗了, *reject* 這個函式會被執行，並且 ```catch``` 的函式參數也會被執行。

> **注意 :** 當Promise 已經呈現 fulfilled 狀態或是 rejected 狀態，且對應的方法也附加上去了，這個方法才會被呼叫，所以非同步操作和處理器的執行程序是沒有競賽條件的(兩者的執行過程不會互相影響)。 [(Ref: MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#Description)

#### 外部資源

- [JavaScript Promises for dummies - Jecelyn Yeen](https://scotch.io/tutorials/javascript-promises-for-dummies)
- [JavaScript Promise API - David Walsh](https://davidwalsh.name/promises)
- [Using promises - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [What is a promise - Eric Elliott](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)
- [JavaScript Promises: an Introduction - Jake Archibald](https://developers.google.com/web/fundamentals/getting-started/primers/promises)
- [Promise documentation - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

### 模版字串(Template literals)

模版字符串(Template literals)是一種針對單一字串或多行字串的 [*expression interpolation*](https://en.wikipedia.org/wiki/String_interpolation)。
換句話說，這是一個新的字串語法，讓你可以在 JavaScript 的表達中更方便的使用 (例如變數)。

#### 程式碼範例

```js
const name = "Nick";
`Hello ${name}, the following expression is equal to four : ${2+2}`;

// Hello Nick, the following expression is equal to four: 4
```

#### 外部資源

- [String interpolation - ES6 Features](http://es6-features.org/#StringInterpolation)
- [ES6 Template Strings - Addy Osmani](https://developers.google.com/web/updates/2015/01/ES6-Template-Strings)

### 標籤模版(Tagged template literals)

模板標籤是放在 *[模版字串(template literal)](#template-literals)* 前的函式。當函式依照這個方式被呼叫，則第一個參數就會是由 *strings* 組成的陣列，它的元素會是除了${}包覆的變數以外的字串。而第二個參數就是用${}包覆的變數，可以使用延伸運算子 `...` 來獲取他們。 [(Ref: MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_template_literals).

> **注意 :** 一個叫做 [styled-components](https://www.styled-components.com/) 的受歡迎library 相當依賴這種使用方式。

以下是他們的作品範例：
```js
function highlight(strings, ...values) {
  const interpolation = strings.reduce((prev, next) => {
    return prev + next + (values.shift() || "");
  }, "");

  return `<mark>${interpolation}</mark>`;
}

const condiment = "jam";
const meal = "toast";

highlight`I like ${condiment} on ${meal}.`;
// "<mark>I like jam on toast.</mark>"
```

在一個有趣的例子: 
```js
function comma(strings, ...values) {
  return strings.reduce((prev, next) => {
    let value = values.shift() || [];
    value = value.join(", ");
    return prev + next + value;
  }, "");
}

const snacks = ['apples', 'bananas', 'cherries'];
comma`I like ${snacks} to snack on.`;
// "I like apples, bananas, cherries to snack on."
```

#### 外部資源
- [Wes Bos on Tagged Template Literals](http://wesbos.com/tagged-template-literals/)
- [Library of common template tags](https://github.com/declandewet/common-tags)

### Imports / Exports

ES6 模組被用來獲得從別的模組明確輸出(exports)的變數或是函式。

我極度推薦你去閱讀 MDN  關於 import/export 的文章(詳見下方外部資料)，它們寫得直接又完整。 

#### 程式碼解釋

##### Named exports()

Named exports 被用來從模組中輸出多個變數。

> **注意 :** 你只能使用 name-export 來輸出命名過的第一類物件[first-class citizens](https://en.wikipedia.org/wiki/First-class_citizen) 。

```js
// mathConstants.js
export const pi = 3.14;
export const exp = 2.7;
export const alpha = 0.35;

// -------------

// myFile.js
import { pi, exp } from './mathConstants.js'; // Named import --  類似解構的語法
console.log(pi) // 3.14
console.log(exp) // 2.7

// -------------

// mySecondFile.js
import * as constants from './mathConstants.js'; // 講所有輸出的值都存進 constants 這個變數
console.log(constants.pi) // 3.14
console.log(constants.exp) // 2.7
```

雖然 named imports 看似跟 *解構賦值* 一樣，但他們完全不同也有著不一樣的語法。They don't support default values nor *deep* destructuring.

Besides, you can do aliases but the syntax is different from the one used in destructuring:

```js
import { foo as bar } from 'myFile.js'; // foo 被引入後，存進 bar 這個變數裡
```

##### 預設 import / export

提到 default export，每個模組只有一個default export。Default export 可以是函式，類別，物件，或其他東西。這個值被視為 "主要" 的輸出值，因為它會是最容易被引入的。 [Ref: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export#Description)

```js
// coolNumber.js
const ultimateNumber = 42;
export default ultimateNumber;

// ------------

// myFile.js
import number from './coolNumber.js';
// Default export，跟它的名子無關，會自動傳入 number 這個變數
console.log(number) // 42
```

函式輸出：

```js
// sum.js
export default function sum(x, y) {
  return x + y;
}
// -------------

// myFile.js
import sum from './sum.js';
const result = sum(1, 2);
console.log(result) // 3
```

#### 外部資源

- [ES6 Modules in bulletpoints](https://ponyfoo.com/articles/es6#modules)
- [Export - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)
- [Import - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
- [Understanding ES6 Modules](https://www.sitepoint.com/understanding-es6-modules/)
- [Destructuring special case - import statements](https://ponyfoo.com/articles/es6-destructuring-in-depth#special-case-import-statements)
- [Misunderstanding ES6 Modules - Kent C. Dodds](https://medium.com/@kentcdodds/misunderstanding-es6-modules-upgrading-babel-tears-and-a-solution-ad2d5ab93ce0)
- [Modules in JavaScript](http://exploringjs.com/es6/ch_modules.html#sec_modules-in-javascript)

### <a name="this_def"></a> JavaScript *this*

*this* 運算子的行為和其他語言的行為是不一樣的，且在大多數的情況下 this 是由函式的呼叫方式而決定。([Ref: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)).

這個部分有許多細節與相對困難之處，我強烈建議你去深入研究下方的外部資源。因此，我會提供一些我個人的想法來幫助你了解 *this* 是如何被決定的。我從 [this article written by Yehuda Katz](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)學習到了這個小技巧。

```js
function myFunc() {
  ...
}

// 在每個論述後面，你都可以在 myFunc 找到 *this* 

myFunc.call("myString", "hello") // "myString" --  call 的第一個參數被傳進 *this* 裡面

// 在非嚴格模式之下
myFunc("hello") // window -- myFunc() 是 myFunc.call(window, "hello") 的語法糖

// 在嚴格模式之下
myFunc("hello") // undefined -- myFunc() 是 myFunc.call(undefined, "hello") 的語法糖
```

```js
var person = {
  myFunc: function() { ... }
}

person.myFunc.call(person, "test") // person Object -- call 的第一個參數被傳進 *this* 裡面
person.myFunc("test") // person Object -- person.myFunc() 是 person.myFunc.call(person, "test") 的語法糖

var myBoundFunc = person.myFunc.bind("hello") // 創造一個函式並且將 "hello" 傳入 *this* value
person.myFunc("test") // person Object -- 綁定方法對原本的方法沒有影響
myBoundFunc("test") // "hello" -- myBoundFunc is person.myFunc with "hello" bound to *this*
```

#### External resources

- [Understanding JavaScript Function Invocation and "this" - Yehuda Katz](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)
- [JavaScript this - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)

### Class
ES6 引入了 JavaScript 類別，它們是基於原型繼承的語法糖，而 不是 真正意義上基於類別的繼承模型。
Javascript是一個 [prototype-based](https://en.wikipedia.org/wiki/Prototype-based_programming) 的語言 (而 Java is [class-based](https://en.wikipedia.org/wiki/Class-based_programming) 的語言)。 ES6 使用了 JavaScript 的類別，這類別是基於原形繼承的語法糖， **並不是** 一個基於類別繼承的模組。([ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)).

如果你對其他語言的 classes 相當熟悉的話 ，你的確會傾向於誤解 Javascript 的 *class* 。請不要在這樣的基礎下去妄自猜想 JavaScript classes 是如何運作的，你要把他是為一種全新的概念。

因為這份文件沒有試圖要從頭開始教你 Javascript。 我相信你已經了解何為原型及原型的行為。但這裡還是有些連結可以幫助你了解這些概念：

- [Understanding Prototypes in JS - Yehuda Katz](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)
- [A plain English guide to JS prototypes - Sebastian Porto](http://sporto.github.io/blog/2013/02/22/a-plain-english-guide-to-javascript-prototypes/)
- [Inheritance and the prototype chain - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

#### 範例

ES6 之前的原型語法：

```js
var Person = function(name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype.stringSentence = function() {
  return "Hello, my name is " + this.name + " and I'm " + this.age;
}
```

使用ES6的新類型語法：

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  stringSentence() {
    return "Hello, my name is " + this.name + " and I'm " + this.age;
  }
}

const myPerson = new Person("Manu", 23);
console.log(myPerson.age) // 23
console.log(myPerson.stringSentence()) // "Hello, my name is Manu and I'm 23
```

#### 外部資源
了解原型:

- [Understanding Prototypes in JS - Yehuda Katz](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)
- [A plain English guide to JS prototypes - Sebastian Porto](http://sporto.github.io/blog/2013/02/22/a-plain-english-guide-to-javascript-prototypes/)
- [Inheritance and the prototype chain - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

了解類別:

- [ES6 Classes in Depth - Nicolas Bevacqua](https://ponyfoo.com/articles/es6-classes-in-depth)
- [ES6 Features - Classes](http://es6-features.org/#ClassDefinition)
- [JavaScript Classes - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

### Async Await

除了 [Promises](#promises)以外，這裡你可能會遇到處理非同步的新語法，稱為 *async / await*。

async/await functions 的目的是要簡化同步使用 promises 的行為，並對一組 Promises 執行一些行為。如同 Promises 類似於結構化的 callbacks，async/await 類似於 generators 和 promises 的組合。 ([Ref: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function))

> **注意:** 在研究 async / await 之前，你必須先了解什麼是 promises 以及它是如何運作的，因為 async / await 是 promise 的延伸。

> **Note 2:** [*await* must be used in an *async* function](https://hackernoon.com/6-reasons-why-javascripts-async-await-blows-promises-away-tutorial-c7ec10518dd9#f3f0), 這意謂著你不能在程式碼的最外層使用 await 因為它不在 async function 的裡面

#### Sample code

```js
async function getGithubUser(username) { // 關鍵字 async 允許我們在函式內使用 await，並且函式會回傳 promise
  try { //async / await 會使用 try/catch 來處理錯誤
    const response = await fetch(`https://api.github.com/users/${username}`); // 同步地等待 fetch promise 完成，才會進行下一步
    return response.json();
  } catch (err) {
    alert(err);
  }
}

getGithubUser('mbeaudru').then(user => console.log(user)); // 記錄 user 的回傳 - 這裡不可使用 await 語法，因為不在 async function 裡面
```

#### 程式碼解說

*Async / Await* 的語法建立在 promises 之上，但是前者允許使用命令式風格來撰寫程式碼。

*async* 運算子將函式轉換成 *promise* ，在這個函式裡面你可以使用 *await* 。

```js
async function myFunc() {
  //因為是 async 函式，所以可以使用 await
  try {
    return "hello world";
  } catch(e) {
    throw new Error();
  }
}

myFunc().then(msg => console.log(msg)) // "hello world" -- myFunc 因為 async 運算子而轉換成promise
```

當 async function 裡 *return* 完成了， Promise 就滿足條件，並且回傳值。如果 async function 出現 error，Promise 的狀態會變成 *rejected*。

*await* 運算子，會等待 *Promise* 滿足條件，且只能用在 *async* function 的區塊。使用時，執行環境會暫停，等到 await 的動作完成後且 Promise 完成或發生錯誤，才會進行下一步。

> **注意:** *fetch* 是 Promise 用來執行 AJAX request 的方法。

讓我們來看看使用Promise的fetch來取得 github 的資料：

```js
function getGithubUser(username) {
  return new Promise((resolve, reject) => {
    fetch(`https://api.github.com/users/${username}`)
      .then(response => {
        const user = response.json();
        resolve(user);
      })
      .catch(err => reject(err));
  })
}

getGithubUser('mbeaudru')
  .then(user => console.log(user))
  .catch(err => console.log(err));
```

*async / await* 的撰寫方法如下：

```js
async function getGithubUser(username) { // promise + await keyword usage allowed
  try { // 我們使用 try / catch 來處理 async function errors
    const response = await fetch(`https://api.github.com/users/${username}`); // 執行暫停直到 fetch promise 完成
    const user = response.json();
    return user; // 等於 Promise 完成後的resolve參數(resolve(user))
  } catch (err) {
    throw new Error(err); //  等於 Promise 失敗後的 rejecte 參數(reject(err))
  }
}

getGithubUser('mbeaudru')
  .then(user => console.log(user))
  .catch(err => console.log(err));
```

*async / await* 的語法特別適合用在一連串相互依賴的 Promises。
舉例而言，如果你需要從資料庫依序 fetch 部落格文章和作者資料：

```js
async function fetchPostById(postId) {
  try {
    const token = await fetch('token_url');
    const post = await fetch(`/posts/${postId}?token=${token}`);
    const author = await fetch(`/users/${post.authorId}`);

    post.author = author;
    return post;
  } catch(e) {
    throw new Error(e);
  }
}

fetchPostById('gzIrzeo64')
  .then(post => console.log(post))
  .catch(err => console.log(err));
```

> **注意:** As you can see, *try / catch* are necessary to handle errors. But if you are making *express routes*, you can use a middleware to avoid error handling and have a very pleasant code to read. See [this article from Alex Bazhenov](https://medium.com/@Abazhenov/using-async-await-in-express-with-node-8-b8af872c0016) to learn more.

#### 外部資源

- [Async/Await - JavaScript.Info](https://javascript.info/async-await)
- [ES7 Async/Await](http://rossboucher.com/await/#/)
- [6 Reasons Why JavaScript’s Async/Await Blows Promises Away](https://hackernoon.com/6-reasons-why-javascripts-async-await-blows-promises-away-tutorial-c7ec10518dd9)
- [JavaScript awaits](https://dev.to/kayis/javascript-awaits)
- [Using Async Await in Express with Node 8](https://medium.com/@Abazhenov/using-async-await-in-express-with-node-8-b8af872c0016)
- [Async Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [Await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
- [Using async / await in express with node 8](https://medium.com/@Abazhenov/using-async-await-in-express-with-node-8-b8af872c0016)
- [告別Promise](https://jigsawye.com/2016/04/18/understanding-javascript-async-await/)

### Truthy / Falsy

在 JavaScript 裡，truthy 或是 falsy value 是一種在布林值分類下，結果等同於 true 或是 false 的值。可以用  ```if``` 條件式來進行布林值判斷:

每一個值都會被分類為```true```，除了以下幾種：

- false
- 0
- "" (empty string)
- null
- undefined
- NaN

這裡有一些 *boolean context* 的例子：

- ```if``` 條件式判斷：

```js
if (myVar) {}
```

```myVar``` 可以是任何的[第一類物件(first-class citizen)](https://en.wikipedia.org/wiki/First-class_citizen) (變數，函式，布林) 但是這些會
在 boolean context 中被轉換成布林值。

- 使用 **NOT** ```!``` 這個邏輯運算子

如果單一運算元為 true，則這個運算子會回傳 false ，反之，則回傳true。

```js
!0 // true -- 0 is falsy so it returns true
!!0 // false -- 0 is falsy so !0 returns true so !(!0) returns false
!!"" // false -- empty string is falsy so NOT (NOT false) equals false
```

- 使用 *Boolean* 物件的建構式

```js
new Boolean(0) // false
new Boolean(1) // true
```

- 使用三元判斷式：

```js
myVar ? "truthy" : "falsy"
```

myVar 會在 boolean context 被評估。

## 總結

### <a name="scope_def"></a> 作用域

係指在作用域中變數和表達式都是 "可見的" 或是可以被查詢的。如果變數或其他表達式不在 "目前的作用域" 當中，則不可被使用。

Source: [MDN](https://developer.mozilla.org/en-US/docs/Glossary/Scope)

### <a name="mutation_def"></a> 變數變化

當一個變數的初始值被改變了，就稱為變數變化。
```js
var myArray = [];
myArray.push("firstEl") // myArray 被變化了
```

如果變數不能被變化，我們稱之為不可變變數 (*immutable* )。

[Check MDN Mutable article](https://developer.mozilla.org/en-US/docs/Glossary/Mutable) for more details.
