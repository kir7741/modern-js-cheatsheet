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

使用 ```var```宣告的變數是以 *函式的區塊*為作用域，這樣意謂著當變數在函式中被創造時，該函式的所有東西都能夠使用這個變數。 此外,以 *函示作用域*宣告的變數，無法在函式外面被使用。

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

<a name="tdz_sample"></a> Now, what it means for *let* (and *const*) variables for not being accessible before being assigned:

```js
console.log(myVar) // raises a ReferenceError !
let myVar = 2;
```

By contrast with *var* variables, if you try to read or write on a *let* or *const* variable before they are assigned an error will be raised. This phenomenon is often called [*Temporal dead zone*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_Dead_Zone_and_errors_with_let) or *TDZ*.

> **Note:** Technically, *let* and *const* variables declarations are being hoisted too, but not their assignation. Since they're made so that they can't be used before assignation, it intuitively feels like there is no hoisting, but there is. Find out more on this [very detailed explanation here](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified) if you want to know more.

In addition, you can't re-declare a *let* variable:

```js
let myVar = 2;
let myVar = 3; // Raises a SyntaxError
```

##### const

```const``` declared variables behave like *let* variables, but also they can't be reassigned.

To sum it up, *const* variables:

- are *block scoped*
- are not accessible before being assigned
- can't be re-declared in the same scope
- can't be reassigned

```js
const myVar = "Nick";
myVar = "John" // raises an error, reassignment is not allowed
```

```js
const myVar = "Nick";
const myVar = "John" // raises an error, re-declaration is not allowed
```

<a name="const_mutable_sample"></a> But there is a subtlety : ```const``` variables are not [**immutable**](#mutation_def) ! Concretely, it means that *object* and *array* ```const``` declared variables **can** be mutated.

For objects:
```js
const person = {
  name: 'Nick'
};
person.name = 'John' // this will work ! person variable is not completely reassigned, but mutated
console.log(person.name) // "John"
person = "Sandra" // raises an error, because reassignment is not allowed with const declared variables
```

For arrays:
```js
const person = [];
person.push('John'); // this will work ! person variable is not completely reassigned, but mutated
console.log(person[0]) // "John"
person = ["Nick"] // raises an error, because reassignment is not allowed with const declared variables
```

#### External resource

- [How let and const are scoped in JavaScript - WesBos](http://wesbos.com/javascript-scoping/)
- [Temporal Dead Zone (TDZ) Demystified](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified)





