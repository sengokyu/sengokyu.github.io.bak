---
'created_at: ': '2019-05-11T16:53:45+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: JavaScript
      versions: []
    - name: test
      versions: []
    - name: "\u30C6\u30B9\u30C8"
      versions: []
    - name: jasmine
      versions: []
    - name: TypeScript
      versions: []
  state: 0
title: "[JavaScript] jasmine spy\uFF08\u3044\u308F\u3086\u308B\u30C6\u30B9\u30C8\u30C0\
  \u30D6\u30EB\u3042\u308B\u3044\u306F\u30E2\u30C3\u30AF\uFF09 \u30EC\u30B7\u30D4\u96C6"
'updated_at: ': '2019-05-11T16:53:45+09:00'

---
あるオブジェクト/関数が、また別のオブジェクト/関数に依存していることは普通にあります。  
ユニットテストのときは、実際の依存先オブジェクトではなく、スタブやタプル、モックと呼ばれるダミーのオブジェクトを使います。  
jasmineでは、このダミーのオブジェクトをSpyと呼んでいます。これはそのSpyの使い方メモです。  
  
# Spyを準備する  
  
## Spy関数を作る  
  
****  
```js:
const spyFunc = jasmine.createSpy('関数名');
```  
  
Spy関数を作成します。  
関数名はなんでもよいです。  
  
  
## Spyメソッドを持ったオブジェクトを作る  
  
****  
```js:
const spyObj = jasmine.createSpyObj('クラス名', [ 'method1', 'method2', ... ]);
```  
  
`method1`、`method2`というメソッドを持ったSpyオブジェクトを作成します。  
クラス名はなんでもよいです。  
  
## 既存オブジェクトにSpyプロパティを作る  
  
****  
```js:
const spyObj = {};
spyOnProperty(spyObj, 'property1', 'get');
const spySetProp = spyOnProperty(spyObj, 'property1', 'set');
```  
  
`property1`という名前のSpyプロパティを`spyObj`に作成しています。  
第3引数のget/setは、値を入出力する方向です。  
  
# Spyの振る舞いを定義する  
  
## 戻り値を設定する  
  
****  
```js:
spy.and.returnValue('hoge');
```  
  
戻り値を`'hoge'`にしています。  
  
メソッドの場合はこのようになります。  
  
****  
```js:
spyObj.method1.and.returnValue('hoge');
```  
  
  
## 複数回呼ばれたときの戻り値をそれぞれ設定する  
  
****  
```js:
spy.and.returnValues('hoge', 'fuga', 'fugo');
```  
  
1回目の戻り値は`'hoge'`、2回目の戻り値は`'fuga'`、3回目の戻り値は`'fugo'`にしています。  
  
## 例外を発生させる  
  
****  
```js:
spy.and.throwError('Error message');
```  
  
## コールバック関数を呼び出す  
  
****  
```js:
// 例）第1引数がコールバック関数だった場合
spy.and.calls.first().args[0](コールバック関数の引数);
```  
  
`first()`は、関数の初回呼び出しを意味します。  
最後の呼び出しの場合は、`mostRecent()`を使います。  
  
****  
```js:
spy.and.calls.mostRecent().args[0](コールバック関数の引数);
```  
  
複数回呼び出される時の回数を指定する場合は、`all()`に添字をつけます。  
  
****  
```js:
spy.and.calls.all()[2].args[0](コールバック関数の引数);
```  
  
  
  
全体としての例はこんな感じです。  
  
****  
```js:
// テスト対象のクラス
class Timeouter {
    constructor(timeout) {
        this._timeout = timeout;
    }

    echoAfter10Secocnds() {
        this._timeout((message) => console.log(message), 10);
    }
}

// テストコード
const timeout = jasmine.createSpy('timeout');
const instance = new Timeouter(timeout);

instance.echoAfter10Seconds();
timeout.calls.first().args[0]('Hello.'); // 初回呼び出し
timeout.calls.all()[1].args[0]('I am Jasmine.'); // 2回目の呼び出し
timeout.calls.mostRecent().args[0]('See you again.'); // 最後の呼び出し
```  
  
  
# テストの方法  
  
## spyが呼ばれたか  
  
****  
```js:
expect(spy).toHaveBeenCalled();
```  
  
## spyが呼ばれなかったか  
  
****  
```js:
expect(spy).not.toHaveBeenCalled();
```  
  
## spyが特定の引数で呼ばれたか  
  
****  
```js:
expect(spy).toHaveBeenCalledWith(期待する引数);
```  
  
## spyが何らかの引数で呼ばれたか  
  
****  
```js:
expect(spy).toHaveBeenCalledWith(jasmine.any(期待する引数の型));

// 例）Number型の場合
expect(spy).toHaveBeenCalledWith(jasmine.any(Number));
```  
  
## `null`あるいは`undefined`以外の引数で呼ばれたか  
  
****  
```js:
expect(spu).toHaveBeenCalledWith(jasmine.anything());
```  
  
## spyがn回呼ばれたか  
  
****  
```js:
// 例）2回呼ばれたか
expect(spy).toHaveBeenCalledTimes(2);
```  
  
## spyがn回目に特定の引数で呼ばれたか  
  
`expect`を複数個並べればよいです。  
  
****  
```js:
expect(spy).toHaveBeenCalled();
expect(spy).toHaveBeenCalled();
expect(spy).toHaveBeenCalledWith(期待する引数);
```  
