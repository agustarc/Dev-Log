* 퇴행성 관절염으로 인해 키보드 타이핑을 덜 하고 싶다.

* Git 명령어도 다 입력하기 귀찮다.

* 그렇다면 아래와 같이 명령어 숏컷을 만들어두고 사용하자.

```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

* 만약 옵션도 포함한 숏컷을 만들고 싶다면 ```" "```로 감싸야 한다.
```
$ git config --global alias.last "log -1 HEAD"
```

* 추가한 Alias 목록은 gitconfig 파일에 기록된다.
