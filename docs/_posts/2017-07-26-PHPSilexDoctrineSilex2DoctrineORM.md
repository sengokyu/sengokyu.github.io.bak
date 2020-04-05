---
date: '2017-07-26T14:35:24+09:00'
layout: post
published: true
qiita_article_id: 4f3a6c84a255297ab9a5
tags:
- PHP
- Symfony
- doctrine
- Symfony2
- Silex
title: '[PHP][Silex][Doctrine] Silex 2へDoctrine ORMを導入する'
updated: '2017-07-26T14:38:44+09:00'

---
Silex 2には[Doctrine DBAL](http://www.doctrine-project.org/projects/dbal.html)が同梱されていますが、やっぱりORMでプログラム書きたいので、Doctrine ORMを導入します。  
  
# 導入手順  
  
## ステップ 1 - Doctrine ORMの導入  
  
まずDoctrine ORMをcomposerで入れます。  
  
****  
```shell-session:
% php composer.phar require doctrine/orm symfony/yaml
```  
  
## ステップ 2 - サービスプロバイダの導入  
  
ありがたいことにDoctrine ORMをSilexのDIコンテナに載せてくれるサービスプロバイダが公開されていますので、それを導入します。  
  
Doctrine ORM Service Provider → https://github.com/dflydev/dflydev-doctrine-orm-service-provider  
  
****  
```shell-session:
% php composer.phar require dflydev/doctrine-orm-service-provider
```  
  
## ステップ 3 - 設定ファイルの作成  
  
### doctrineコマンドラインツールを使うための設定ファイル  
  
`vendor/bin/doctrine` コマンドを使うための設定ファイルを作成します。  
設定ファル内でDoctrineのEntity Managerを生成します。  
  
**config/cli-config.php**  
```php:config/cli-config.php
use Doctrine\ORM\Tools\Console\ConsoleRunner;

// replace with file to your own project bootstrap
require_once dirname(__DIR__).'/src/app.php';
require_once 'dev.php';

// replace with mechanism to retrieve EntityManager in your app
$entityManager = $app['orm.em'];

```  
  
Doctrine ORM Service Providerのおかげで、`$app['orm.em']`を参照するだけです。  
  
  
### データベース接続用の設定  
  
Silexでも使う`prod.php`と`dev.php`を編集します。  
  
**config\prod.php**  
```php:config\prod.php
// DBへの接続情報です。本番環境用です。
$app['db.options'] = array(
    'driver' => 'pdo_mysql',
    'dbname' => 'mm',
    'host'   => 'localhost',
    'user'   => 'mm_user',
    'password' => 'mm_pass'
);

// Doctrine ORMの設定です。
// マッピングの定義方法（ここでは「annotation」を選んでます）
// エンティティのネームスペース
// エンティティクラスファイルのあるパス
$app['orm.em.options'] = array(
    'mappings' => array(
        array(
            'type' => 'annotation',
            'namespace' => 'ML\Entity',
            'path' => dirname(__DIR__).'/src/MM/Entity'
        )
    )
);

// 生成するプロキシクラスファイルの置き場
$app['orm.proxies_dir'] = dirname(__DIR__).'/src/MM/Proxy';
```  
  
**config/dev.php**  
```php:config/dev.php
// 開発環境での接続設定です。
$app['db.options'] = array(
    'driver' => 'pdo_mysql',
    'dbname' => 'mm_dev',
    'host'   => 'localhost',
    'user'   => 'mm_user',
    'password' => 'mm_pass'
);

```  
  
## （休憩）  
  
ここまでくれば、`vendor/bin/doctrine`コマンドが動くはずです。  
試してみてください。  
  
  
# 使い方  
  
## 使い方 1 - エンティティクラスの作成  
  
エンティティクラスを作成します。  
  
例えば以下のような内容です。  
  
**src/MM/Entity/User.php**  
```php:src/MM/Entity/User.php
namespace MM\Entity;

/**
 * User Entity
 *
 * @Entity(repositoryClass="MM\Repository\UserRepository")
 * @Table(name="users")
 */
class User
{
    /**
     * ID
     *
     * @Id
     * @Column(type="guid")
     * @GeneratedValue(strategy="UUID")
     */
    private $uuid;

    /**
     * email
     *
     * @Column(type="string",length=512)
     */
    private $email;

    // （略）
}
```  
  
setter/getterも必要に応じて定義します。  
  
  
## 使い方 2 - レポジトリクラスの生成  
  
doctrineコマンドを使います。  
  
****  
```shell-session:
% ./vendor/bin/doctrine orm:generate-repositories src
```  
  
レポジトリクラスが生成されます。  
中身空っぽなので、使わなければ要らないです。  
  
  
## 使い方 3 - テーブルの作成  
  
データベースにテーブルを作成します。  
  
****  
```shell-session:
% ./vendor/bin/doctrine orm:schema-tool:create
```  
  
エンティティクラスを修正し、テーブルを更新するときはこのようにします。  
  
****  
```shell-session:
% ./vendor/bin/doctrine orm:schema-tool:update --force
```  
  
  
