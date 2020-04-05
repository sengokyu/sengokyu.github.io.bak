---
date: '2017-09-28T20:31:16+09:00'
layout: post
published: true
qiita_article_id: 3ca6a7d8c2b7135ef9bd
tags:
- PHP
title: '[PHP] SplEnumが未だ実験的なので、JavaっぽいEnumクラスを作った'
updated: '2017-12-22T16:05:21+09:00'

---
**2017/10/3追記**  
  
大バグ修正しました。  
  
---  
  
**2017/10/2追記**  
  
下記プログラムには大バグがあります。  
  
親クラス内で定義したstaticなプロパティは、全子クラスで共有されています。  
  
いやはや、面目ない。:fearful:  
  
---  
  
PHPにもEnum欲しいですよね。ね？  
  
期待のSplEnumは、、、  
  
http://php.net/manual/ja/intro.spl-types.php  
> 警告  
> この拡張モジュールは、 実験的 なものです。この拡張モジュールの動作・ 関数名・その他ドキュメントに書かれている事項は、予告なく、将来的な PHP のリリースにおいて変更される可能性があります。 このモジュールは自己責任で使用してください。  
  
まだ実験中だった！！  
  
仕方がないので、ついカッとなって作った。後悔はしていない。  
  
# 何作ったのさ  
  
すでに同案件多数ですが、  
  
[PHP で簡易 Enum を実装する](https://qiita.com/yosugi/items/38c43723501b320330b3)  
[PHPで簡易Enumを実装する](https://qiita.com/t_ishida/items/2e4d2aa31d69abfd48a5)  
  
  
自分的にはこんなものが欲しかったです。  
  
****  
```php:
class Day
{
    const SUNDAY = new Day('日曜日', '市場');
    const MONDAY = new Day('月曜日', '風呂');
    const TUESDAY = new Day('火曜日', '入浴');
    // ...

    public function __constuct($label, $work) { /* ... */ }
    public function getLabel() { /* ... */ }
    public function getWork() { /* ... */ }
}


Day::SUNDAY->getLabel(); // ← 日曜日
Day::SUNDAY->getWork();  // ← 市場
```  
  
`const`で`new`してますので、このコードは動きません。  
いつかできるようになればいいな。。。  
  
  
  
  
# 代替策  
  
## ユースケース  
  
こんな使い勝手のものにしてみました。  
`const`の代わりに`static`メソッド呼び出しです。  
  
****  
```php:
Day::SUNDAY()->getLabel(); // ← 日曜日
Day::SUNDAY()->getWork();  // ← 市場
```  
  
switch caseでも使えますよ。  
  
****  
```php:
function dayEnumTest(Day $day)
{
   switch($day) {
     case Day::MONDAY():
       echo 'Mondays are bad.';
       break;
     case Day::SATURDAY():
     case Day::SUNDAY():
       echo 'Weekends are best.';
       break;
     default:
       echo 'Midweek days are so-so.';
   }
}
```  
  
## Enumの実装  
  
Enumの実装は次のようになります。  
オーバーライドした`initialize`メソッド内で、Enum的なものを初期化しています。  
  
****  
```php:
class Day extends \LAPL\Lang\PHPEnum
{
    protected static function initialize() {
        /* 第1引数が名前、第2引数がインスタンス */
        self::register('SUNDAY', new Day('日曜日', '市場'));
        self::register('MONDAY',  new Day('月曜日', 'お風呂'));
        self::register('TUESDAY', new Day('火曜日', '入浴'));
        // ...
    }
}
```  
  
  
# 作ったもの  
  
こちらにソースを置きました。  
https://github.com/sengokyu/php-enum  
  
さらに詳しい使い方はテストケースを見てください。  
https://github.com/sengokyu/php-enum/blob/master/Tests/PHPEnumTests.php  
