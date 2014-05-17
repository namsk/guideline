# 갤러리형 레이아웃 작성하기

이전에 언급한 바와 같이 게시판의 종류를 세가지로 분류한 바 있다. 일반형, 블로그형, 갤러리형 세가지 중에 일반형과 블로그형은 이미 전용 레이아웃을 구현하였고, 이제 남은 건 `갤러리형` 레이아웃을 만드는 것이다.

`갤러리형` 게시판은 이미지를 업로드할 수 있으면 업로드된 이미지들은 쎔네일 형태로 보이도록 하고자 한다.

이미지를 업로드를 구현하기 위해서는 두가지 젬을 사용할 수 있다. 하나는 [`paperclip`](https://github.com/thoughtbot/paperclip)이고 다른 하나는 [`carrierwave`](https://github.com/carrierwaveuploader/carrierwave)라는 젬이다.

이 두 젬은 모두 시스템에 `ImageMagick`이라는 툴이 설치되어 있어야 한다. 시스템에서 아래와 같은 명령으로 설치 여부를 알 수 있다.

```bash
$ convert

Version: ImageMagick 6.8.7-7 Q16 x86_64 2013-11-27 http://www.imagemagick.org
Copyright: Copyright (C) 1999-2014 ImageMagick Studio LLC
Features: DPC Modules
Delegates: bzlib freetype jng jpeg ltdl png xml zlib
~중략~
```

만약 설치되어 있지 않으면 [`ImageMagick 설치하기`](../appendices/imagemagick.html)를 참고하여 설치하면 된다.

## Carrierwave 젬 설치하기

여기서는 [`carrierwave`](https://github.com/carrierwaveuploader/carrierwave) 젬을 사용하기로 한다.

Gemfile에 아래와 같이 젬을 추가한다.

```ruby
gem 'carrierwave'
```

젬을 설치한다.

```bash
$ bundle install
```

## 업로드 클래스의 생성

이미지 업로드를 위한 `Picture`라는 이름을 가지는 업로더를 생성한다.

```bash
$ bin/rails g uploader Picture
      create  app/uploaders/picture_uploader.rb
```

생성된 `PictureUploader` 클래스 파일의 내용은 아래와 같다


```ruby
# encoding: utf-8

class PictureUploader < CarrierWave::Uploader::Base

  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  # include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this uploader:
  storage :file
  # storage :fog

  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # Provide a default URL as a default if there hasn't been a file uploaded:
  # def default_url
  #   # For Rails 3.1+ asset pipeline compatibility:
  #   # ActionController::Base.helpers.asset_path("fallback/" + [version_name, "default.png"].compact.join('_'))
  #
  #   "/images/fallback/" + [version_name, "default.png"].compact.join('_')
  # end

  # Process files as they are uploaded:
  # process :scale => [200, 300]
  #
  # def scale(width, height)
  #   # do something
  # end

  # Create different versions of your uploaded files:
  # version :thumb do
  #   process :resize_to_fit => [50, 50]
  # end

  # Add a white list of extensions which are allowed to be uploaded.
  # For images you might use something like this:
  # def extension_white_list
  #   %w(jpg jpeg gif png)
  # end

  # Override the filename of the uploaded files:
  # Avoid using model.id or version_name here, see uploader/store.rb for details.
  # def filename
  #   "something.jpg" if original_filename
  # end

end
```

사용법에 대한 자세한 안내가 코멘트로 처리되어 있다. 코멘트를 제거한 클래스를 보면 아래와 같다.

```ruby
# encoding: utf-8

class PictureUploader < CarrierWave::Uploader::Base

  storage :file

  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

end
```

## Picture 속성 추가하기

`Post` 클래스에 `picture` 속성을 추가하기 위해서 아래와 같이 마이그레이션 클래스 파일을 생성하고 DB 마이그레이션 한다.

```bash
$ bin/rails g migration add_picture_to_posts picture
      invoke  active_record
      create    db/migrate/20140517023813_add_picture_to_posts.rb

$ bin/rake db:migrate
== 20140517023813 AddPictureToPosts: migrating ================================
-- add_column(:posts, :picture, :string)
   -> 0.0023s
== 20140517023813 AddPictureToPosts: migrated (0.0024s) =======================
```

## 업로더 마운트하기

`Post` 클래스 파일(`app/models/post.rb`)을 열어 `PictureUploader`  업로더 클래스를 `picture` 속성으로 마우트한다.

```ruby
class Post < ActiveRecord::Base
  belongs_to :bulletin
  mount_uploader :picture, PictureUploader
end
```

## MiniMagick 젬 추가하기

이미지 크기를 조절하기 위해서 `Rmagick`이나 `MiniMagick` 젬을 추가한다. `carrierwave` 문서에 따르면 `MiniMagick` 젬을 추천하므로 아래와 같이 `Gemfile`에 `minimagick` 젬을 추가하고,

```
gem 'mini_magick'
```

젬을 설치한다.

```bash
$ bundle install
```

## 업로더에 이미지 크기 옵션 추가하기


```ruby
# encoding: utf-8

class PictureUploader < CarrierWave::Uploader::Base

  include CarrierWave::MiniMagick

  storage :file

  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  process :resize_to_fit => [800, 800]

  version :thumb do
    process :resize_to_fill => [200,200]
  end

end
```

## Post 폼에 파일업로드 추가하기

폼 `partial` 템플릿 파일(`app/views/posts/_form.html.erb`)을 아래와 같이 추가한다.

```erb
<% if @post.bulletin.post_type == "gallery" %>
  <div class="form-group">
    <%= f.input :picture, as: :file, input_html:{ class: 'form-control' } %>
    <%= f.hidden_field :picture_cache %>
  </div>
<% end %>
```

위에서 `erb` 코드 부분을 보면, `gallery`형 게시판에서만 이미지를 업로드할 수 있도록 조건을 추가한 것을 주목하자.

## 갤러리 게시판을 생성

우선 게시판 생성시 `Post type`에 `gallery` 옵션을 추가하기 위해 아래와 같이 작성한다.

```erb
<div class="form-group">
  <%= f.input :post_type, collection: [ ['게시판', 'bulletin'], ['블로그', 'blog'], ['갤러리', 'gallery']], input_html:{ class:'form-control'} %>
</div>
```

다음으로, 이미지를 업로드하는 게시판을 생성하기 위해서 `http://localhost:3000/bulletins`로 접속한 후 아래와 같이 "갤러리"라는 게시판을 추가한다. 이 때 `Post_type`에서 `갤러리`로 선택하고 저장한다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-17_12-13-02_zpsb3c96ebb.png)

이렇게 해서 게시판은 총 4개가 되었다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-17_12-18-25_zps5b7f02bb.png)

