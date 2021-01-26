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
    //Docker Daemon에 연결부분
	ctx := context.Background()
	cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
	if err != nil {
		panic(err)
	}

    //Docker api 호출하여 사용
	containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
	if err != nil {
		panic(err)
	}

    //결과값 출력
	for _, container := range containers {
		fmt.Println(container.ID)
	}
}
```


이 예시에 활용된 ContainerList가 api 인데, Go로 작성되어 활용할 수 있는 api list는 [이곳](https://godoc.org/github.com/docker/docker/client) 에서 확인할 수 있다.

<br>

## 컨테이너 정보를 출력해주는 api/결과값 정리

아래 예시들은 go routine을 활용하였으므로 코드내에 waitGroup을 이용하여 wait 처리를 해주어야 한다. <br>


func (*Client) CheckpointList<br>
특정 컨테이너의 checkpointlist return 

```go
go func() {
		defer w.Done()
		var checkpoints []types.Checkpoint
		options := types.CheckpointListOptions{}
		checkpoints, err := cli.CheckpointList(ctx, "7e03a3c395de", options)
		if err != nil {
			panic(err)
		}
		doc, _ := json.MarshalIndent(checkpoints, "", "    ")
		fmt.Println(string(doc))
	}()
```

```bash
null
```

<br><br>

func (*Client) ClientVersion<br>
client가 사용하고있는 api version return

```go
go func() {
		defer w.Done()
		var clientversion string
		clientversion = cli.ClientVersion()

		doc, _ := json.MarshalIndent(clientversion, "", "    ")
		fmt.Println(string(doc))
	}()
```

```bash
"1.41"
```





