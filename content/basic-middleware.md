+++
weight = 7
title = "มิดเดิลแวร์ - ขั้นพื้นฐาน"
description = "ตัวอย่างต่อไปนี้จะแสดงวิธีการสร้าง logging จากมิดเดิลแวร์ในภาษา Go"
+++

# Middleware (Basic)

ตัวอย่างต่อไปนี้จะแสดงวิธีการสร้าง logging จากมิดเดิลแวร์ในภาษา Go

มิดเดิลแวร์จะทำการรับค่า `http.HandlerFunc` เป็นตัวแปรตัวหนึ่ง ซึ่งจะทำการครอบตัวฟังก์ชันนั้นเอาไว้และจะส่งค่า `http.HandlerFunc` ตัวใหม่กลับไปจากเซิร์ฟเวอร์

{{< edison >}}

{{< highlight go >}}
// basic-middleware.go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func logging(f http.HandlerFunc) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		log.Println(r.URL.Path)
		f(w, r)
	}
}

func foo(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "foo")
}

func bar(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "bar")
}

func main() {
	http.HandleFunc("/foo", logging(foo))
	http.HandleFunc("/bar", logging(bar))

	http.ListenAndServe(":8080", nil)
}
{{< / highlight >}}
{{< highlight console >}}
$ go run basic-middleware.go
2017/02/10 23:59:34 /foo
2017/02/10 23:59:35 /bar
2017/02/10 23:59:36 /foo?bar

$ curl -s http://localhost:8080/foo
$ curl -s http://localhost:8080/bar
$ curl -s http://localhost:8080/foo?bar
{{< / highlight >}}
