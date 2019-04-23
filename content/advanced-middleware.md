+++
weight = 8
title = "มิดเดิลแวร์ - ขั้นสูง"
description = "ตัวอย่างต่อไปนี้จะแสดงวิธีการสร้าง มิดเดิลแวร์ขั้นสูง ในภาษา Go"
+++

# มิดเดิลแวร์ - ขั้นสูง

ตัวอย่างต่อไปนี้จะแสดงวิธีการสร้าง มิดเดิลแวร์ขั้นสูง ในภาษา Go

ตัวมิดเดิลแวร์จะทำการรับ `http.HandlerFunc` ในรูปแบบของตัวแปร และทำการครอบมันไว้แล้วจึงส่งค่ากลับไปเป็น `http.HandlerFunc` ตัวใหม่ให้กับเซิร์ฟเวอร์เพื่อรอการเรียกใช้งาน

นี่คือคำจัดกัดความใหม่ของ `Middleware` ซึ่งจะทำให้ง่ายต่อการผูก (chain) มิดเดอแวร์หลายๆ ตัวเข้าไว้ด้วยกัน ความคิดนี้ได้รับแรงบันดาลใจมาจาก Mat Ryers ซึ่งได้เคยพูดไว้ในเรื่องเกี่ยวกับการสร้าง APIs ถ้าหากต้องการรู้รายละเอียดเพ่ิมเติม สามารถเข้าไปดูได้ที่ <a target="_blank" href="https://medium.com/@matryer/writing-middleware-in-golang-and-how-go-makes-it-so-much-fun-4375c1246e81">here</a>.

{{< edison >}}

<br />
ตัวอย่างโค้ต่อไปนี่ใช้เพื่อสร้าง มิดเดิลเวร์ตัวใหม่ และในตัวอย่างตัวเต็มเราได้ใช้ตัวอย่างโค้ดสำเร็จรูปในการสร้างเพื่อความรวดเร็ว

{{< highlight go >}}
func createNewMiddleware() Middleware {

	// Create a new Middleware
	middleware := func(next http.HandlerFunc) http.HandlerFunc {

		// Define the http.HandlerFunc which is called by the server eventually
		handler := func(w http.ResponseWriter, r *http.Request) {

			// ... do middleware things

			// Call the next middleware/handler in chain
			next(w, r)
		}

		// Return newly created handler
		return handler
	}

	// Return newly created middleware
	return middleware
}
{{< / highlight >}}
<br />

นี่คือตัวอย่างตัวเต็ม:
{{< highlight go >}}
// advanced-middleware.go
package main

import (
	"fmt"
	"log"
	"net/http"
	"time"
)

type Middleware func(http.HandlerFunc) http.HandlerFunc

// Logging logs all requests with its path and the time it took to process
func Logging() Middleware {

	// Create a new Middleware
	return func(f http.HandlerFunc) http.HandlerFunc {

		// Define the http.HandlerFunc
		return func(w http.ResponseWriter, r *http.Request) {

			// Do middleware things
			start := time.Now()
			defer func() { log.Println(r.URL.Path, time.Since(start)) }()

			// Call the next middleware/handler in chain
			f(w, r)
		}
	}
}

// Method ensures that url can only be requested with a specific method, else returns a 400 Bad Request
func Method(m string) Middleware {

	// Create a new Middleware
	return func(f http.HandlerFunc) http.HandlerFunc {

		// Define the http.HandlerFunc
		return func(w http.ResponseWriter, r *http.Request) {

			// Do middleware things
			if r.Method != m {
				http.Error(w, http.StatusText(http.StatusBadRequest), http.StatusBadRequest)
				return
			}

			// Call the next middleware/handler in chain
			f(w, r)
		}
	}
}

// Chain applies middlewares to a http.HandlerFunc
func Chain(f http.HandlerFunc, middlewares ...Middleware) http.HandlerFunc {
	for _, m := range middlewares {
		f = m(f)
	}
	return f
}

func Hello(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello world")
}

func main() {
	http.HandleFunc("/", Chain(Hello, Method("GET"), Logging()))
	http.ListenAndServe(":8080", nil)
}
{{< / highlight >}}
{{< highlight console >}}
$ go run advanced-middleware.go
2017/02/11 00:34:53 / 0s

$ curl -s http://localhost:8080/
hello world

$ curl -s -XPOST http://localhost:8080/
Bad Request
{{< / highlight >}}


