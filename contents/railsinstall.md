# 인스톨러를 이용한 설치법

두가지 인스톨러를 웹에서 찾을 수 있다.

## bitnami 루비스택 인스톨러

BitNami Ruby Stack greatly simplify the development and deployment of Ruby on Rails and its runtime dependencies. It includes ready-to-run versions of Ruby, Rails, MySQL, Git, Subversion, RVM and other components.

https://bitnami.com/stack/ruby/installer


## Engine Yard 에서 제공하는 무료 레일스 인스톨러

http://railsinstaller.org/

<iframe src="http://www.youtube.com/embed/InKZOPylUSQ" frameborder="0" allowfullscreen></iframe>

---

# 매뉴얼 설치법


## 맥 OS X

최신 OS X인 매버릭스(10.9)를 기준으로 설명한다. 맥을 구입하면 기본적으로 ruby가 내장되어 있다. 즉, 시스템 루비를 바로 사용할 수 있게 된다. 설치된 루비 버전을 알기 위해 아래와 같은 명령을 실행해 본다. 시스템 루비는 2.0 버전으로 설치되어 있다.

```sh
$ ruby -v
ruby 2.0
```

그러나 [몇가지 이유](http://robots.thoughtbot.com/psa-do-not-use-system-ruby)로해서 시스템 루비는 가능하면 사용하지 않는 것이 좋다.

대신에 루비 버전 관리자를 설치하여 루비를 버전별로 사용할 수 있도록 한다. 이를 위해서 `rbenv`을 이용하는 것이 추천된다. [rbenv 설치](rbenv.html)를 참고하여 설치한 후, 루비의 최신 버전인 2.1.2를 설치해 보자. 우선 `rbenv` 플러그인인  [`ruby-build`](https://github.com/sstephenson/ruby-build) 목록에 설치가능한 루비 버전이 어떤 것이 있는지 알아보자.

```sh
$ rbenv install list
```

만약, 목록 중에 2.1.2 버전이 없으면 `ruby-build` 플러그인을 업데이트할 필요가 있다. `homebrew`를 이용하여 `ruby-build`를 설치했다면 아래와 같이 업데이트할 수 있다.

```sh
$ brew update ruby-build
```

이제 다시 `rbenv install list` 명령으로 2.1.2 버전이 등록되었는지를 확인하고 있다면 아래와 같이 루비 2.1.2 버전을 rbenv으로 설치한다.

```sh
$ rbenv install 2.1.2
```

이제 레일스를 설치해 보자.

```sh
$ gem install rails
```

그리고 아래와 같이 설치를 마무리 한다.

```sh
$ rbenv rehash
```

아마도 가장 최선버전의 레일스가 설치될 것이다. 2014년 5월 16일 현재 4.1.1 버전이 설치될 것이다. 아래와 같이 설치된 레일스 버전을 확인할 수 있다.

```sh
$ rails -v
Rails 4.1.1
```

## 우분투 14.04 LTS (Trusty Tahr)의 경우

[gorail.com 사이트 참조](https://gorails.com/setup/ubuntu/14.04)

### Ruby 설치 (2.1.2)

Ruby 설치를 위해 필요한 라이브러리 등을 우선 설치합니다.

``` bash
$ sudo apt-get update
$ sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties
```

설치 방법은 rbenv나 rvm을 이용하거나, 혹은 소스로 직접 설치하는 방법이 있습니다. 공식적으로 rbenv를 이용할 것을 권장하고 있습니다.

#### rbenv 를 이용하는 방법

rbenv를 이용해서 ruby를 설치하는 것은 매우 간단합니다. 우선 rbenv를 설치하고, ruby-build를 하면 됩니다.

``` bash
$ cd
$ git clone git://github.com/sstephenson/rbenv.git .rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ exec $SHELL

$ git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ exec $SHELL

$ rbenv install 2.1.2
$ rbenv global 2.1.2
$ ruby -v
```

마지막 작업은 각 패키지들의 문서들을 설치하지 말라고 지정하는 것입니다.

``` bash
$ echo "gem: --no-ri --no-rdoc" > ~/.gemrc
```

#### rvm 을 이용하는 방법

``` bash
$ sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
$ curl -L https://get.rvm.io | bash -s stable
$ source ~/.rvm/scripts/rvm
$ echo "source ~/.rvm/scripts/rvm" >> ~/.bashrc
$ rvm install 2.1.2
$ rvm use 2.1.2 --default
$ ruby -v
```

마지막 작업은 각 패키지들의 문서들을 설치하지 말라고 지정하는 것입니다.

``` bash
$ echo "gem: --no-ri --no-rdoc" > ~/.gemrc
```


#### 소스로 직접 설치하는 방법

``` bash
$ cd
$ wget http://ftp.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz
$ tar -xzvf ruby-2.1.2.tar.gz
$ cd ruby-2.1.2/
$ ./configure
$ make
$ sudo make install
$ ruby -v
```

마지막 작업은 역시 각 패키지들의 문서들을 설치하지 말라고 지정하는 것입니다.

``` bash
$ echo "gem: --no-ri --no-rdoc" > ~/.gemrc
```

### Git 설정

본 가이드의 [`Git 설치하기`](git.html) 참조


### Rails 설치

Rails는 상당히 많은 프로그램에 의존하기 때문에 NodeJS와 같은 Javascript runtime을 설치해야 합니다. 이는 Rails에서 Coffeescript와 the Asset Pipeline을 사용할 수 있도록 해주고, javascript를 최소화해 더 빠른 제작환경을 만들어줍니다.

NodeJS를 설치하기 위해서는 PPA repository를 추가해야 합니다.

``` bash
$ sudo add-apt-repository ppa:chris-lea/node.js
$ sudo apt-get update
$ sudo apt-get install nodejs
```

그리고 rails를 설치합니다.
``` bash
$ gem install rails
```

rbenv를 사용하고 있다면 rails를 사용하기 위해서는 다음의 명령어를 실행해야 합니다.

``` bash
$ rbenv rehash
```

rails가 제대로 설치되었다면 rails -v 명령어로 버전을 확인하면 제대로 설치되었는지 알 수 있습니다.

``` bash
$ rails -v
# Rails 4.1.1
```

만일 다른 결과가 나온다면, 설치가 제대로 되지 않았다는 의미입니다.
