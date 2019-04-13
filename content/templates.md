+++
weight = 4
title = "เทมเพลต"
description = "ตัวอย่างการใช้งานแพคเกจ `gorilla/mux` เพื่อสร้างเร้า ตั้งชื่อตัวแปร การจัดการกับ GET/POST เมธอด และการจัดการโดเมน"
+++

# เทมเพลต

## เกี่ยวกับบทเรียน
แพคเกจ `html/template` ที่มีมาให้เราได้ใช้เป็นเทมเพลตสำหรับภาษา HTML ที่เพรียบร้อม
สำหรับใช้ในส่วนของการแสดงผลข้อมูลสำหรับเว็บแอปพลิเคชั่นในเว็บเบราว์เซอร์ของผู้ใช้งาน
ข้อดีอย่างหนึ่งของ เทมเพลตในภาษา Go ก็เห็นจะเป็นเรื่องของการ escape ข้อมูลที่ได้รับมาโดยอัตโนมัติ
ดังนั้นเราก็หายห่วงในเรื่องการโจมตีแบบ XSS ไปได้เลย เพราะภาษา Go จะทำการวิเคราะห์ HTML เพมเพลต
และจะ escape อินพุตทั้งหมดที่รับมาก่อนที่จะทำการแสดงผลออกไปยังหน้าเบราว์เซอร์

{{< edison >}}

## เทมเพลตตัวแรก
การใช้งานหรือเขียนเทมเพลตในภาษา Go นั้นไม่มีอะไรซับซ้อนเลย จากตัวอย่างต่อไปนี้จะแสดงให้เห็นการส้าง  TODO ลิส โดยทำการเขียนออกมาเป็น ul ในภาษา HTML
ในขณะที่ทำการประมวลผลเทมเพลตนั้น ข้อมูลที่ถูกส่งเข้ามาสามารถที่จะเป็นข้อมูลชนิดใดก็ได้ที่มาจากโครงสร้งข้อมูลของภาษา Go มันสามารถรับได้ทั้งแบบตัวอักษรและตัวเลข หรือว่าจะเป็นโครงสร้างข้อมูลแบบซ้อนทับกันเหมือนดังตัวอย่างด้านล่างนี้
การเข้าถึงข้อมูลชั้นแรกสุดที่ได้รับมาในเทมเพลตเราสามารถเข้าถึงได้โดย `{{.}}`
จุดข้างในวงเล็บปีกกา นั่นเรียกว่า ไปป์ไลน์ (pipeline) และเป็นองค์ประกอบรากของข้อมูลหรือข้อมูลลำดับแรก

{{< highlight go >}}
data := TodoPageData{
	PageTitle: "My TODO list",
	Todos: []Todo{
		{Title: "Task 1", Done: false},
		{Title: "Task 2", Done: true},
		{Title: "Task 3", Done: true},
	},
}
{{< / highlight >}}
{{< highlight html >}}
<h1>{{.PageTitle}}<h1>
<ul>
    {{range .Todos}}
        {{if .Done}}
            <li class="done">{{.Title}}</li>
        {{else}}
            <li>{{.Title}}</li>
        {{end}}
    {{end}}
</ul>
{{< / highlight >}}

## ไวยากรณ์ สำรหรับควบคุมการทำงาน (Control Structure)
เทมเพลตในภาษา GO ประกอบไปด้วยเซ็ทคำสั่งอันหลากหลายเพื่อควบคุมการทำงานต่างๆ สำหรับการประมวลผล HTML ของคุณ ตัวอย่างต่อไปนี้จะแสดงให้คุณเห็นถึงภาพรวมและคำสั้งต่างๆที่มีคนที่ใช้กันมากที่สุด
หากต้องการทราบเพ่ิมเติมเกี่ยวกับรายชื่อคำสั่งต่างๆ ที่สามารถใช้งานได้ ขอให้เข้าไปที่ <a target="_blank" href="https://golang.org/pkg/text/template/#hdr-Actions">text/template</a>

