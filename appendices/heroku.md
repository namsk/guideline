# Heroku(허로쿠)에 배포하기

공식웹사이트 : https://www.heroku.com

허로쿠 서비스를 이용하면 Capistrano를 사용하지 않코도 레일스 프로젝트를 손쉽게 배포할 수 있다. 헤로쿠 서비스는 개발자가 프로젝트 개발에만 집중할 수 있도록 해 준다. 배포 부분은 허로쿠 서비스가 알아서 대신해 준다.


## 허로쿠 준비부터 배포까지

* [허로쿠 공식웹사이트](https://www.heroku.com)를 방문하여 회원가입한다.
* 본인 계정의 `Dashboard`의 하단에 있는 `Create a new app` 버튼을 클릭하여 새로운 어플리케이션을 생성한다. `App` 이름은 본인이 원하는 것으로 해도 무방하다.
* 로컬머신의 커맨드라인 쉘에서 [`허로쿠 툴벨트`](https://toolbelt.heroku.com)(아래 참고)를 설치한다.
* `Gemfile`에 허로쿠 배포에 필요한 두개의 젬을 추가하고,
  ```ruby
  gem 'pg', group: :production
  gem 'rails_12factor', group: :production
  ```

  기존의 `sqlite3` 젬은 아래와 같이 `:group` 옵션을 추가한다
  ```ruby
  gem 'sqlite3', group: :development
  ```

  설치한다.
  ```bash
  $ bundle install
  ```


* `rails s` 명령으로 개발모드에서 이상없이 작동하는 것이 확인되면 `git`의 원격 저장소 `heroku`를 생성한다.
  ```bash
  $ git remote add heroku git@heroku.com:<heroku-app-name>.git
  ```


* 이제 아래와 같이 허로쿠로 배포작업을 시작한다.

  ```bash
  $ git push heroku master
  ```


* 브라우저에서 `http://<heroku-app-name>.heroku.com`으로 접속해서 확인한다.

* 자세한 내용은 [여기](https://devcenter.heroku.com/articles/quickstart)를 참고하기 바란다.


## 허로쿠에서 레일스 4 프로젝트 배포하기

자세한 내용은 [여기](https://devcenter.heroku.com/articles/getting-started-with-rails4)를 참고하기 바란다.


### 허로쿠 툴벨트 설치하기

로컬 운영체제에 맞는 [`허로쿠 툴벨트`](https://toolbelt.heroku.com)를 설치한다. 이후에는 터미널에서 헤로쿠에서 `heroku` 명령을 사용할 수 있게 된다

```bash
$ heroku
Usage: heroku COMMAND [--app APP] [command-specific-options]

Primary help topics, type "heroku help TOPIC" for more details:

  addons    #  manage addon resources
  apps      #  manage apps (create, destroy)
  auth      #  authentication (login, logout)
  config    #  manage app config vars
  domains   #  manage custom domains
  logs      #  display logs for an app
  ps        #  manage dynos (dynos, workers)
  releases  #  manage app releases
  run       #  run one-off commands (console, rake)
  sharing   #  manage collaborators on an app

(생락...)
```

### pg 젬 추가하기

허로쿠로 배포하면 자동으로 `Postgresql` 데이터베이스를 사용하기 때문에 `Gemfile`에 `pg` 젬을 추가하고,

```ruby
gem 'pg', group: :production
```

설치한다.

```bash
$ bundle install
```

### 허로쿠에서 App 생성하기

![허로쿠 웹사이트](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-22_18-49-38_zps2da0fee1.png)

생성 후 아래와 같은 결과를 보게 된다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-22_19-02-24_zps38d79bf5.png)


이제 `Finish up` 버튼을 클릭해서 종료한다.

### heroku 원격 브랜치 생성하기

```bash
$ git remote add heroku git@heroku.com:rcafe.git
```

### 허로쿠 배포를 위한 젬 추가

아래와 같이 젬을 추가하고,

```ruby
gem 'rails_12factor', group: :production
```

설치한다.

```bash
$ bundle install
```

### 배포 준비

```bash
$ git add .
$ git commit -m "작업을 내용을 커밋함"
$ git push origin  # github에 푸시하기
```


### 허로쿠에 실제 배포하기

```bash
$ git push heroku master  # 허로쿠에 푸시하기
```

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-22_20-51-00_zpsc7d58644.png)


### 브라우저에서 확인하기

```bash
$ open http://rcafe.heroku.com
```





