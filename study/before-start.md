# Before we start

[Standard Go Project Layout](https://github.com/golang-standards/project-layout)
[A getting started guide for Go newcomers](https://github.com/alco/gostart)
[How to start a Go project in 2018](https://boyter.org/posts/how-to-start-go-project-2018/)
[Go Project Layout](https://medium.com/golang-learn/go-project-layout-e5213cdcfaa2)

## `cmd` layout
when you need to have more than one application binary
This pattern enables us to use `go get` command
## `pkg` layout
`pkg` directory is where you put your public libraries.
But not always: some people put internal libraries in the `pkg` directory, while others do not.
## `internal` directory
the place to put your private libraries and the application code you don't want others to import
## `vendor` directory
External packages you import in your projects

## Go Lang Korean Communities
- [Golang Korean Community](https://golangkorea.github.io/)
- [그들이 자바·파이썬 대신 고(Go)를 선택한 까닭](http://www.bloter.net/archives/245951)
- [Golang Korea FB community](https://www.facebook.com/groups/golangko/about/)
- [Github Golang Korea Page](https://github.com/golangkorea)
- [Reddit Golang](https://www.reddit.com/r/golang/)

## Why choose Go Lang
- Simple Server structure
- Scalability
- Extensibility 
- Compatibility
Go Lang is a static-typed, multi-core, easy-to-deploy language. Optimization and packaging features are included in the language itself.

Advantages: Concurrency using Channel, Simple [go routine](https://gobyexample.com/goroutines) with strong-typed language, CLEAN code, Interface's convenient, fast compilation and execution 
Disadvantage: Not many full-featured debugger option, Third-party library error, no inheritance, a lot of error handling 

---

※ 데브시스터즈가 사용한 고 언어 활용한 라이브러리도구

- 빔고(vimgo) : 고 언어 에디터
- 고 뎁스(Godeps) : 고의 특정 버전과 라이브러리를 버전을 맞춰주는 패키지 도구. 의존성문제를 해결하는 데 도움이 된다
- 고 에러스(Goerros) : 에러관련 정보를 쉽게 보여주는 도구
- 고 컨베이 (GoConvey) : 고 테스트 도구
- 퀵 패키지(testing/quick) : 고 테스트 도구

※데브시스터즈가 직접 개발한 고 언어 오픈소스 도구

- 씨네(Cine) : 동시성 소프트웨어의 설계 방법 중 하나인 ‘액터 모델(Actor model)’을 고로 구현한 것
- 고퀵(GoQuic) : 구글의 네트워크 프로토콜 QUIC(퀵)을 고 언어용 라이브러리로 만든 라이브러리. 퀵  프로토콜의 구현 코드가 크로미엄 프로젝트 안에 들어있지만, 실제로 가져다 사용하려면 크로미엄과 그 의존성을 모두 빌드해야 해서 무척 까다롭다. 보다 사용하기 쉽도록 퀵만의 핵심 코드와 의존성을 분리한 것이 ‘libquic’이고, 이것을 기반으로 고퀵을 구현했다고 한다
※고 개발시 참고한 문서

- 책 ‘Programming in Go’
- 책 ‘The GO Programming language’
- 레딧 golang
- 한국 Golang Korea 커뮤니티

[Go lang SlideShare](https://www.slideshare.net/100001105751070/go-51748987)

[Python 개발자를 위한 Go](https://www.slideshare.net/leekchan/gophercon-korea-2015-python-go)
