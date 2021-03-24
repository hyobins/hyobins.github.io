---
layout: post
published: On
title: Prometheus | InfluxDB Go Client
category: monitoring
subtitle: InfluxDB - Go Client 활용
date: '2021-03-22'
---  


### [Github 바로가기](https://github.com/influxdata/influxdb-client-go)

<br>

# Writes 
iunfluxdb go client는 writing 방법으로 non-blocking방식과 blocking 방식을 지원한다. 


### Non-blocking

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
    
    "github.com/influxdata/influxdb-client-go/v2"
)

func main() {
    client := influxdb2.NewClientWithOptions("http://localhost:8086", "${my-token}",
        influxdb2.DefaultOptions().SetBatchSize(20))
    ctx := context.Background()

    bucketsAPI := client.BucketsAPI()
    org, err := client.OrganizationAPI().FindOrganizationByName(ctx, "mobigen")
    if err != nil {
        fmt.Printf("ERROR. Cannot find organization")
    }

    bucket, err := bucketsAPI.CreateBucketWithName(ctx, org, "rand-buck", domain.RetentionRule{EverySeconds: 3600 * 12})
    if err != nil {
        fmt.Printf("Error. Cannot create bucket")
    }

    // Get non-blocking write client
    writeAPI := client.WriteAPI(org.Name, bucket.Name)
    // Read and log errors
    errorsCh := writeAPI.Errors()
    go func(){
        for err := range errorsCh {
            fmt.Printf("write error: %s\n", err.Error())
        }
    }()

    // write some points
    for i := 0; i <100; i++ {
        // create point
        p := influxdb2.NewPoint(
            "rand-buck", 
            map[string]string{
                "contID":   fmt.Sprintf("contID_%v", i),
                "contName": fmt.Sprintf("contName_%v", i),
                "vendor":   "mobigen",
            },
            map[string]interface{}{
                "utime": rand.Float64(),
                "stime": rand.Float64(),
                "cutime": rand.Float64(),
                "cstime": rand.Float64(),
                "rxByte": rand.Float64(),
                "rxPacket": rand.Float64(),
                "txByte": rand.Float64(),
                "txPacket": rand.Float64(),
                "vmsize": rand.Float64(),
                "vmrss": rand.Float64(),
                "rssfile": rand.Float64(),
            },
            time.Now())
        // write asynchronously
        writeAPI.WritePoint(p)
    }
    // Force all unwritten data to be sent
    writeAPI.Flush()
    // Ensures background processes finishes
    client.Close()
}
```



### 주요 함수

NewPoint 함수는 형식에 맞추어 데이터를 쓰면 된다.

```go
func NewPoint(
	measurement string,
	tags map[string]string,
	fields map[string]interface{},
	ts time.Time,
)
```

```Errors()``` 메소드는 data를 write 할 때 발생한 error를 읽어서 채널에 리턴해주는데, 비동기 방식에서만 사용 가능하다. (그렇지 않으면 이 메소드가 write 프로세스를 block하기 때문)

```go
// Read and log errors
errorsCh := writeAPI.Errors()
go func(){
    for err := range errorsCh {
        fmt.Printf("write error: %s\n", err.Error())
    }
}()
```

<br>

### Blocking
Blocking 방식 writing은 batch size를 명시하지 않고 set of points 단위로 생성된다.

```go
package main

import (
    "context"
    "fmt"
    "math/rand"
    "time"

    "github.com/influxdata/influxdb-client-go/v2"
)

func main() {
    // Create a new client using an InfluxDB server base URL and an authentication token
    client := influxdb2.NewClient("http://localhost:8086", "my-token")
    // Get blocking write client
    writeAPI := client.WriteAPIBlocking("my-org","my-bucket")
    // write some points
    for i := 0; i <100; i++ {
        // create data point
        p := influxdb2.NewPoint(
            "system",
            map[string]string{
                "id":       fmt.Sprintf("rack_%v", i%10),
                "vendor":   "AWS",
                "hostname": fmt.Sprintf("host_%v", i%100),
            },
            map[string]interface{}{
                "temperature": rand.Float64() * 80.0,
                "disk_free":   rand.Float64() * 1000.0,
                "disk_total":  (i/10 + 1) * 1000000,
                "mem_total":   (i/100 + 1) * 10000000,
                "mem_free":    rand.Uint64(),
            },
            time.Now())
        // write synchronously
        err := writeAPI.WritePoint(context.Background(), p)
        if err != nil {
            panic(err)
        }
    }
    // Ensures background processes finishes
    client.Close()
}
```

<br><br>

# Query

```go
package main

