# Bulletin 모델의 생성

때로는 글의 성격에 따라 별도로 관리할 필요가 있다. `게시판`의 개념을 도입하면 원하는 만큼의 게시판을 작성하여 글을 게시판별로 묶을 수 있다. 이를 위해서 `Bulletin`이란 모델을 작성하기로 하자.

```bash
$ bin/rails g scaffold Bulletin title description:text
      invoke  active_record
      create    db/migrate/20140503084439_create_bulletins.rb
      create    app/models/bulletin.rb
      invoke    test_unit
      create      test/models/bulletin_test.rb
      create      test/fixtures/bulletins.yml
      invoke  resource_route
       route    resources :bulletins
      invoke  scaffold_controller
      create    app/controllers/bulletins_controller.rb
      invoke    erb
      create      app/views/bulletins
      create      app/views/bulletins/index.html.erb
      create      app/views/bulletins/edit.html.erb
      create      app/views/bulletins/show.html.erb
      create      app/views/bulletins/new.html.erb
      create      app/views/bulletins/_form.html.erb
      invoke    test_unit
      create      test/controllers/bulletins_controller_test.rb
      invoke    helper
      create      app/helpers/bulletins_helper.rb
      invoke      test_unit
      create        test/helpers/bulletins_helper_test.rb
      invoke    jbuilder
      create      app/views/bulletins/index.json.jbuilder
      create      app/views/bulletins/show.json.jbuilder
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/bulletins.js.coffee
      invoke    scss
      create      app/assets/stylesheets/bulletins.css.scss
      invoke  scss
   identical    app/assets/stylesheets/scaffolds.css.scss
```

DB 마이그레이션 후 브라우저에서 확인해 보자.

```bash
$ bin/rake db:migrate
$ open http://localhost:3000/bulletins
```

`posts` 뷰 페이지들과 같이 `bootstrap` 클래스로 스타일을 수정한 후 브라우저를 다시 로딩하면 아래와 같다.


[게시판 목록]
![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-09_09-28-59_zpsc7622442.png)

[게시판 수정]
![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-09_09-33-57_zps1ff0d6f4.png)

[게시판 보기]
![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-09_09-38-11_zps4cb136f4.png)

## 타임존(Timezone)

[게시판 보기] 화면캡쳐에서 `Created at`(생성일) 값을 보면 `2014-05-03 08:59:18 UTC`와 같다. 레일스의 디폴트 타임존 변경은  `config/application.rb` 파일에서 할 수 있다.

```ruby
... 중략~
    # Set Time.zone default to the specified zone and make Active Record auto-convert to this zone.
    # Run "rake -D time" for a list of tasks for finding time zone names. Default is UTC.
    # config.time_zone = 'Central Time (US & Canada)'
... 중략~
```

레일스의 디폴트 타임존은 `UTC`이고, 로컬 타임존 목록을 보기 위해서는 터미널에서 아래와 같이 명령을 실행한다.

```bash
$ bin/rake -D time
rake time:zones:all
    Displays all time zones, also available: time:zones:us, time:zones:local -- filter with OFFSET parameter, e.g., OFFSET=-6

$ bin/rake time:zones:local

* UTC +09:00 *
Irkutsk
Osaka
Sapporo
Seoul
Tokyo
```

타임존을 `Seoul`로 설정하기 위해서는 아래와 같이 값을 변경하고 로컬 웹서버 다시 시작한다. (config/application.rb)

```ruby
config.time_zone = 'Seoul'
```

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-09_09-42-45_zps285adb2d.png)

이제 `Created at` 값이 '2014-05-03 17:59:18 **+0900**' 와 같이 변경된 타임존에 맞게 나타나는 것을 볼 수 있다.

[주의사항]

> 이와 같이 타임존을 변경하여 시간을 해당 타임존에 맞게 표시할 수 있지만, 데이터베이스 저장된 값을 항상 UTC 타임존으로 저장된다는 것을 주목하자. DB로부터 `UTC`로 저장된 시간을 불러와 표시할 때는 레일스가 config/application.rb 에 저장된 time_zone 값에 맞게 자동으로 변경한 후에 표시한다.

> 그러나, DB에 저장할 때도 로컬 타임존에 맞게 저장하려면 `config.time_zone = 'Seoul'` 아래에  `config.active_record.default_timezone = :local`와 같이 추가해 주면 된다.




