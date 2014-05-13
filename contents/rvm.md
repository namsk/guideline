# rvm 설치

: `rbenv`을 원치 않을 경우 대신해서 `rvm`을 설치할 수 있다.

## Ubuntu(우분투)의 경우

* 우선 [curl](http://curl.haxx.se/)이 설치되어 있어야 한다. 가장 안정된 curl은 우분투 소프트웨어 센터에서 제공하는 패키지이다. 우분투 소프트웨어 센터에서 'curl'을 검색하면 쉽게 찾을 수 있다.

* [Ruby Version Manager 홈페이지](https://rvm.io/) 에 있는 [설치방법](http://rvm.io/rvm/install) 중 적당한 것을 골라 설치한다.

* rvm은 자신의 홈디렉토리의 '~/.rvm'에 설치되게 된다. rvm설치를 위해선 root 계정이 필요없지만, rvm 구동에 필요한 패키지 설치, 즉 패키지 의존(Package Dependencies) 때문에 루트 계정이 필요할 수 있다. (참고: [root 계정없이 rvm 설치하는 방법](http://stackoverflow.com/questions/18284364/how-do-i-install-rvm-without-root-access))
