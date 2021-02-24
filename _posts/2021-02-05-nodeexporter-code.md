---
layout: post
published: On
title: Node Exporter 소스코드 분석
subtitle: 오픈소스 node-exporter 소스코드 분석하기
date: '2021-02-05'
---

## 흐름대로 정리 

### node_exporter.go 

[185] kingpin cli parser로 파싱한 명령어들의 정보를 인자로 담아서 newHandler 호출. <br>

```go
http.Handle(*metricsPath, newHandler(!*disableExporterMetrics, *maxRequests, logger))
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte(`<html>
        <head><title>Node Exporter</title></head>
        <body>
        <h1>Node Exporter</h1>
        <p><a href="` + *metricsPath + `">Metrics</a></p>
        </body>
        </html>`))
})
```



```go
//registry를 새로 생성해주고; pre-registered
r := prometheus.NewRegistry()


r.MustRegister(version.NewCollector("node_exporter"))
if err := r.Register(nc); err != nil {
    return nil, fmt.Errorf("couldn't register node collector: %s", err)
}


handler := promhttp.HandlerFor(
    //Gatherers 생성
    prometheus.Gatherers{h.exporterMetricsRegistry, r},
    promhttp.HandlerOpts{
        ErrorHandling:       promhttp.ContinueOnError,
        MaxRequestsInFlight: h.maxRequests,
        Registry:            h.exporterMetricsRegistry,
    },
)
```



