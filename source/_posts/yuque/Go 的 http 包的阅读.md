
---

title: Go 的 http 包的阅读

urlname: fgpaan

date: 2019-09-08 15:04:15 +0800

tags: []

---
<a name="7qYbs"></a>
# 先看实践：
```go
package main
import (
    "fmt"
    "net/http"
    "strings"
    "log"
)
func sayhelloName(w http.ResponseWriter, r *http.Request) {
    r.ParseForm()  //解析参数，默认是不会解析的
    fmt.Println(r.Form)  //这些信息是输出到服务器端的打印信息
    fmt.Println("path", r.URL.Path)
    fmt.Println("scheme", r.URL.Scheme)
    fmt.Println(r.Form["url_long"])
    for k, v := range r.Form {
        fmt.Println("key:", k)
        fmt.Println("val:", strings.Join(v, ""))
    }
    fmt.Fprintf(w, "Hello astaxie!") //这个写入到w的是输出到客户端的
}
func main() {
    http.HandleFunc("/", sayhelloName) //设置访问的路由
    err := http.ListenAndServe(":9090", nil) //设置监听的端口
    if err != nil {
        log.Fatal("ListenAndServe: ", err)
    }
}
```
<a name="L5E6a"></a>
## 第一层级：
<a name="Rwx8Q"></a>
### 流程结构：
![](https://cdn.nlark.com/yuque/__flowchart/bc9d27cda1c33e911d12157204bbe049.svg#lake_card_v2=eyJjb2RlIjoic3Q9PnN0YXJ0OiDkuIDkuKrmnIDnroAgaHR0cCDlrp7ot7VcbmZ1bmM9Pm9wZXJhdGlvbjogbWFpbigpXG5zdWJmdW5jPT5wYXJhbGxlbDogaHR0cC5IYW5kbGVGdW5jKClcbmFzc2lnbm1lbnQ9Pm9wZXJhdGlvbjogaHR0cC5MaXN0ZW5BbmRTZXJ2ZSgpXG5jb25kPT5jb25kaXRpb246IGVyciAhPSBuaWwgP1xuc3Vic3ViZnVuYz0-c3Vicm91dGluZTogc2F5aGVsbG9OYW1lKClcbmxvZz0-b3BlcmF0aW9uOiBsb2cuRmF0YWwoKVxuZT0-ZW5kOiBlbmRcblxuc3QtPmZ1bmMtPnN1YmZ1bmNcbnN1YmZ1bmMocGF0aDEsIGJvdHRvbSktPmFzc2lnbm1lbnQtPmNvbmRcbnN1YmZ1bmMocGF0aDIsIHJpZ2h0KS0-c3Vic3ViZnVuY1xuY29uZCh5ZXMpLT5sb2dcbmNvbmQobm8pLT5lXG5sb2ctPmUiLCJ0eXBlIjoiZmxvd2NoYXJ0IiwiaWQiOiJ4b3UxTyIsInVybCI6Imh0dHBzOi8vY2RuLm5sYXJrLmNvbS95dXF1ZS9fX2Zsb3djaGFydC9iYzlkMjdjZGExYzMzZTkxMWQxMjE1NzIwNGJiZTA0OS5zdmciLCJoZWlnaHQiOjIxMSwiY2FyZCI6ImRpYWdyYW0ifQ==)其中 `sayhelloName()` 中使用了 http 的两个 `type`  :  `http.ResponseWriter`  & `http.Request` 
<a name="87VpM"></a>
### 内容结构：
其中 无框的为方法或者函数
![](https://cdn.nlark.com/yuque/__puml/10c959429436e1e65e5b66252a60443c.svg#lake_card_v2=eyJjb2RlIjoiQHN0YXJ0bWluZG1hcFxuKiBodHRwIOacgOeugOWunui3tVxuKiogUmVzcG9uc2VXcml0ZXJcbioqIFJlcXVlc3RcbioqKl8gRm9ybSgpXG4qKiogVVJMXG4qKioqIFBhdGhcbioqKiogU2NoZW1lXG4qKl8gSGFuZGxlRnVuYygpXG4qKl8gTGlzdGVuQW5kU2VydmUoKVxuQGVuZG1pbmRtYXAiLCJ0eXBlIjoicHVtbCIsImlkIjoiU3h4TEciLCJ1cmwiOiJodHRwczovL2Nkbi5ubGFyay5jb20veXVxdWUvX19wdW1sLzEwYzk1OTQyOTQzNmUxZTY1ZTViNjYyNTJhNjA0NDNjLnN2ZyIsImNhcmQiOiJkaWFncmFtIn0=)
---


<a name="fBzlT"></a>
## 第二层级：
<a name="7f2rW"></a>
### http.ResponseWriter
这是一个接口，实现这个接口需要实现三个方法：

![](https://cdn.nlark.com/yuque/__puml/bd76e8f6f04535a5c6a150d68e3cadea.svg#lake_card_v2=eyJjb2RlIjoiQHN0YXJ0bWluZG1hcFxuKiBodHRwLlJlc3BvbnNlV3JpdGVyXG4qKl8gSGVhZGVyKClcbioqXyBXcml0ZSgpXG4qKl8gV3JpdGVIZWFkZXIoKVxuQGVuZG1pbmRtYXAiLCJ0eXBlIjoicHVtbCIsImlkIjoiWWdWTlUiLCJ1cmwiOiJodHRwczovL2Nkbi5ubGFyay5jb20veXVxdWUvX19wdW1sL2JkNzZlOGY2ZjA0NTM1YTVjNmExNTBkNjhlM2NhZGVhLnN2ZyIsImNhcmQiOiJkaWFncmFtIn0=)<a name="P6U2m"></a>
### http.Request
这也是一个接口，不过内容稍微多一点。

![](https://cdn.nlark.com/yuque/__puml/052d60b7f2b368d506e1c0c67d37de51.svg#lake_card_v2=eyJjb2RlIjoiQHN0YXJ0bWluZG1hcFxuKiBodHRwLlJlcXVlc3RcbioqXyBNZXRob2Qo55So5LqO5oyH5a6aIEhUVFAg5pa55rOVKVxuKipfIFVSTCjnlKjkuo7mjIflrpogVVJMIOaIluiAhSBVUkkpXG4qKl8gUHJvdG8o55So5LqO5oyH5a6aIGh0dHAg55qE54mI5pysKVxuKipfIFByb3RvTWFqb3JcbioqXyBQcm90b01pbm9yXG4qKl8gSGVhZGVyKOeUqOS6juWMheWQqyBodHRwIOWktOS_oeaBrylcbkBlbmRtaW5kbWFwIiwidHlwZSI6InB1bWwiLCJpZCI6InpjY0cwIiwidXJsIjoiaHR0cHM6Ly9jZG4ubmxhcmsuY29tL3l1cXVlL19fcHVtbC8wNTJkNjBiN2YyYjM2OGQ1MDZlMWMwYzY3ZDM3ZGU1MS5zdmciLCJjYXJkIjoiZGlhZ3JhbSJ9)

