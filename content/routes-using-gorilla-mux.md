+++
weight = 3
title = "เราติ้ง (โดยใช้ gorilla/mux)"
description = "ตัวอย่างการใช้งานแพ็กเกจ `gorilla/mux` เพื่อสร้างเร้า ตั้งชื่อตัวแปร การจัดการกับ GET/POST เมธอด และการจัดการโดเมน"
+++

# เราติ้ง (โดยใช้ gorilla/mux)

## เกี่ยวกับบทเรียน
แพ็กเกจ `net/http` ในภาษา Go มีฟังก์ชันเพื่ออำนวยความสะดวกสำหรับการสร้างและใช้งาน HTTP โพรโทคอล
แต่มีเรื่องหนึ่งที่ทำได้ไม่ดีนัก คือถ้าหากเราต้องเจอกับรีเควสต์ที่ซับซ้อน
อย่างเช่นในกรณีที่เราต้องการแยกตัวแปรจากรีเควสต์ในรูปแบบ url ตัวเดียวโดดๆ
อย่างไรก็แล้วแต่เราก็มีแพ็กเกจที่เป็นที่นิยมซึ่งถูกพูดถึงกันเป็นอย่างมาใน
Go คอมมูนิตี้ว่าเป็นแพ็กเกจที่เขียนออกมาได้ดีและโค้ดมีคุณภาพมาก
ในบทนี้เราจะมาเรียนรู้การใช้แพ็กเกจ `gorilla/mux` เพื่อสร้างเร้า ตั้งชื่อตัวแปร จัดการกับ GET/POST เมธอด และการจัดการโดเมน

{{< edison >}}

## การติดตั้งแพ็กเกจ `gorilla/mux`
`gorilla/mux` คือ แพ็กเกจที่สร้างมาเพื่อใช้งานร่วมกับ HTTP เร้าเตอร์ของ Go ซึ่งมีฟีเจอร์มากมายที่จะช่วยเพิ่มความรวดเร็วและประสิทธิภาพในการเขียนเว็บแอปพลิเคชัน
แพ็กเกจนี้สร้างขึ้นโดยมีการรับตัวแปร `func (w http.ResponseWriter, r *http.Request)` เช่นเดียวกับของ Go ซึ่งนั่นทำให้แพ็กเกจนี้สามารถใช้งานได้กับ HTTP ไลบรารีอย่าง middleware และ application ซึ่งถูกสร้างขึ้นมาด้วยแพ็กเกจ HTTP พื้นฐานของ Go ที่มีอยู่แล้ว เราใช่คำสั่ง `go get` เพื่อติดตั้งแพ็กเกจจาก Github

{{< highlight sh >}}
go get -u github.com/gorilla/mux
{{< / highlight >}}

## สร้างเร้าใหม่
ขั้นแรก สร้างเร้าตัวใหม่เพื่อรับรีเควสต์
เร้าตัวนี้จะเป็นเร้าหลักสำหรับเว็บแอปพลิเคชันของคุณ
แล้วหลังจากนั้นเราจะทำการส่งพรารามิเตอร์สู่เซิร์ฟเวอร์กัน
มันจะทำการรับค่าการเชื่อมต่อ HTTP ทั้งหมดและส่งต่อไปให้ handlers ที่คุณจะทำการสร้างขึ้นเพื่อจักการกับรีเควสต์ที่เข้ามา
เราสามารถสร้างเร้าตัวใหม่ได้ดังนี้

{{< highlight go >}}
r := mux.NewRouter()
{{< / highlight >}}

## ระบุ Handler ที่จะใช้งาน
หลังจากที่คุณสร้างเร้าตัวใหม่เป็ฯที่เรียบร้อยแล้วคุณต้องทำการระบุ handler ที่จะเอาไว้ใช้จัดการกับรีเควสต์นั้นที่เข้ามาด้วย
ข้อแตกต่างเพียงอย่างเดียวคือ แทนที่เราจะทำการเรียก `http.HandleFunc(...)` เหมือนกับการใช้งานสแตนดาร์ดไลบรารี เราต้องเปลี่ยนไปเรียก `HandleFunc` แบบนี้แทน `r.HandleFunc(...)`

## URL พารามิเตอร์
ข้อได้เปรียบหลักของ `gorilla/mux` เร้าเตอร์คือความสามารถในการดึงเอาส่วนต่างจากรีเควสต์ URL จากตัวอย่าง สมมุติว่านี่คือ URL จากแอปพลิเคชั่นของคุณ

{{< highlight sh >}}
/books/go-programming-blueprint/page/10
{{< / highlight >}}

จากที่เห็น ​URL จะมีสองส่วนหลักๆ คือ

1. ชื่อหนังสือ (Books title) (go-programming-blueprint)
2. หน้า (Page) (10)

