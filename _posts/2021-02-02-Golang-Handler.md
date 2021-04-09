---
layout: post
published: On
title: Go | Golang Handler
subtitle: golang handler 패턴
category: golang
date: '2021-02-02'
---

# HTTP 서버

Go의 표준 패키지인 net/http 패키지는 웹 관련 서버(및 클라이언트) 기능을 제공한다. 주요 http 패키지 메서드로 ListenAndServe(), Handle(), HandleFunc() 등을 들 수 있다. <br>
ListenAndServe() 메서드는 지정된 포트에 웹 서버를 열고 클라이언트 Request를 받아들여 새 go 루틴에 작업을 할당하는 일을 한다.<br><br>

## Handle과 HandleFunc()
Handle()과 HandleFunc() 메서드는 요청된 request path에 어떤 request 핸들러를 사용할 지를 지정하는 라우팅 역할을 한다.


### __http.HandleFunc()__

```go
http.HandleFunc("/hello", func(w http.ResponseWriter, req *http.Request) {
    w.Write([]byte("Hello World"))
})
```

### __http.Handle()__

아래 예제는 http.Handle()메서드를 사용한 예제이다. http.Hanlde()는 첫번째 파라미터로 url 을 받아들이고, 두번째 파라미터는 testHandler 인스턴스를 new() 함수로 생성하여 전달한다. 

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


# Introduction

인턴의 업무(?) 중에서 가장 어렵고 복잡하면서 (조금은)지루한 일은 ```소스코드 분석``` 이었다. 특히 Node Exporter 라는 구조화된 프로그램을 분석하면서 http server-client 패턴을 접할 기회가 많았는데, 이번 기회에 제대로 정리해보려고 한다.<br>

[참고한 게시물](https://www.integralist.co.uk/posts/understanding-golangs-func-type/)

<br><br>
다음은 가장 기본적인 Go based web server 이다.

```go
package main

import (
  "fmt"
  "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Hello %s", r.URL.Path[1:])
}

func main() {
  http.HandleFunc("/World", handler)
  http.ListenAndServe(":8080", nil)
}
```

이 간단한 web server 구현을 4가지 방법으로 해볼 수 있다. 사실 3가지인데, 1,2번은 결국 같은 방법이기 때문이다. 

1. No request parsing
2. Manual request parsing
3. Multiplexer
4. Global multiplexer 
   
<br>

## No request parsing

```go
package main

import (
  "fmt"
  "log"
  "net/http"
)

type pounds float32

func (p pounds) String() string {
  return fmt.Sprintf("£%.2f", p)
}

type database map[string]pounds

func (d database) ServeHTTP(w http.ResponseWriter, r *http.Request) {
  for item, price := range d {
    fmt.Fprintf(w, "%s: %s\n", item, price)
  }
}

func main() {
  db := database{
    "foo": 1,
    "bar": 2,
  }

  log.Fatal(http.ListenAndServe("localhost:8000", db))
}
```

가장 raw 한 예시이다. ```ListenAndServe```의 두번째 argument로 db를 전달한다. 

> 참고: argument는 전달 인자, 인자를 의미한다. 함수 혹은 메서드를 호출 할때 전달 혹은 입력되는 실제값이다. 반면 parameter는 함수 혹은 메서드 정의에서 나열되는 변수 명이다.

위 코드를 보면 db는 database type의 인스턴스 이고, 이 database type은 keys 값으로 strings, values 값으로 pounds값을 가진다. 또한 이 database type은 ServeHTTP 메서드를 가지기 때문에 ListenAndServe 메서드의 두번째 인자값은 Handler 타입이어야 한다는 조건을 만족한다. <br>

위 web server는 URL값에 관계없이 동일한 요청을 처리하는 것이다. 예를들어 

```sh
http://localhost:8000/
http://localhost:8000/abc
http://localhost:8000/xyz
```

세가지 결과 모두 

```sh
foo: £1.00
bar: £2.00
```

라는 결과값을 리턴한다. 

<br><br>

## Manual request parsing

위 코드에 ```ServeHTTP``` 메서드에 switch문을 추가하여 다양한 routes를 다룰 수 있는 기능을 추가해보자. 

```go
package main

import (
    "fmt"
    "log"
    "net/http"
)

type pounds float32

func (p pounds) String() string{
    return fmt.Sprintf("£%.2f", p)
}

type database map[string]pounds

func (d database) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    switch r.URL.Path {
    case "/foo":
        fmt.Fprintf(w, "foo: %s\n", d["foo"])
    case "/bar":
        fmt.Fprintf(w, "bar: %s\n", d["bar"])
    default:
        w.WriteHeader(http.StatusNotFound)
        fmt.Fprintf(w, "No page found for: %s\n", r.URL)
    }
}


func main() {
    db := databse{
        "foo": 1,
        "bar": 2,
    }

    log.Fatal(http.ListenAndServe("localhost:8080", db))
}
```

<br><br>

## Multiplexer 
위 코드는 각 case별로 나누어 처리할 수 있게되었다. 그러나 
위 코드에서 switch 로 분기된 부분을 함수로 만들어 준다. 그리고 이번에는 ServeHTTP 대신 ServeMux를 사용해보자.




### 참고

[HttpServer](http://golang.site/go/article/111-%EA%B0%84%EB%8B%A8%ED%95%9C-%EC%9B%B9-%EC%84%9C%EB%B2%84-HTTP-%EC%84%9C%EB%B2%84) <br>
[golang function type](https://www.integralist.co.uk/posts/understanding-golangs-func-type/)