import (
    "context"
    "fmt"

    "github.com/influxdata/influxdb-client-go/v2"
)

func main() {
    org := "mobigen"
    token := "${token}"
    url := "http://localhost:8086"

    // Create a new client using an InfluxDB server base URL and an authentication token
    client := influxdb2.NewClient(url, token)
    // Get query client
    queryAPI := client.QueryAPI(org)
    // get QueryTableResult
    result, err := queryAPI.Query(context.Background(), `from(bucket:"rand-buck")
	|> range(start:-8h)
	|> filter(fn:(r) =>
		r._measurement == "rand-buck" and
		r._field == "utime" or r._field == "stime")
    |> pivot(rowKey:["_time"], columnKey:["_field"], valueColumn: "_value")
    |> map(fn: (r) => ({ r with _value: r.utime + r.stime}))
	|> yield(name: "_results")`)

    if err == nil {
        // Iterate over query response
        for result.Next() {
            // Access data
            fmt.Printf("Time: %v\n", result.Record().Time())
            fmt.Printf("ContainerName: %v   |   ", result.Record().ValueByKey("contName"))
            fmt.Printf("utime + stime: %v\n", result.Record().Value())
        }
        // check for an error
        if result.Err() != nil {
            fmt.Printf("query parsing error: %s\n", result.Err().Error())
        }
    } else {
        fmt.Printf("ERROR. Cannot serve qeury result\n")
    }
    // Ensures background processes finishes
    client.Close()
}
```


출력 결과

```sh
[root@node01 hb]# ./influx-goclient
Time: 2021-03-24 01:46:13.295213661 +0000 UTC
ContainerName: name0   |   utime + stime: 1.545169376024632
Time: 2021-03-24 01:46:13.295373905 +0000 UTC
ContainerName: name1   |   utime + stime: 1.0279038335724717
Time: 2021-03-24 01:46:13.295725902 +0000 UTC
ContainerName: name10   |   utime + stime: 1.0135963801679202
Time: 2021-03-24 01:46:13.295754915 +0000 UTC
ContainerName: name11   |   utime + stime: 0.6264307459330203
Time: 2021-03-24 01:46:13.295776496 +0000 UTC
ContainerName: name12   |   utime + stime: 1.5148849208782624
Time: 2021-03-24 01:46:13.29579993 +0000 UTC
ContainerName: name13   |   utime + stime: 0.11503004905526187
```

## Result Data 접근
```go
result.Record().${아래 요소}
```


|함수| Parameter | Output type | 설명
|--|--|--|--|
|Field||string| output table의 field 값 리턴 <br> 한 table에 다수 field가 존재할 시(pivot()) nil return(Type Error)
|Measurement||string| output table의 measurement 값 리턴<br> Measurement 가 다수 존재할 시 nil return(Type Error)
|Start, Stop, Time||time.Time| 해당 데이터의 조회 시작/조회 끝/입력 시간 
|Value||interface{}| Field의 Value값 리턴, <br> _value column의 값이 존재하지 않을 시 nil 리턴
|Values||map[string]interface{}| 기본 output table 전체 값 리턴
|ValueByKey|Key값(string)|interface{}| ```Values()```의 리턴값에서 indexing

```sh
Field       m func() string 
Measurement m func() string
Start       m func() time.Time
Stop        m func() time.Time
String      m func() string
Table       m func() int
Time        m func() time.Time
Value       m func() interface{}
Values      m func() map[string]interface{}
ValueByKey  m func(key string) interface{}
```

주요 field 설명 <br>
result.Record.Field()