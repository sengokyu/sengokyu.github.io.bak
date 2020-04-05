---
date: '2017-08-20T10:08:33+09:00'
layout: post
published: true
qiita_article_id: 2b1fee6a42468e1d2a8c
tags:
- Java
- PHP
- JavaScript
- C#
title: '[Java][JavaScript][C#][PHP] 言語別テストの作り心地主観的比較'
updated: '2017-08-28T16:20:57+09:00'

---
# 自己紹介  
  
名前： sengoku  
Github: sengokyu  
Qiita: sengoku  
  
子供時代　-> National JR-100でBASICを覚える  
学生時代　-> SONY NEWSでunixを覚える  
Visual Basicで仕事を始め、FreeBSDでサーバを立てたり、Perl/PHPでプログラム組んだりしながら今に至る。  
  
----  
  
# 今日話すこと  
  
下記言語におけるテストの作り心地について話します。  
  
* Java  
* JavaScript  
* C＃  
* PHP  
  
----  
  
# Java  
  
----  
  
# [Java] 用意したいもの  
  
* NetBeansなどの統合環境  
* Mavenなどの依存性管理とビルドツール  
* JUnit  
* Hamcrest  
* Mockito  
* Arquillian ← Java EEの場合  
  
---  
  
# [Java] テストクラスを作る  
  
NetBeansの場合、すでにテスト対象クラスがあるのならメニューから選ぶだけ。  
  
----  
  
# [Java] このような雛形ができます  
  
****  
```java:
public class FooTest {
   private Foo instance;

   @Before
   public void setUp() {
       instance = new Foo();
   }

   @Test
   public void testGetBaz() {}
}
```  
  
----  
  
# [Java] テストメソッドの中身を作ります  
  
****  
```java:
String actual = instance.getBaz();
assertThat(actual, is("baz"));
```  
----  
  
# [Java] 実行します  
  
NetBeansの場合、キーボードショートカットを押すだけ。  
  
テスト対象クラスを編集しているときも、ショートカットキーを押せば対応するテストクラスを実行してくれます。  
  
----  
  
# [Java] 依存するクラスはモックにします  
  
****  
```java:
// モックを生成
Foo fooMock = Mockito.mock(Foo.class);
// メソッドの戻り値を設定
Mockito.doReturn("baz").when(fooMock).getBaz();
// メソッドが呼ばれたかどうかをテスト
Mockito.verify(fooMock).getBaz();
```  
  
  
----  
  
# [Java] 使い心地  
  
----  
  
# ショートカットキー押すだけで即実行できていい  
  
----  
  
# モックの作成が簡単  
  
----  
  
# JavaScript  
  
----  
  
# [JavaScript] 用意したいもの  
  
* MacかLinux。Windowsの場合はBash on Ubuntu on Windowsがいいかも  
* VS CodeやEmacs等のお好みのエディタ  
* Google ChromeやFirefox  
* karma  
* karma-jasmine  
  
----  
  
# [JavaScript] テストファイルを作リます  
  
あらかじめ決めた拡張子でファイルを作リます。  
テスト対象.spec.jsが一般的？？？  
  
describeで何をテストするかを書きます。  
itでテスト対象がどんな動きをするのかを書きます。  
  
----  
  
# [JavaScript] こんな感じになります  
  
**foo.spec.js**  
```javascript:foo.spec.js
describe('Fooクラスのテストです', () => {
    let instance;

    beforeEach(() => {
        instance = new Foo();
    });

    it('getBazするとbazが返る', () => {
    });
});
```  
  
----  
  
# [JavaScript] テストの中身を書きます  
  
****  
```javascript:
let actual = instance.getBaz();
expect(actual).toBe('baz');
```  
  
----  
  
# [JavaScript] テストの設定ファイルを書く  
  
* karma.conf.jsというファイルを作ります。  
* 監視対象のファイル名を記載します。  
* プリプロセッサが必要であれば記載します。  
  
----  
  
# [JavaScript] 実行する  
  
karmaを起動しておけば、ファイルの変更を検知して自動的にテストを実行してくれます。  
  
----  
  
# [JavaScript] 依存するクラスや関数はモックにします  
  
****  
```javascript:
// モックを生成
let fooMock = jasmine.createSpyObj('Foo', ['getBaz']);
// メソッドの戻り値を設定
fooMock.getBaz.and.returnValue('baz');
// メソッドが呼ばれたかどうかをテスト
expect(fooMock.getBaz).toHaveBeenCalled();
```  
  
  
----  
  
# [JavaScript] 使い心地  
  
----  
  
# ファイル監視が楽チンすぎる  
  
----  
  
# describeとitに日本語書けてわかりやすい  
  
----  
  
# expectの書き心地が英語っぽくていい  
  
----  
  
# C＃  
  
----  
  
# [C#] 用意したいもの  
  
* Visual Studio （無料じゃないやつ）  
* NFluent  
* NSubstitute  
  
