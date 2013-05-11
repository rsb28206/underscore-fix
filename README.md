Underscore-fix
==============

2013 Minori Yamashita <ympbyc@gmail.com>

Underscoreに足りない関数を全部乗せ。

Sequence
--------

SequenceはStringやargumentsやArrayなど、lengthプロパティを持ったオブジェクト.

### _.slice ###

```
[a] * Number -> [a]
[a] * Number * Number -> [a]
```

シーケンスSを取り、そのsliceメソッドを適用するか、Arrayにキャストしてからsliceメソッドを適用する.

```javascript
_.slice([1,2,3,4,5], 2); //=> [3,4,5]
_.slice("abcde", 2, 4); //=> "cd"
_.slice({length: 4, 0:1, 1:2, 2:3, 3:4}, 2,4); //=> [3, 4]
```

### _.concat ###

```
[a] * [a] -> [a]
[a] * a -> [a]
[a] * a * a -> [a]
...
```

シーケンスSを取り、そのconcatメソッドを適用するか、Arrayにキャストしてからconcatメソッドを適用する.

```javascript
_.concat([1,2,3], 4, 5 6); //=> [1,2,3,4,5,6]
_.concat([1,2,3], [4,5,6]); //=> [1,2,3,4,5,6]
```

### _.len ###

```
[a] -> Number
```

シーケンスのlengthプロパティを読み出す

```javscript
_.len([1,2,3]); //=> 3
_.len("abcde"); //=> 5
```


Array
-----

### _.join ###

```
[String] * String -> String
```

配列のjoinメソッドを呼ぶ.

```javascript
_.join(["aaa", "bbb", "ccc"], "~"); => "aaa~bbb~ccc"
```

Object
------

### _.merge ###

```
{} * {} -> {}
{} * {} * {} -> {}
...
```

2つ以上のオブジェクトをマージした新しいオブジェクトを返す. スロット名が衝突した場合、後ろのものが優先される.

```javascript
_.merge({a:1, b:2, c:3},
        {c:"a", d:"b", e:"f"},
        {e: 8});

//=> {a:1, b:2, c:"a", d:"b", e:8}
```

### _.mapmap ###

```
{} -> Function a -> {a}
```

_.mapは配列を返すが、これはオブジェクトを返す.

```javascript
_.mapmap({a:1, b:2, c:3}, _.partial(_['+'], 5)); //=> {a:6, b:7, c:8}
```

### _.json ###

```
String -> {}
{} -> String
```

文字列が与えられたらJSON.parse、それ以外ならJSON.stringifyする.

```javascript
_.json({a:1, b:[2, 3]}); //=> '{"a":1, "b":[2, 3]}'
_.json('{"a": 1, "b": [2, 3]}', {a:1, b:[2, 3]});
```

Function
--------

### _.apply ###

```
Function a -> Array -> a
```

関数Fと配列Vを取り、`F.apply(null, V)`する.

```javascript
_.apply(function (a, b, c) { return [a,b,c]; },
        [1,2,3]); //=> [1,2,3]
```

### _.flip ###

```
(a * b -> c) -> (b * a -> c)
(a * b * c -> d) -> (b * a * c -> d)
...
```

関数の第一引数と第二引数の位置を入れ替える.

```javascript
_.flip(_['-'])(10, 5); //=> -5
```

### _.flippar ###

```
Function -> Function
```

flipとpartialをひとまとめにしたもの.

```javascript
var join_with_sharp = _.flippar(_.join, "#");

join_with_sharp(["aaa", "bbb", "ccc"]); //=> "aaa#bbb#ccc"
```

### _.pipe ###

```
a * (a -> b) -> b
a * (a -> b) * (b -> c) -> c
...
```

F#の`|>`, Clojureの`->>`

```javascript
_.pipe("hello, ",
       _.flippar(_['+'], "world!"),
       _.str.capitalize); //=> "Hello, world!"
```

### _.iff ###

```
Boolean * (() -> a) * (() -> a) -> a
```

ifの関数版

```javascript
_.iff(1 < 5,
      _.partial(_.identity, "yes"),
      _.partial(_.identity, "no")); //=> "yes"
```

### _.optarg ###

```
Number * Function -> Function
```

数値Nと関数Fを取り、関数Fへの引数のN番目以降を配列にしてFに渡す関数Gを返す。argumentsのスライシングを抽象化する。

```javascript
var f = _.optarg(2, function (a, b, cs) {
  return cs;
});

f(1, 2, 3, 4, 5, 6, 7); //=> [3, 4, 5, 6, 7]
```

### _.bin_multi ###

```
(a * a -> a) -> (a * a * a * ... -> a)
```

`a * a -> a`な二引数関数を無限に引数を取れる関数に変換する.

```javascript
var add = _.bin_multi(function (a) { return a + b; });

add(1,2,3,4,5,6,7,8,9); //=> 45
```

String
------

### _.simple_template ###

```
String * {} -> String
```

eval禁止などで_.templateが使えない時や、テンプレートにロジックがない場合に使いやすいテンプレーティング関数.

```javascript
_.simple_template("quick {{ color }} {{animal}} is {{color}}",
                  {color: "brown", animal: "fox"});

//=> "quick brown fox is brown"
```

Methods
-------

### _.fn ###

```
{} * String -> Function
```

オブジェクトとメソッド名を取り、メソッドを関数にして返す.

```
var toUpper = _.fn(String.prototype, "toUpperCase");
toUpper("hello"); //=> "HELLO"
```

Operator
--------

演算子は関数合成や部分適用のときに扱い辛いので関数を提供する. `a * a -> a`なもの(例えば四則演算`Number * Number -> Number`)に関しては全て多引数化してある.

用意されている関数:

```
_["+"], _["-"], _["*"], _["/"], _["%"],
_.and, _.or, _.not,
_.eq, _.neq, _.lt, _.gt, _.lte, _.gte,
_.at
```

```javascript
_["+"](1,2,3,4,5); //=> 15
_.map([1,2,3,4], _.partial(_["*"], 2)); //=> [2,4,6,8]

_.at({a: {b: {c: "xxx"}}}, "a", "b", "c"); //=> "xxx"
```
