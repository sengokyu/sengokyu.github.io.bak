# sengokyu.github.io

## はじめかた

```
git clone git@github.com:sengokyu/sengokyu.github.io.git
cd sengokyu.github.io
docker run --rm --name jekyll -it -p 4000:4000 -v "$PWD/docs:/srv/jekyll" jekyll/jekyll:pages jekyll serve
```

## 用意するもの

* git
* Docker
* Visual Studio Code
  * Jekyll Syntax Support

  