## rbenv 설치

[레일스 공식웹사이트](http://rubyonrails.org/download)에서는 `rbenv`을 이용하여 루비를 설치할 것을 권고하고 있다.

[`rbenv`](https://github.com/sstephenson/rbenv)을 이용하면 어플리케이션 별로 각기 다른 루비 버전을 쉽게 사용할 수 있고 새 버전이 릴리스될 때도 쉽게 업데이트할 수 있다. 아래에 운영시스템별로 설치법을 정리했습니다.

---

### 맥 OS X

맥에서는 [Homebrew](http://brew.sh/) 패키지 매니저를 이용하면 `rbenv`과 `ruby-build`를 쉽게 설치할 수 있다.


```
$ brew update
$ brew install rbenv ruby-build
```

