---
layout: post
published: On
title: Golang + Docker API
subtitle: golang으로 docekr api 호출하여 다양한 정보 얻기
date: '2021-01-26'
---

# Docker API 

Docekr는 Docker daemon과 정보를 주고받기위한 api(=Docker Engine API)를 제공한다. <br>

예시는 [이곳](https://docs.docker.com/engine/api/sdk/examples/) 에서 확인할 수 있다.

<br>

docker ps 와 같은 출력값을 갖는 예시이다. 

```go
package main

import (
	"context"
	"fmt"

	"github.com/docker/docker/api/types"
	"github.com/docker/docker/client"
)

func main() {
	ctx := context.Background()
	cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
	if err != nil {
		panic(err)
	}

	containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
	if err != nil {
		panic(err)
	}

	for _, container := range containers {
		fmt.Println(container.ID)
	}
}
```


이 예시에 활용된 ContainerList가 api 인데, Go로 작성되어 활용할 수 있는 api list는 [이곳](https://godoc.org/github.com/docker/docker/client) 에서 확인할 수 있다.