----  
  
# [C#] テストプロジェクトを作ります  
  
実プロジェクトとは別に、テスト専用の別プロジェクトを作ります。  
  
Foo.Project なら Foo.Project.Tests みたいなのを作ります。  
  
----  
  
# [C#] テストクラスを作ります  
  
プレーンなクラスを作り、属性をつけてあげます。  
  
----  
  
# [C#] こんな風になります  
  
**FooTests.cs**  
```csharp:FooTests.cs
[TestClass]
public class FooTests
{
    private Foo instance;
    [TestInitialize]
    public void SetUp() { instance = new Foo(); }
    [TestMethod]
    public void TestGetBaz() {}
}
```  
  
----  
  
# [C#] テストメソッドの中身を書きます  
  
****  
```csharp:
var actual = instance.GetBaz();
Check.That(actual).IsEqualTo("baz");
```  
  
----  
  
# [C#] テストを実行します  
  
Visual Studioのメニューから、Test Explorerウィンドウを開いて、全テストを実行するか、テストを指定するかして実行します。  
  
キーボードショートカットは、割り当ててもなぜか使えませんでした（VS2015)。つらみ  
  
----  
  
# [C#] 依存するクラスはモックにします  
  
****  
```csharp:
// モックの生成
var fooMock = Substitute.For<Foo>();
// 戻り値を設定
fooMock.GetBaz().Returns("baz");
// メソッドが呼ばれたかどうかをテスト
fooMock.Received().GetBaz();
```  
  
----  
  
# [C#] 使い心地　  
  
----  
  
# マウス操作するのがつらい（のは、もう過去のこと？）  
  
----  
  
# 　モック化を阻むフレームワーク内のクラス（は、もう過去のこと？）  
  
----  
  
# PHP  
  
----  
  
# [PHP] 用意したいもの  
  
* お好みのテキストエディタか統合環境  
* PHPUnit  
* Phake  
  
----  
  
# [PHP] テストクラスを作ります  
  
* 適当なフォルダを作成し、その中にテストクラスファイルを置きます。  
* テストクラスは、\PHPUnit\Framework\TestCaseを継承しないといけません。  
* テストメソッドは「test」から始まらないといけません。  
  
----  
  
# [PHP] こんな風にできます  
  
**FooTest.php**  
```php:FooTest.php
class FooTest extends \PHPUnit\Framework\TestCase
{
    private $instance;
    public function setUp() {
        $this->instance = new Foo();
    }
    public function testGetBaz() {}
}
```  
  
----  
  
# [PHP] テストメソッドの中身を書きます  
  
****  
```php:
$actulal = $instance->getBaz();
$this->assertEquals('baz', $actual);
```  
  
----  
  
# [PHP]　テストの設定ファイルを作ります  
  
phpunit.xmlというファイルを作成します。  
  
phpunitの動作を設定したり、読み込むテストファイルの場所を記載します。  
  
----  
  
# [PHP] テストを実行します  
  
phpunitコマンドを実行すると、テストを実行して結果を表示してくれます。  
  
----  
  
# [PHP] PHPでもモックを使いたい  
  
****  
```php:
// モックを生成
$fooMock = Phake::mock('Foo');
// 戻り値を設定
Phake::when($fooMock)->getBaz()->thenReturn('baz');
// 呼ばれたかどうかをテスト
Phake::verify($fooMock)->getBaz();
```  
  
----  
  
# [PHP] 使い心地  
  
----  
  
# とくに可もなく不可もなく  
  
----  
  
# 総合的な印象  
  
----  
  
# Java  
  
* テストを作るのが当たり前過ぎる環境が用意されています  
* テストを作る/実行するストレスが少ない  
* 統合テストも頑張れる  
  
----  
  
# JavaScript  
  
* テストを作るのも実行するのも流麗で気持ちいい  
* 日々日々良くなっていくので、昨日の常識はdeprecateだったりします  
  
----  
  
# C＃  
  
* C#言語自体はいいけれど、.Net Frameworkが絡むとつらみ  
* テスト文化が後から入ってきた（？）ので、まだこなれていない印象  
* .Net Core時代になれば万事解決！？　  
  
----  
  
# PHP  
  
* 可もなく不可もないけれど、ちょっと楽しくない  
  
  
----  
  
# ところで  
  
----  
  
# SQLiteのテストコードは5,000万行、本体は7万行らしい  
  
----  
  
# つまり  
  
----  
  
# ソフトウェア開発とはテストを作ることであると言っていい  
  
----  
  
# ならば  
  
----  
  
# 実稼働するプログラムを作りやすい環境より  
  
----  
  
# テストを作りやすい環境を選ぶのが本筋では！！  
  
----  
  
![逆に考えるんだテスト.jpg](/assets/images/093d871f-135d-00df-b730-c05e036305c0.jpeg)  
  
----  
  
# 終わり  
