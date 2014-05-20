# Capistrano 설치

##설치
Gemfile에 다음 코드를 추가한다
```
gem 'capistrano', '~> 3.2.0'
```
추가 후터미널에서 아래 명령어를 실행한다.
```bash
$ bundle install
```
Capistrano를 처음 설정하기 위해 아래 터미널에서 아래 명령어를 실행한다.
```bash
$ bundle exec cap install
```

초기설정이 완료되면 프로젝트 폴더 아래에 다음과 같은 파일들이 생기는지 확인하자.
```
├── Capfile
├── config
│   ├── deploy
│   │   ├── production.rb
│   │   └── staging.rb
│   └── deploy.rb
└── lib
    └── capistrano
            └── tasks
```

> **참고사이트** [github.com: capistrano/capistrano ](https://github.com/capistrano/capistrano)
