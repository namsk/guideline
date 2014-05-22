# rbenv 설치

[레일스 공식웹사이트](http://rubyonrails.org/download)에서는 `rbenv`을 이용하여 루비를 설치할 것을 권고하고 있다. 그러나 이와 비슷한 것으로 [`rvm`](rvm.html)이란 것도 있다.  어떤 것을 사용할 것인가 망설일 필요가 없다. `rbenv`을 권하는 더 자세한 이유를 알고 싶다면 [Why choose rbenv over RVM?](https://github.com/sstephenson/rbenv/wiki/Why-rbenv%3F)을 참고하기 바란다.

[`rbenv`](https://github.com/sstephenson/rbenv)을 이용하면 어플리케이션 별로 각기 다른 루비 버전을 쉽게 사용할 수 있고 새 버전이 릴리스될 때도 쉽게 업데이트할 수 있다. 아래에 운영시스템별로 설치법을 정리했다.


## 맥 OS X

맥에서는 [Homebrew](http://brew.sh/) 패키지 매니저를 이용하면 `rbenv`과 `ruby-build`를 쉽게 설치할 수 있다.

Homebrew는 한줄의 명령어로 설치 가능한데 해당 명령어는 계속 변경되기 때문에 문서에 언급하지 않는다. http://brew.sh 하단의 Install Homebrew를 참고.

Homebrew를 이용한 `rbenv`, `ruby-build` 설치 방법

```bash
$ brew update
$ brew install rbenv ruby-build
```

## 리눅스

리눅스 환경에서 루비의 버전 관리를 도와주는 `rbenv`를 설치해보자. 먼저 우분투를 비롯한 데비안 기반 리눅스에서 `rbenv`를 설치하는 방법을 소개한다.

[`github`](https://github.com)에서 `rbenv`를 받아온다. git 명령으로 `github`의 `rbenv` 프로젝트를 [사용자 홈 디렉토리]/.rbenv에 클론한다.

```bash
$ git clone git://github.com/sstephenson/rbenv.git .rbenv
```

명령 프롬프트에서 `rbenv`를 실행할 수 있게 쉘 환경변수를 수정한다. .bashrc 파일을 읽어들일 수 있도록 편집기를 열어서 .bash_profile에 다음과 같이 추가한다.

```bash
[ -f "$HOME/.profile" ] && source "$HOME/.profile"
[ -f "$HOME/.bashrc" ] && source "$HOME/.bashrc"
```

.bashrc 내용은 다음과 같이 작성한다. `rbenv`가 저장된 디렉토리를 RBENV_ROOT 환경 변수에, `rbenv` 실행 파일이 들어 있는 디렉토리를 PATH에 추가한다. 쉘을 실행할때마다 'rbenv init -' 명령을 실행한다.

```bash
export RBENV_ROOT="${HOME}/.rbenv"
if [ -d "${RBENV_ROOT}" ]; then
  export PATH="${RBENV_ROOT}/bin:${PATH}"
  eval "$(rbenv init -)"
fi
```

루비를 설치하기 위해서는 `rbenv`의 플러그인 `ruby-build`가 필요하다. .rbenv/plugins 디렉토리를 생성하고 `github`에서 `ruby-build`를 받아온다.

```bash
$ mkdir -p ~/.rbenv/plugins
$ cd ~/.rbenv/plugins
$ git clone git://github.com/sstephenson/ruby-build.git
```

install 옵션을 사용해서 루비를 설치해보자. 2.0.0-p451 버전을 설치했다.

```sh
$ rbenv install 2.0.0-p451
```

rehash 옵션은 새로운 환경을 재설정하는 옵션으로 새로 루비를 설치하거나 루비 젬을 설치한 다음 반드시 실행해야 한다.

```sh
$ rbenv rehash
```
global 옵션은 전역 설정을 변경하는 옵션으로 시스템에서 해당 버전의 루비를 사용하기 위해 사용한다.

```sh
$ rbenv global 2.0.0-p451
```

설치하고자 했던 루비 버전이 제대로 설치되었는지 확인해보자.

```sh
$ ruby -v
ruby 2.0.0p451 (2014-02-24 revision 45167) [x86_64-linux]
```

루비 설치가 끝났으면 루비 젬을 관리하기 위해 bundler를 설치한다. 새로운 환경을 재설정하기 위해 rehash를 잊지 말자.

```sh
$ gem install bundler
$ rbenv rehash
```

> **Hint** [rbenv-gem-rehash](https://github.com/sstephenson/rbenv-gem-rehash)을 설치하면, 매번 `rbenv rehash` 명령을 실행하지 않아도 된다. 또한, [rbenv-bundler](https://github.com/carsomyr/rbenv-bundler)를 설치하면 `bundle exec`를 생략해도 된다.

## 윈도우

윈도우에서는 `rbenv`을 사용할 수 없다. 관련 문서에 의하면, 대신에 [RubyInstaller](http://rubyinstaller.org) 또는 [Pik](https://github.com/vertiginous/pik)를 사용할 것을 권한다.


> **Note** [Mischa Taylor's Coding Blog](http://misheska.com/blog/2013/06/15/using-rbenv-to-manage-multiple-versions-of-ruby/) : Rbenv is supported on Linux and Mac OS X. Rbenv doesn’t work on Windows (and isn’t really necessary on Windows as there is no system Ruby on Windows). On Windows, the RubyInstaller and/or Pik are both good alternatives to work with multiple versions of Ruby on the same box.