이제 어플리케이션 레이아웃 파일(`app/views/layouts/application.html.erb`)을 열고, 상단 메뉴항목에 `갤러리`를 추가한다.

```erb
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li class="<%= params[:bulletin_id] == '공지사항' ? 'active' : '' %>"><%= link_to '공지사항', bulletin_posts_path('공지사항') %></li>
    <li class="<%= params[:bulletin_id] == '새소식' ? 'active' : '' %>"><%= link_to '새소식', bulletin_posts_path('새소식') %></li>
    <li class="<%= params[:bulletin_id] == '가입인사' ? 'active' : '' %>"><%= link_to '가입인사', bulletin_posts_path('가입인사') %></li>
    <li class="<%= params[:bulletin_id] == '갤러리' ? 'active' : '' %>"><%= link_to '갤러리', bulletin_posts_path('갤러리') %></li>
  </ul>
```

갤러리 게시판에서 이미지를 추가하는 화면은 아래와 같다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-17_12-31-35_zps0d325841.png)



다음에는 `갤러리`용 `partial` 템플릿 파일을 생성하기 위해  `app/views/posts/post_types/` 디렉토리에 `_gallery.html.erb` 파일을 추가하고 아래와 같이 작성한다.

```erb
<h2><%= params[:bulletin_id] %></h2>

<% @posts.each do | post | %>
    <div class='gallery'>
      <div class='picture'><%= image_tag(post.picture_url) if post.picture? %></div>
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
또한, 업로더 클래스에 아래와 같이 업로드할 수 있는 파일 포맷을 지정할 수 있다.

```ruby
def extension_white_list
  %w(jpg jpeg gif png pdf)
end
```

이제 `.jpg`, `.jpeg`, `.gif`, `.png`, `.pdf` 확장자를 가진 파일만 업로드할 수 있게 된다.


![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/2014-05-17_16-00-09_zps29ff9da5.png)


