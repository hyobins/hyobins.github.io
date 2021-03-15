---
layout: post
published: On
title: Go | Golang Handler
subtitle: golang handler 패턴
category: golang
date: '2021-02-02'
---

# HTTP 서버

Go의 표준패키지인 http 패키지 메서드로 ListenAndServe(), Handle(), Handle() 등이 있다. <br>
ListenAndServe() 메서드는 지정된 포트에 웹 서버를 열고 클라이언트 Request를 받아들여 새 go 루틴에 작업을 할당하는 일을 한다. <br><br>

## Handle과 HandleFunc()
Handle()과 HandleFunc() 메서드는 요청된 request path에 어떤 request 핸들러를 사용할 지를 지정하는 라우팅 역할을 한다.


### __http.HandleFunc()__

```go
http.HandleFunc("/hello", func(w http.ResponseWriter, req *http.Request) {
    w.Write([]byte("Hello World"))
})
```

### __http.Handle()__

아래 예제는 http.Handler 인터페이스를 갖는 testHandler 라는 struct를 정의하고 이 struct의 메서드 ServeHTTP()을 구현한 예제이다. http.Handle()의 두번째 파라미터는 testHandler 객체를 new() 함수로 생성하여 전달한다. 

```go
package main
 
import (
    "net/http"
)
 
func main() {
    http.Handle("/", new(testHandler))
 
    http.ListenAndServe(":5000", nil)
}
 
type testHandler struct {
    http.Handler
}

//testHandler 객체의 method
func (h *testHandler) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    str := "Your Request Path is " + req.URL.Path
    w.Write([]byte(str))
}
```




<br><br>



### 참고

http://golang.site/go/article/111-%EA%B0%84%EB%8B%A8%ED%95%9C-%EC%9B%B9-%EC%84%9C%EB%B2%84-HTTP-%EC%84%9C%EB%B2%84