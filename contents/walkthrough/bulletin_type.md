# 게시판 레이아웃 작성하기

게시판의 형태는 흔히 세가지 정도로 분류할 수 있다.

* `일반형(bulletin)` : 일반적인 게시판의 형태. 디폴트 형태
* `블로그형(blog)` : 블그로와 게시물의 내용이 일렬로 보이도록 하는 형태
* `갤러리형(gallery)` : 이미지 갤러리처럼 한 줄에 여러개의 썸네일 이미지가 보이도록 하는 형태

## Bulletin 모델에 post_type 속성 추가하기

게시판을 새로 추가할 때, 레이아웃를 지정하기 위해서 `post_type`이라는 속성을 추가하기로 한다. 이 속성은 `string` 속성을 가지는 것으로 하고, `bulletin`, `blog`, `gallery` 값을 가질 수 있다. 이를 위해서 `Bulletin` 모델에 속성을 추가하는 마이그레이션 파일을 생성한다.

```bash
$ bin/rails g migration add_post_type_to_bulletins post_type
      invoke  active_record
      create    db/migrate/20140513063455_add_post_type_to_bulletins.rb
```

그리고 방금 전에 생성된 마이그레이션 파일을 열어 아래와 같이 `post_type`의 디폴트 값을 `bulletin`으로 추가하고,

```ruby
class AddPostTypeToBulletins < ActiveRecord::Migration
  def change
    add_column :bulletins, :post_type, :string, default: 'bulletin'
  end
end
```

저장한 후 `db:migrate` 작업을 한다.

```bash
$ bin/rake db:migrate
== 20140513063455 AddPostTypeToBulletins: migrating ===========================
-- add_column(:bulletins, :post_type, :string, {:default=>"bulletin"})
   -> 0.0037s
== 20140513063455 AddPostTypeToBulletins: migrated (0.0038s) ==================
```

## Strong Parameter 추가하기

`bulletins_controller.rb` 파일의 열어 하단에 있는 `bulletin_params` 메소드에 아래와 같이 `post_type` 속성을 추가한다.

```ruby
def bulletin_params
  params.require(:bulletin).permit(:title, :description, :post_type)
end
```

## Bulletin 뷰 템플릿 파일의 변경

`app/views/bulletins/_form.html.erb` 파일을 열어 아래의 코드를 추가해 준다.

```html
<div class="form-group">
  <%= f.input :post_type, collection: [ ['게시판', 'bulletin'], ['블로그', 'blog']], input_html:{ class:'form-control'} %>
</div>
```

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-13_17-52-00_zps4b0eb3c6.png)

`app/views/bulletins/show.html.erb` 파일을 열어 아래의 코드를 추가해 준다.

```html
<tr>
  <th>Post Type</th>
  <td><%= @bulletin.post_type %></td>
</tr>
```

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-13_17-55-07_zps869b50c5.png)

이렇게 해서 `Bulletin` 모델에서 추가할 작업을 완료되었다.

게시판의 형태에 따른 뷰를 보이도록 하기 위해서는 `app/views/posts/` 디렉토리에 `post_types` 디렉토리를 만들고 이 디렉토리에 `_bulletin.html.erb` 파일과 `_blog.html.erb`, `_gallery_html.erb` 파일을 생성한다.

이제 `posts` 컨트롤러의 `index` 액션 뷰 파일의 모든 내용을 `_bulletin.html.erb` 파일로 옮기고, `index` 뷰 파일은 아래와 같이 수정하자.

```ruby
<%= render "posts/post_types/#{@bulletin.post_type}" %>
```

`render` 메소드는 `partial` 템플릿 파일을 인수로 받아 렌더링 결과를 삽입해 준다. 루비에서는 이중 인용부호내의 `#{expression}`는 표현식의 결과로 대체해 준다. 따라서 `@bulletin.post_type` 값이 `'bulletin'`일 경우 `"posts/post_types/bulletin"`로 평가되어 `app/views/posts/post_types/` 디렉토리의 `_bulletin.html.erb`이라는 `partial` 템플릿 파일을 `render` 메소드가 처리하게 된다.

[주의사항]

> `partial` 템플릿 파일에서는 부모 템플릿 파일에서 사용하는 모든 인스턴스 변수를 그대로 사용할 수 있다.

`_blog.html.erb` 파일의 내용을 아래와 같이 작성한다.

```html
<h2><%= params[:bulletin_id] %></h2>

<% @posts.each do | post | %>
    <div class='post'>
      <div class='title'><%= post.title %></div>
      <div class='content'><%= simple_format post.content %></div>
      <div>
          <%= link_to 'Show', [post.bulletin, post], class:'btn btn-default' %>
          <%= link_to 'Edit', edit_bulletin_post_path(post.bulletin, post), class:'btn btn-default' %>
          <%= link_to 'Destroy', [post.bulletin, post], method: :delete, data: { confirm: 'Are you sure?' }, class:'btn btn-default' %>
      </div>
    </div>
<% end %>

<br>

<%= link_to 'New Post', new_bulletin_post_path, class: 'btn btn-default' %>
```

이제 `가입인사` 게시판의 종류를 `블로그`로 변경한 후,

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-13_18-24-22_zpsd9dcab9f.png)

브라우저에서 상단 메뉴 중 `가입인사`를 클릭해서 보면 아래와 같이 변경되어 보이게 된다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-13_18-27-05_zps11cfa325.png)

위와 같이 보이게 하기 위해서의 약간의 작업을 추가로 해 주어야 한다.
우선 `CSS` 클래스 `post`, `title`, `content`를 `app/assets/stylesheets/posts.css.scss` 파일에 작성해 준다.

```css
@import 'bootstrap/variables';

.post {
  border: 1px solid $gray-light;
  border-radius: 5px;
  padding:1em;
  margin-bottom: 1em;
  .title {
    font-weight: bold;
    font-size: 1.5em;
    margin-bottom:.5em;
  }
}
```

그리고, `app/assets/stylesheets/` 디렉토리에 잇는 `application.css.scss` 파일을 `bootstrap_init.css.scss`로 변경하고, `application.css` 파일을 새로 추가한 후 아래와 같이 작성한다.

```ruby
/*
*= require_self
*= require_tree .
*/
```


