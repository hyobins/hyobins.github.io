---
layout: post
published: On
title: Prometheus | InfluxDB Go Client
category: monitoring
subtitle: InfluxDB - Go Client 활용
date: '2021-03-22'
---  

# Go Client

[Github](https://github.com/influxdata/influxdb-client-go)


```go
package main

import (
    "context"
    "fmt"

    "github.com/influxdata/influxdb-client-go/v2"
)

func main() {
    // Create a new client using an InfluxDB server base URL and an authentication token
    client := influxdb2.NewClient("http://localhost:8086", "${token}")
    // Get query client
    queryAPI := client.QueryAPI("mobigen")
    // get QueryTableResult
    result, err := queryAPI.Query(context.Background(), `from(bucket:"my-buck")
	|> range(start:-10s)
	|> filter(fn:(r) =>
		r._measurement == "node_container_cpu_cstime" and
		r._field == "gauge" and
        r.name == "nginx1g")
	|> yield(name: "_results")`)
    if err == nil {
        // Iterate over query response
        for result.Next() {
            // Notice when group key has changed
            if result.TableChanged() {
                fmt.Printf("table: %s\n", result.TableMetadata().String())
            }
            // Access data
            fmt.Printf("value: %v\n", result.Record().Value())
        }
        // check for an error
        if result.Err() != nil {
            fmt.Printf("query parsing error: %s\n", result.Err().Error())
        }
    } else {
        panic(err)
    }
    // Ensures background processes finishes
    client.Close()
}
```

```sh
[root@node01 ~]# ./influx-goclient
ru2n
table: col{0: name: result, datatype: string, defaultValue: _results, group: false},col{1: name: table, datatype: long, defaultValue: , group: false},col{2: name: _start, datatype: dateTime:RFC3339, defaultValue: , group: true},col{3: name: _stop, datatype: dateTime:RFC3339, defaultValue: , group: true},col{4: name: _time, datatype: dateTime:RFC3339, defaultValue: , group: false},col{5: name: _value, datatype: double, defaultValue: , group: false},col{6: name: _field, datatype: string, defaultValue: , group: true},col{7: name: _measurement, datatype: string, defaultValue: , group: true},col{8: name: id, datatype: string, defaultValue: , group: true},col{9: name: name, datatype: string, defaultValue: , group: true},col{10: name: pid, datatype: string, defaultValue: , group: true},col{11: name: type, datatype: string, defaultValue: , group: true}
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
value: 3
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
value: 0
```


```influx``` cli 결과와 value값이 같음.

<br><br>

# Writes 
iunfluxdb go client는 writing 방법으로 non-blocking방식과 blocking 방식을 지원한다. 


```go
package main

import (
    "fmt"
    "math/rand"
    "time"
    
    "github.com/influxdata/influxdb-client-go/v2"
)

func main() {
    // Create a new client using an InfluxDB server base URL and an authentication token
    // and set batch size to 20 
    client := influxdb2.NewClientWithOptions("http://localhost:8086", "${my-token}",
        influxdb2.DefaultOptions().SetBatchSize(20))
    ctx := context.Background()

    // Create a bucket API client
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



# Built-in Functions

```go
func NewPoint(
	measurement string,
	tags map[string]string,
	fields map[string]interface{},
	ts time.Time,
)
```




1. 출력형식 맞추기 
   timg, containerID, Name, 