# sengokyu.github.io

## はじめかた

```
docker pull jekyll/jekyll:pages
docker run --rm --name jekyll -it -p 4000:4000 -v "$PWD/docs:/srv/jekyll" jekyll/jekyll:pages jekyll serve
```

## 用意するもの

* git
* Docker
* Visual Studio Code
  * Jekyll Syntax Support

  