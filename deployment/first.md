# 프로젝트 배포하기

: `Capistrano3`를 이용한 레일스 프로젝트의 배포 자동화

##Capistrano란?

루비로 작성된 원격 서버 배포 자동화 도구. 레일즈 뿐만 아니라 다른 언어로 작성된 코드도 쉽게 배포할 수 있다.

간단한 코드예제
```
role :demo, %w{example.com example.org example.net}
task :uptime do |host|
  on roles(:demo), in: :parallel do
    uptime = capture(:uptime)
    puts "#{host.hostname} reports: #{uptime}"
  end
end
```
> **참고사이트** [capistranorb.com](http://capistranorb.com)