ใสการที่เราจะทำการเทียบ URL ที่ได้เห็นไป เราจำเป็นที่จะต้องการทำการแทนที่ค่าแบบจากรูปแบบ URL ที่เราได้กำหนดเอาไว้แบบนี้

{{< highlight go >}}
r.HandleFunc("/books/{title}/page/{page}", func(w http.ResponseWriter, r *http.Request) {
	// get the book
	// navigate to the page
})
{{< / highlight >}}

จากนั้นส่ิงสุดท้ายคือการที่เราต้องรับค่าตัวแปรและแยกออกเป็นเซกเมนต์
`gorilla/mux` แพ็กเกจมาพร้อมกับฟังก์ชัน `mux.Vars(r)` ซึ่งทำการรับค่าจาก `http.Request` ในรูปแบบของตัวแปร และส่งกลับมาและแมพมาให้เรียบร้อย ซึ่งถูกแยกออกมาเป็นส่วนอย่างชัดเจน

{{< highlight go >}}
func(w http.ResponseWriter, r *http.Request) {
	vars := mux.Vars(r)
	vars["title"] // the book title slug
	vars["page"] // the page
}
{{< / highlight >}}

## ติดตั้ง HTTP server ที่เราเพิ่มสร้างไป
คงจะสงสัยใช่ไหมครับว่า `nil` ใน `http.ListenAndServe(":80", nil)` คืออะไร
มันก็คือช่องรับค่าตัวแปรสำหรับ เร้าหลักของ HTTP เซิร์ฟเวอร์
ซึ่งโดยปรกติเราจะส่งค่า `nil` เข้าไปซึ่งนั่นจะหมายความว่าให้ใช้เร้าเตอร์พื้นฐานซึ่งก็คือแพ็กเกจ `net/http`
แต่เพิ่มให้สามารถใช้เร้าที่เราพึ่งทำการสร้างได้ เราจำเป็นต้องแทนที่ `nil` ตัวแปรซึ่งเป็นเร้าของเรา `r` เข้าไปแทน

{{< highlight go >}}
http.ListenAndServe(":80", r)
{{< / highlight >}}

## โค้ด (สำหรับทดสอบ copy/paste)
นี่เป็นตัวโค้ดที่เสร็จสมบูรณ์ ซึ่งคุณสามารถนำไปใช้ทดสอบได้ เป็นโค้ดที่คุณได้เรียนรู้มาทั้งหมดในบทเรียนบทนี้

{{< highlight go >}}
package main

import (
	"fmt"
	"net/http"

	"github.com/gorilla/mux"
)

func main() {
	r := mux.NewRouter()
	r.HandleFunc("/books/{title}/page/{page}", func(w http.ResponseWriter, r *http.Request) {
		vars := mux.Vars(r)
		title := vars["title"]
		page := vars["page"]

		fmt.Fprintf(w, "You've requested the book: %s on page %s\n", title, page)
	})

	http.ListenAndServe(":80", r)
}
{{< / highlight >}}

## ฟีเจอร์ของ `gorilla/mux`

### เมธอด (Methods)
การระบุและรับรีเควสต์สำหรับการจัดการกับ  HTTP เมธอด

{{< highlight go >}}
r.HandleFunc("/books/{title}", CreateBook).Methods("POST")
r.HandleFunc("/books/{title}", ReadBook).Methods("GET")
r.HandleFunc("/books/{title}", UpdateBook).Methods("PUT")
r.HandleFunc("/books/{title}", DeleteBook).Methods("DELETE")
{{< / highlight >}}

### ชื่อโฮส และ ซับโดเมน (Hostnames & Subdomains)
การระบุและรับรีเควสต์สำหรับการจัดการกับ ชื่อโฮส หรือ ซับโดเมน

{{< highlight go >}}
r.HandleFunc("/books/{title}", BookHandler).Host("www.mybookstore.com")
{{< / highlight >}}

### สกีมา (Schemes)
การระบุและรับรีเควสต์สำหรับการจัดการกับ http/https.
Restrict the request handler to http/https.
{{< highlight go >}}
r.HandleFunc("/secure", SecureHandler).Schemes("https")
r.HandleFunc("/insecure", InsecureHandler).Schemes("http")
{{< / highlight >}}

### ชื่อต้นของพาท และเร้าเตอร์รอง (Path Prefixes & Subrouters)
การระบุและรับรีเควสต์สำหรับการจัดการกับ ชื่อต้นของพาท
Restrict the request handler to specific path prefixes.
{{< highlight go >}}
bookrouter := r.PathPrefix("/books").Subrouter()
bookrouter.HandleFunc("/", AllBooks)
bookrouter.HandleFunc("/{title}", GetBook)
{{< / highlight >}}
