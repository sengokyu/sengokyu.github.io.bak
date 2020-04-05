---
date: '2017-01-18T10:06:25+09:00'
layout: post
published: true
qiita_article_id: 260015eca079090ec726
tags:
- Java
- JavaScript
- spring
- Angular
- gulp
title: '[Java][Spring][JavaScript][gulp][webpack][uglify] 私的AngularJSアプリ開発環境'
updated: '2017-01-18T10:06:25+09:00'

---
# 背景  
  
JavaとAngularJSでSPAなWebアプリケーションを作っていました。  
小規模なので、warのなかに全部入れてデプロイしてます。  
  
JavaScriptファイルがたくさん増えてきて、なんとかまとめたいなと思いました。  
  
  
## 葛藤の歴史  
  
こんな葛藤の結果です。  
  
warの中にJavaScriptも含むぞ！  
↓  
`<script>`タグたくさん書くの面倒だし、ファイル1個にまとめたい。  
そのほうがHTTPコネクションも少なくなるし。  
↓  
Mavenでまとめよう！  
↓  
どうもMavenでJavaScript触るのいけてない。  
minifyしたら、なんか動かないことあるし!?  
↓  
JavaScriptのことは、JavaScriptに任せたほうがよくね？  
↓  
Mavenからnode呼ぶのも、なんかいけてない。  
というか、Jenkinsの入っているビルド環境にはnodeないし。  
↓  
もういいから、ビルドしたJavaScriptをレポジトリに含めちゃおうよ。 ＜ イマココ！  
  
  
  
  
# ディレクトリ構成  
  
このような構成にすることにしました。  
  
  
****  
```text:
src/
  main/
    java/                （Javaソース置き場） 
    js/                  (JavaScriptソース置き場）
      app.js             (webpackへ読み込ませるためのファイル)
      **/*.spec.js         (テストもここ)
      **/*.js              (その他いろいろJavaScript)
    webapp/
      static/
        js/
          bundle.js      （webpackでまとめたファイル）
          bundle.min.js  (uglifyで圧縮したファイル）
    resources/           （プロパティファイル等置き場）
  test/
    java/                （Javaテストソース置き場）
    resources/           （テスト用プロパティファイル等置き場）
gulpfile.js
karma.conf.js
package.json
pom.xml
```  
  
`bundle.js`と`bundle.min.js`は、あらかじめビルドしてレポジトリに格納することにしました。  
  
# 開発作業時は……  
  
gulpとkarmaを動かして、常にbundle.jsを更新させています。  
  
NetBeansの自動デプロイを有効にして、bundle.jsが反映されるようにしています。  
  
# 設定ファイル達  
  
## gulp  
  
**gulpfile.js**  
```js:gulpfile.js
'use strict';

var gulp       = require('gulp');
var webpack    = require('gulp-webpack');
var uglify     = require('gulp-uglify');
var rename     = require('gulp-rename');
var path       = require('path');
var fs         = require('fs');
var karma      = require('karma');

var config = {
    srcdir: './src/main/js',
    dstdir: './src/main/webapp/static/js',
    src: 'app.js',
    dst: 'bundle.js',
    dstmin: 'bundle.min.js',
    karmaconf: path.join(__dirname, 'karma.conf.js')
};

gulp.task('webpack', () => {
    var opts = {
        debug: true,
        output: { filename: config.dst },
        devtool: '#source-map'
    };
    
    return gulp.src(path.join(config.srcdir, config.src))
        .pipe(webpack(opts))
        .pipe(gulp.dest(config.dstdir));
});

gulp.task('uglify', [ 'webpack' ], () => {
    var opts = {
        warnings: true
    };

    return gulp.src(path.join(config.dstdir, config.dst))
        .pipe(rename(config.dstmin))
        .pipe(uglify(opts))
        .pipe(gulp.dest(config.dstdir));
});

gulp.task('karma', ['uglify'], () => {
    var opts = {
        configFile: config.karmaconf,
        singleRun: true
    };

    new karma.Server(opts).start();
});

gulp.task('clean', () => {
    fs.unlink(path.join(config.dstdir, config.dst), () => {});
    fs.unlink(path.join(config.dstdir, config.dst) + '.map', () => {});
    fs.unlink(path.join(config.dstdir, config.dstmin), () => {});
});

gulp.task('all', [ 'uglify' ]);

gulp.task('default', ['all'], () => {
    var files = [
        path.join(config.srcdir, '**/*.js'),
        '!' + path.join(config.srcdir, '**/*.spec.js'),
        '!' + path.join(config.srcdir, '**/*.test.js')
    ];
    gulp.watch(files, ['all']);
});

```  
  
