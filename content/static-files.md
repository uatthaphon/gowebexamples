+++
weight = 6
title = "แอสเสทและไฟล์"
description = "ตัวอย่างต่อไปนี้จะแสดงให้เห็นถึงการใช้งานสแตติกไฟล์ อย่างพวก CSS หรือ JS จากไดเรคทอรี่ที่ถูกเก็บไฟล์เหล่านี้ไว้ด้วย http.FileServer"
+++

# แอสเสทและไฟล์

ตัวอย่างต่อไปนี้จะแสดงให้เห็นถึงการใช้งานสแตติกไฟล์ อย่างพวก CSS หรือ JS จากไดเรคทอรี่ที่ถูกเก็บไฟล์เหล่านี้ไว้

{{< edison >}}

{{< highlight go >}}
// static-files.go
package main

import "net/http"

func main() {
	fs := http.FileServer(http.Dir("assets/"))
	http.Handle("/static/", http.StripPrefix("/static/", fs))

	http.ListenAndServe(":8080", nil)
}
{{< / highlight >}}
{{< highlight console >}}
$ tree assets/
assets/
└── css
    └── styles.css
{{< / highlight >}}
{{< highlight console >}}
$ go run static-files.go

$ curl -s http://localhost:8080/static/css/styles.css
body {
    background-color: black;
}
{{< / highlight >}}
