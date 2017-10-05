# Modern JavaScript Cheatsheet 繁體中文版
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

##### *this* 參考

To understand this subtlety introduced with arrow functions, you must know how [this](#this_def) behaves in JavaScript.

In an arrow function, *this* is equal to the *this* value of the enclosing execution context. What it means is that an arrow function doesn't create a new *this*, it grabs it from its surrounding instead.

Without arrow function, if you wanted to access a variable from *this* in a function inside a function, you had to use the *that = this* or *self = this* trick.

For instance, using setTimeout function inside myFunc:

```js
function myFunc() {
  this.myVar = 0;
  var that = this; // that = this trick
  setTimeout(
    function() { // A new *this* is created in this function scope
      that.myVar++;
      console.log(that.myVar) // 1

      console.log(this.myVar) // undefined -- see function declaration above
    },
    0
  );
}
```

But with arrow function, *this* is taken from its surrounding:

```js
function myFunc() {
  this.myVar = 0;
  setTimeout(
    () => { // this taken from surrounding, meaning myFunc here
      this.myVar++;
      console.log(this.myVar) // 1
    },
    0
  );
}
```

#### Useful resources

- [Arrow functions introduction - WesBos](http://wesbos.com/arrow-functions/)
- [JavaScript arrow function - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [Arrow function and lexical *this*](https://hackernoon.com/javascript-es6-arrow-functions-and-lexical-this-f2a3e2a5e8c4)

### Function default parameter value

Starting from ES2015 JavaScript update, you can set default value to your function parameters using the following syntax:

```js
function myFunc(x = 10) {
  return x;
}
console.log(myFunc()) // 10 -- no value is provided so x default value 10 is assigned to x in myFunc
console.log(myFunc(5)) // 5 -- a value is provided so x is equal to 5 in myFunc

console.log(myFunc(undefined)) // 10 -- undefined value is provided so default value is assigned to x
console.log(myFunc(null)) // null -- a value (null) is provided, see below for more details
```

The default parameter is applied in two and only two situations:

- No parameter provided
- *undefined* parameter provided

In other words, if you pass in *null* the default parameter **won't be applied**.

> **Note:** Default value assignment can be used with destructured parameters as well (see next notion to see an example)

#### External resource

- [Default parameter value - ES6 Features](http://es6-features.org/#DefaultParameterValues)
- [Default parameters - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)

### Destructuring objects and arrays

*Destructuring* is a convenient way of creating new variables by extracting some values from data stored in objects or arrays.

To name a few use cases, *destructuring* can be used to destructure function parameters or *this.props* in React projects for instance.

#### Explanation with sample code

- Object

Let's consider the following object for all the samples:

```js
const person = {
  firstName: "Nick",
  lastName: "Anderson",
  age: 35,
  sex: "M"
}
```

Without destructuring

```js
const first = person.firstName;
const age = person.age;
const city = person.city || "Paris";
```

With destructuring, all in one line:

```js
const { firstName: first, age, city = "Paris" } = person; // That's it !

console.log(age) // 35 -- A new variable age is created and is equal to person.age
console.log(first) // "Nick" -- A new variable first is created and is equal to person.firstName
console.log(firstName) // Undefined -- person.firstName exists BUT the new variable created is named first
console.log(city) // "Paris" -- A new variable city is created and since person.city is undefined, city is equal to the default value provided "Paris".
```

**Note :** In ```const { age } = person;```, the brackets after *const* keyword are not used to declare an object nor a block but is the *destructuring* syntax.

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





