---
date: '2017-07-05T14:13:41+09:00'
layout: post
published: true
qiita_article_id: b55c0611c9b7491d876d
tags:
- PHP
- CakePHP
title: '[PHP] CakePHP - HABTM(HasAndBelongsToMay)と中間テーブルの罠'
updated: '2017-07-05T14:13:41+09:00'

---
# 前提 - 罠にはまるひと  
  
CakePHPで、こんなことをしているひとは罠にはまります。  
  
- モデル間アソシエーションとしてHABTM(HasAndBelongsToMany)を使っている  
- HABTMの中間テーブルをモデルとして使っている  
  
図にすると以下のような感じです。  
ModelAとModelBが多対多の関係にあります。  
CakePHPで多対多を実現するときは、中間テーブルを使います。  
  
本来であれば、中間テーブルにはModelAとModelBの主キーだけを格納しておくべきなのですが、普通にモデルとして使ってしまう場合です。  
  
```
+--------+ n   n +--------+
+ ModelA +---+---+ ModelB |
+--------+   |   +--------+
             |
       +-----+-----+
       | Assoccor  |
       +-----------+       
```  
  
  
# 罠の内容  
  
CakePHP 1.3時代からあるネタのようです。  
  
HABTMを持つモデルのfind等を呼び出すと、中間テーブルに対するモデルが`AppModel`のインスタンスとして生成されてしまい、自前で用意した中間テーブルに対するモデルが使われない。  
  
何を言っているかわからｎ(ry  
  
上の例で説明しますと、  
  
`ModelA#find`や`ModelB#find`を呼び出すと、`Assoccor`クラスのインスタンスではなく、`AppModel`のインスタンスが、内部的なレジストリに登録されてしまいます。  
  
ここで`Assoccor`クラスを使おうとすると、`AppModel`のインスタンスがやってきます。  
  
コードで書くと、このような感じです。  
  
**SomeController.php**  
```php:SomeController.php
class SomeController {
    var $uses = array('ModelA', 'Assoccor');

    public function doSomething() {
        // ...
        $result = $this->ModelA->findAll();
        // ↑ ここで中間テーブルに対するモデルのインスタンスも生成されている

        // ...
        $this->Assoccor->someSpecialMethod(); 
        // ↑ AppModelのインスタンスになっているので、
        //   メソッドが見つからないというエラーになる
    }
}

```  
  
  
# 解決策  
  
アソシエーションを定義するとき、`with`パラメータで中間テーブルに対するクラス名も渡してあげます。  
  
****  
```php:
array('className' => 'ModelB',
      'joinTable' => 'model_a_model_b',
      'with'      => 'Assoccor'
      );
```  
  
  
  
# 参考リンク  
  
- [あの日見たHABTMの中間テーブルの使い方を僕たちはまだ知らない ](https://norm-nois.com/blog/archives/1358)  
