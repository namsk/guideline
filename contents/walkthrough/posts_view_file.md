# posts 뷰 변경

`index` 액션의 뷰 파일 중 인스턴스 변수(`@posts`)를 사용하는 부분만 집중해서 보자.

```html
<% @posts.each do |post| %>
  <tr>
    <td><%= post.title %></td>
    <td>
      <%= link_to 'Show', [post.bulletin, post], class:'btn btn-default' %>
      <%= link_to 'Edit', edit_bulletin_post_path(post.bulletin, post), class:'btn btn-default' %>
      <%= link_to 'Destroy', [post.bulletin, post], method: :delete, data: { confirm: 'Are you sure?' }, class:'btn btn-default' %>
    </td>
  </tr>
<% end %>
```

`@posts` 인스턴스 변수는 배열을 가진다. 루비의 배열 메소드인 `each`는 리시버(`.each` 앞에 있는 객체)의 각 요소를 하나씩 반복해서 `do` 블록 변수(여기서는 `post`)로 넘겨 준다. 따라서 `<%= post.title %>`는 `post` 객체의 `title` 속성값이 삽입된다.


## navbar 메뉴변경

`app/views/layouts/application.html.erb` 파일에서 아래와 같이 `<ul class='nav navbar-nav'>` 부분을 아래와 같이 변경한다.

```html
<ul class="nav navbar-nav">
  <li class="<%= params[:bulletin_id] == '공지사항' ? 'active' : '' %>"><%= link_to '공지사항', bulletin_posts_path('공지사항') %></li>
  <li class="<%= params[:bulletin_id] == '새소식' ? 'active' : '' %>"><%= link_to '새소식', bulletin_posts_path('새소식') %></li>
  <li class="<%= params[:bulletin_id] == '가입인사' ? 'active' : '' %>"><%= link_to '가입인사', bulletin_posts_path('가입인사') %></li>
</ul>
```

이제 상단 메뉴 항목를 클릭하면 해당 게시판으로 이동하고 해당 항목이 주황색의 글씨로 표시된다.

![](http://i1373.photobucket.com/albums/ag392/rorlab/Photobucket%20Desktop%20-%20RORLAB/rcafe/2014-05-09_11-19-28_zps89cc799c.png)
