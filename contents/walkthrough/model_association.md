# 모델간의 관계선언

`posts` 테이블의 글을 게시판별로 분류하기 위해서는 `Bulletin` 모델과 `Post` 모델 사이에 일대다의 관계선언을 해 줄 필요가 있다.

`app/models/bulletin.rb` 파일을 열고 아래와 같이 추가한다.

```ruby
class Bulletin < ActiveRecord::Base
  has_many :posts, dependent: :destroy
end
```

`app/models/post.rb` 파일을 열고 아래와 같이 추가한다.

```ruby
class Post < ActiveRecord::Base
  belongs_to :bulletin
end
```

[주의사항]

> 두 모델 간의 관계선언을 할 때는 단복수에 주의해야 한다. 위에서와같이 `has_many` 다음에는 항상 복수형(`posts`)을 지정해야 하고, `belongs_to` 다음에는 항상 단수형(`bulletin`)으로 지정해야 한다.

관계선언을 하는 [매크로 스타일의 클래스 메소드](http://stackoverflow.com/a/926655)인 `has_many`와 `belongs_to`는 매우 직관적이어서 별도의 설명이 필요치 않다. 단, `has_many`의 경우 `dependent` 옵션을 지정할 수 있는데, `:destroy`로 지정할 경우, 특정 `bulletin` 레코드를 삭제할 때 이 게시판에 속하는 모든 `posts`도 동시에 삭제된다.

[주의사항]

> 단, 이와 같이 두 모델의 관계를 선언하는 것만으로 실제 DB 테이블이 자동으로 연결되지 않는다. 즉, 관계형 데이터베이스에서 두 테이블이 관계를 가지기 위해서는 자식 테이블 필드 중에 부모 테이블의 `id`를 외래키(foreign key)로 가지고 있어야 한다.

실제 이 두 모델이 액티브레코드를 통해서 연결되는 각각의 테이블을 물리적으로 연결하기 위해서는 `posts` 테이블에 `bulletin_id`(모델명 + 'id')이란 정수형의 필드를 추가해 주어야 한다.

[주의사항]

> 레일스의 모든 모델 클래스는 각각의 해당 테이블의 `primary key`가 `id`인 것으로 가정한다. 이것은 레일스의 규칙이다. 물론 `id` 이외의 다른 키를 `primary key`로 사용할 수 있지만, 부가적인 작업을 더 해주어야 하기 때문에 매우 불편하다. 따라서 레일스 나라에서 살려면 레일스의 법을 준수할 필요가 있는 것이다.

이번에는 `posts` 테이블에 `bulletin_id` 필드를 추가하기 위해 마이그레이션 파일을 작성해 보자.

```bash
$ bin/rails g migration AddBulletinIdToPosts bulletin_id:integer:index
      invoke  active_record
      create    db/migrate/20140509005154_add_bulletin_id_to_posts.rb
```

위의 `bulletin_id:integer:index`와 같이 추가할 필드명과 데이터형 다음에 `index` 옵션을 지정하면 해당 필드에 대한 인덱스 파일까지 생성한다.

생성된 마이그레이션 파일(`db/migrate/(생성된 일자가 포함된 일련의 숫자)_add_bulletin_id_to_posts.rb`)은 아래와 같다.

```ruby
class AddBulletinIdToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :bulletin_id, :integer
    add_index :posts, :bulletin_id
  end
end
```

이 마이그레이션 파일에 대해서 `db:migrate` 작업을 한다.

```bash
$ bin/rake db:migrate
== 20140509005154 AddBulletinIdToPosts: migrating =============================
-- add_column(:posts, :bulletin_id, :integer)
   -> 0.0035s
-- add_index(:posts, :bulletin_id)
   -> 0.0004s
== 20140509005154 AddBulletinIdToPosts: migrated (0.0040s) ====================
```

## 레일스 콘솔에서 확인

지금까지 작업한 것이 제대로 동작하는지를 확인하기 위해서 터미널에서 아래와 같이 레일스 콘솔을 실행해 보자.

```bash
$ bin/rails console
Loading development environment (Rails 4.1.1)
irb(main):001:0> bulletin = Bulletin.new
=> #<Bulletin id: nil, title: nil, description: nil, created_at: nil, updated_at: nil>
```

그리고 `bulletin.post`까지 입력한 후 `<tab>` 키를 눌러보면 사용할 수 있는 메소드들을 볼 수 있다.

```bash
irb(main):002:0> bulletin.post
bulletin.post_ids   bulletin.post_ids=  bulletin.posts      bulletin.posts=
```

이 메소드들은 두 모델 관계선언으로 인해 자동으로 생성된다.

* `bulletin.post_ids` : 이 메소드는 특정 `bulletin` 객체에 속하는 모든 `post` 객체들의 `id` 값들을 배열로 반환한다. 현재는 빈배열을 반환할 것이다.
* `bulltein.post_ids=` : 이 메소드는 `post` 객체들의 `id` 값들을 요소로하는 배열을 할당해 주어, 해당 `post` 객체들이 이 `bulletin` 객체의 자식 객체들로 등록되도록 한다.
* `bulletin.posts` : 이 메소드는 특정 `bulletin` 객체에 속하는 모든 `post` 객체들을 배열로 반환한다. 현재는 빈배열(`#<ActiveRecord::Associations::CollectionProxy []>`)을 반환할 것이다.
* `bulletin.posts=` : 이 메소드는 `post` 객체들를 요소로하는 배열를 할당해 주어, 해당 `post` 객체들이 이 `bulletin` 객체의 자식 객체들로 등록되도록 한다.

이상으로 두 모델의 관계선언을 완성하였다.

## 관계선언의 잇점

특정 게시판에 하나의 글을 추가한다고 가정해 보자. 우선,  `공지사항`이라는 `bulletin` 객체를 하나 생성하자.

```bash
$ bin/rails console
Loading development environment (Rails 4.1.1)
irb(main):001:0> bulletin = Bulletin.create title:"공지사항"
   (0.1ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "bulletins" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", "2014-05-04 09:05:25.688974"], ["title", "공지사항"], ["updated_at", "2014-05-04 09:05:25.688974"]]
   (1.0ms)  commit transaction
=> #<Bulletin id: 4, title: "공지사항", description: nil, created_at: "2014-05-04 09:05:25", updated_at: "2014-05-04 09:05:25">
```

그리고 `post` 객체도 하나 생성하자.

```bash
irb(main):003:0> post = Post.create title:"레일스 가이드라인 책 집필", content:"초보자를 위한 레일스"
   (0.0ms)  begin transaction
  SQL (0.2ms)  INSERT INTO "posts" ("content", "created_at", "title", "updated_at") VALUES (?, ?, ?, ?)  [["content", "초보자를 위한 레일스 가이드라인"], ["created_at", "2014-05-04 09:06:54.879852"], ["title", "레일스 가이드라인 책 집필"], ["updated_at", "2014-05-04 09:06:54.879852"]]
   (1.3ms)  commit transaction
=> #<Post id: 2, title: "레일스 가이드라인 책 집필", content: "초보자를 위한 레일스 가이드라인", created_at: "2014-05-04 09:06:54", updated_at: "2014-05-04 09:06:54", bulletin_id: nil>
```

현재는 `post.bulletin_id` 값이 `nil`이기 때문에,
`post` 객체와 `bulletin` 객체가 물리적으로 연결되지 않은 상태이다.

```bash
irb(main):004:0> post.bulletin_id
=> nil
```

이 문제를 해결하기 위해서 `post.bulletin_id=` 메소드에 `bulletin.id` 값을 할당한다.

```bash
irb(main):005:0> bulletin.id
=> 4
irb(main):006:0> post.bulletin_id = bulletin.id
=> 4
```

이제 아래와 같이 두 모델의 관계선언이 제대로 설정되었는지를 확인해 보자.

```bash
irb(main):010:0> bulletin.posts
  Post Load (0.2ms)  SELECT "posts".* FROM "posts"  WHERE "posts"."bulletin_id" = ?  [["bulletin_id", 4]]
=> #<ActiveRecord::Associations::CollectionProxy [#<Post id: 2, title: "레일스 가이드라인 책 집필", content: "초보자를 위한 레일스 가이드라인", created_at: "2014-05-04 09:06:54", updated_at: "2014-05-04 09:08:18", bulletin_id: 4>]>
```

지금까지 `bulletin` 객체에 임의의 `post` 객체를 추가하는 과정을 보았다. 왠지 모르게 번잡스러운 느낌이 든다.

두 모델 클래스에서 관계선언을 한 경우에는 이러한 과정을 간단하게 해 줄 수 있다.

```bash
irb(main):011:0> post = bulletin.posts.create title:"두번째 글", content: "관계선언을 이용하여 글을 등록합니다"
   (0.1ms)  begin transaction
  SQL (0.3ms)  INSERT INTO "posts" ("bulletin_id", "content", "created_at", "title", "updated_at") VALUES (?, ?, ?, ?, ?)  [["bulletin_id", 4], ["content", "관계선언을 이용하여 글을 등록합니다"], ["created_at", "2014-05-04 09:20:18.328327"], ["title", "두번째 글"], ["updated_at", "2014-05-04 09:20:18.328327"]]
   (1.0ms)  commit transaction
=> #<Post id: 3, title: "두번째 글", content: "관계선언을 이용하여 글을 등록합니다", created_at: "2014-05-04 09:20:18", updated_at: "2014-05-04 09:20:18", bulletin_id: 4>
irb(main):012:0> bulletin.posts
=> #<ActiveRecord::Associations::CollectionProxy [#<Post id: 2, title: "레일스 가이드라인 책 집필", content: "초보자를 위한 레일스 가이드라인", created_at: "2014-05-04 09:06:54", updated_at: "2014-05-04 09:08:18", bulletin_id: 4>, #<Post id: 3, title: "두번째 글", content: "관계선언을 이용하여 글을 등록합니다", created_at: "2014-05-04 09:20:18", updated_at: "2014-05-04 09:20:18", bulletin_id: 4>]>
irb(main):014:0> bulletin.posts.size
=> 2
```

즉, `bulletin.posts.create`와 같이 `post`를 생성하면 생성되는 `post` 객체의 `bulletin_id` 속성이 `bulletin.id` 값으로 자동으로 지정되기 때문에, `post.bulletin_id=`에 `bulletin.id` 값을 할당하는 과정이 필요 없게 된다.
