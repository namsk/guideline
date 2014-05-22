# deploy.rb 설정

앞서 `Capistrano 설치`를 잘 마무리 했다면 `config/deploy.rb`라는 파일을 살펴보자.
배포시 실행하고자 하는 스크립트나 서버주소 등 배포에 필요한 정보들을 설정하는 설정 파일이다.
처음 `Capistrano 설치`후 파일을 열어보면 아래와 같다.

```ruby
set :application, 'my_app_name'
set :repo_url, 'git@example.com:me/my_repo.git'

# Default branch is :master
# ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }.call

...(중략)

namespace :deploy do
  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      # Your restart mechanism here, for example:
      # execute :touch, release_path.join('tmp/restart.txt')
    end
  end

  after :publishing, :restart

  after :restart, :clear_cache do
    on roles(:web), in: :groups, limit: 3, wait: 10 do
      # Here we can do anything such as:
      # within release_path do
      #   execute :rake, 'cache:clear'
      # end
    end
  end

end
```

기본적으로 `Rake`에서 사용하는 문법을 사용한다. 문법에 대한 자세한 내용은 [Rake](http://rake.rubyforge.org/) 문서 참고바람.


## set

:키-값 지정 메소드인 `set`은 배포시 사용할 변수(키-값 프로퍼티)를 설정한다.

### 기본 키-값 키 목록

* `:application` - 프로젝트 이름

* `:repo_url` - 소스 저장소 주소

* `:deploy_to` - 배포 할 디렉토리 지정


##namespace

배포를 위한 명령어 그룹(네임스페이스)을 지정할 때 사용한다.
Rake의 **`db:setup`** 명령어에서 **`"db"`** 가 네임스페이스이다. 이 네임스페이스에는 DB관련 명령어들이 정의되어있다.

Capistrano를 통한 배포 명령어의 기본 네임스페이스는 **`"deploy"`**이다.

> **Info** 필자의 경우 레일즈 상용배포시 `Nginx+Unicorn` 조합을 많이 사용하는데, `unicorn`이란 네임스페이스를 활용하여 서버 시작(unicorn:start) / 종료(unicorn:stop) / 재시작(unicorn:restart) / 새로고침(unicorn:reload) 등의 명령어를 모아 사용하고 있다.

## task

배포시 실행 구문의 기본 단위이다. **`deploy:restart`**는 `"deploy"`라는 배포 명령어 그룹에서 서버를 재시작(`restart`)하라는 명령어이다. `deploy.rb` 파일에서 임의의 명령어 `task`를 추가하거나 변경할 수 있다.

## after
기본 `task` 명령어에 이어서 다른 `task`를 등록하고자 할 때 사용한다.
위에서 처럼

```ruby
after :restart, :clear_cache do
...
end
```
`after` 명령어는 `restart`라는 실행 구문 뒤에 캐시를 클리어 하라는 실행을 추가해준다.
이를 이용하여, 로그파일 초기화나 배포 후 서버를 재시작 할때하는데 사용될 수 있다. 뒤에 `do ~ end` 코드블럭을 추가하여 특정 서버에서 동작하게 하거나 조건을 추가할 수 있다.


> **Hint** 루비에서 do end는 {}와 동일하게 동작하며 코드블럭이라 불린다. 앞에오는 메쏘드에서 미리 지정된 변수를 전달 받아 주어진 스크립트를 수행한다. do |변수| ... end 또는 {|변수| ... } 로 사용된다.

## ask

터미널에서 배포시 프롬프트로 값을 불러올 수 있다. 3.0에서 새로 추가되었다.

```ruby
ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }.call

```

위의 설정을 추가하면 배포시 다음과 같은 프롬프트가 나타난다.

```bash
...(이전 생략)
Please enter branch: |master|
>
```

이를 이용하여 `git` 저장소에 특정 `branch`를 배포에 활용 할 수 있다.

