---
type: doc
layout: reference
category: "Other"
title: "分解宣言"
---

<!--original
- --
type: doc
layout: reference
category: "Other"
title: "Destructuring Declarations"
- --
-->

# 分解宣言

<!--original
# Destructuring Declarations
-->

いくつかの状況では、オブジェクトをいくつかの変数に _分解_ することが便利になります。例えば:

<!--original
Sometimes it is convenient to _destructure_ an object into a number of variables, for example:
-->

``` kotlin
val (name, age) = person 
```

この構文は _分解宣言_ と呼ばれています。分解宣言は一度に複数の変数を作成します。
ここ二つの変数を宣言しました。: `name` と `age`です。これらは独立して使用できます。:
<!--original
This syntax is called a _destructuring declaration_. A destructuring declaration creates multiple variables at once.
We have declared two new variables: `name` and `age`, and can use them independently:
-->
 
``` kotlin
println(name)
println(age)
```

分解宣言は次のようなコードにコンパイルできます。
<!--original
A destructuring declaration is compiled down to the following code:
-->

``` kotlin
val name = person.component1()
val age = person.component2()
```

`component1()` と `component2()` の関数はKotlinで広く使われている _規約の原則_ の他の例です。
(`+` や `*` や *for*{: .keyword }-ループなどのような演算子を見てください) 。
分解宣言の右辺には、それが要求される数だけコンポーネント関数があれば何が来てもかまいません。
もちろん、`component3()` や `component4()` が続いてもいいのです。

<!--original
The `component1()` and `component2()` functions are another example of the _principle of conventions_ widely used in Kotlin 
(see operators like `+` and `*`, *for*{: .keyword }-loops etc.). 
Anything can be on the right-hand side of a destructuring declaration, as long as the required number of component functions can be called on it.
And, of course, there can be `component3()` and `component4()` and so on.
-->

`componentN()` `operator`キーワードでマークされた関数である必要があることに注意してください。これを使用することで分解宣言で呼出せます。

分解宣言はまた、*for*{: .keyword }-ループでも使用できます。つまりこのように。

<!--original
Note that the `componentN()` functions need to be marked with the `operator` keyword to allow using them in a destructuring declaration.

Destructuring declarations also work in *for*{: .keyword }-loops: when you say
-->

``` kotlin
for ((a, b) in collection) { ... }
```

変数 `a` と `b` はこのcollectionの要素で呼出される `component1()` と `component2()` が返す値を得ます。

<!--original
Variables `a` and `b` get the values returned by `component1()` and `component2()` called on elements of the collection. 
-->

## 例: 関数から二つの値を返す

<!--original
## Example: Returning Two Values from a Function
-->

ここで関数が二つのものを返す必要があるとしましょう。例えば、結果のオブジェクトとなんらかの種類の状態を返すなどです。
Kotlinでこれをコンパクトに行う方法には、[_data class_](data-classes.html) を宣言して、そのインスタンスを返すことです。

<!--original
Let's say we need to return two things from a function. For example, a result object and a status of some sort.
A compact way of doing this in Kotlin is to declare a [_data class_](data-classes.html) and return its instance:
-->

``` kotlin
data class Result(val result: Int, val status: Status)
fun function(...): Result {
    // computations
    
    return Result(result, status)
}

// Now, to use this function:
val (result, status) = function(...)
```

data クラスは自動的に、`componentN()` 関数を宣言するので、分解宣言が動作します。

<!--original
Since data classes automatically declare `componentN()` functions, destructuring declarations work here.
-->

**注意**: 標準クラスの `Pair` を使うこともできます、`Pair<Int, Status>` を返す関数 `function()` があります。
しかし、適切に命名されたデータを使う方がよりよいといえます。

<!--original
**NOTE**: we could also use the standard class `Pair` and have `function()` return `Pair<Int, Status>`, 
but it's often better to have your data named properly.  
-->

## 例: 分解宣言とマップ

<!--original
## Example: Destructuring Declarations and Maps
-->

おそらくもっともよいマップを縦断する方法はこれです。

<!--original
Probably the nicest way to traverse a map is this:
<--

``` kotlin
for ((key, value) in map) {
   // do something with the key and the value
}
```

これがうまく動作するには次の条件を満たしておかなければなりません。

<!--original
To make this work, we should 
-->

* map が `iterator()` 関数によって与えられた値のシーケンスであること
* map の値が `component1()` 関数と `component2()` 関数によって与えられた要素のペアであること。

<!--original
* present the map as a sequence of values by providing an `iterator()` function,
* present each of the elements as a pair by providing functions `component1()` and `component2()`.
-->

そして、標準ライブラリはつぎのような拡張を提供しています。

<!--original
And indeed, the standard library provides such extensions:
-->

``` kotlin
operator fun <K, V> Map<K, V>.iterator(): Iterator<Map.Entry<K, V>> = entrySet().iterator()
operator fun <K, V> Map.Entry<K, V>.component1() = getKey()
operator fun <K, V> Map.Entry<K, V>.component2() = getValue()
  
```  
  
そのためmapを使った *for*{: .keyword }-ループに分解宣言が自由に使用できます。

<!--
So you can freely use destructuring declarations in *for*{: .keyword }-loops with maps (as well as collections of data class instances etc).
-->