Control Structure | Definition
---|---
`{{/* a comment */}}` | การเขียนคอมเมนต์
`{{.}}` | แสดงข้อมูลทั้งหมด
`{{.Title}}` | แสดงข้อมูล "Title" จากข้อมูลที่ได้รับมา
`{{if .Done}} {{else}} {{end}}` | การเขียนคำสั่ง if
`{{range .Todos}} {{.}} {{end}}` | ลูป "Todos" และแสดงผลข้อมูลเมื่อทำการลูปด้วยไวยากรณ์ `{{.}}`
`{{block "content" .}} {{end}}` | กำหนดบล็อก (block) โดยใช้ชื่อว่า "content"

## ใช้เทมเพลตในรูปแบบของไฟล์แยก
เทมเพลตสามารที่จะใช้งานได้จากทั้ง การเขียนตรงๆหรือจากไฟล์ที่มีอยู่ในเครื่องของเรา
โดยปรกติแล้วเรามักที่จะใช้เทมเพลตโดยการสร้างไฟล์แยกและเก็บไว้ในเครื่องของเราเสียมากกว่า
ตัวอย่างด้านล่างจะแสดงให้เห็นวิธีการใช้งาน ตัวอย่างที่เห็นจะมีเทมเพลตในไดเรคทอรี่เดียวกันกับตัวไฟล์โปรแกรมของ Go ซึ่งถูกสร้างชึ้นมาในชื่อ `layout.html`

{{< highlight go >}}
tmpl, err := template.ParseFiles("layout.html")
// or
tmpl := template.Must(template.ParseFiles("layout.html"))
{{< / highlight >}}

## ประมวลผมเทมเพลตใน Request Handler
เมื่อเรารับเทมเพลตมาจากไฟล์ที่ถูกสร้างขึ้นมาเรียบร้อยแล้ว เราก็สามารถนำมันมาใช้ใน request handler ได้เลย
ฟังก์ชั่น `Execute` รับค่า `io.Writer` เพื่อใช้เขียนข้อมูลในเทมเพลตและ `interface{}` เพื่อส่งข้อมูลกลับไปให้เทมเพลตอีกทีหนึ่ง
เมื่อฟังก์ชั่นที่การเรียก `http.ResponseWriter` จากนั้น Content-Type ใน header จะถูกจัดการและเขียนข้อมูลโดยอัตโนมัติและส่งกลับมาเป็น `Content-Type: text/html; charset=utf-8`

{{< highlight go >}}
func(w http.ResponseWriter, r *http.Request) {
	tmpl.Execute(w, "data goes here")
}
{{< / highlight >}}

## โค้ด (สำหรับทดสอบ copy/paste)
นี่เป็นตัวโค้ดที่เสร็จสมบูรณ์ ซึ่งคุณสามารถนำไปใช้ทดสอบได้ เป็นโค้ดที่คุณได้เรียนรู้มาทั้งหมดในบทเรียนบทนี้
{{< highlight go >}}
package main

import (
	"html/template"
	"net/http"
)

type Todo struct {
	Title string
	Done  bool
}

type TodoPageData struct {
	PageTitle string
	Todos     []Todo
}

func main() {
	tmpl := template.Must(template.ParseFiles("layout.html"))

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		data := TodoPageData{
			PageTitle: "My TODO list",
			Todos: []Todo{
				{Title: "Task 1", Done: false},
				{Title: "Task 2", Done: true},
				{Title: "Task 3", Done: true},
			},
		}
		tmpl.Execute(w, data)
	})

	http.ListenAndServe(":80", nil)
}
{{< / highlight >}}
{{< highlight html >}}
<h1>{{.PageTitle}}<h1>
<ul>
    {{range .Todos}}
        {{if .Done}}
            <li class="done">{{.Title}}</li>
        {{else}}
            <li>{{.Title}}</li>
        {{end}}
    {{end}}
</ul>
{{< / highlight >}}
