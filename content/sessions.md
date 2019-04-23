+++
weight = 9
title = "Sessions"
description = "ตัวอย่างต่อไปนี้จะแสดงวิธีการเก็บข้อมูลใน เซสชั่น คุกกี้ ด้วยแพ็กเกจ gorilla/sessions ในภาษา Go"
+++

# เซสชั่น

ตัวอย่างต่อไปนี้จะแสดงวิธีการเก็บข้อมูลใน เซสชั่น คุกกี้ ด้วยแพ็กเกจ <a target="_blank" href="https://github.com/gorilla/sessions">gorilla/sessions</a> ซึ่งได้รับความนิยมเป็นอย่างมากในภาษา Go

คุกกี้ คือข้อมูลก้อนเล็กๆ ที่ถูกเก็บเอาไว้ในเบราเซอร์ของผู้ใช้งาน และส่งกลับมายังเซิร์ฟเวอร์ของเราทุกครั้งที่ถูกเรียกใช้งาน ในคุกกี้เราสามารถที่จะเก็บข้อมูลต่างๆ ได้อย่างเช่น ตรวจสอบว่าผู้ใช้งานเข้าสู่ระบบแล้วหรือไม่ หรือเพื่อดูว่าเขาคือใคร (ในระบบของเรา)

ในตัวอย่างต่อไปนี้เราจะอนุญาติให้ผู้ใช้งานที่ลงทะเบียนในระบบเท่านั้นที่จะสามารถเห็นข้อความลับของเราได้จากหน้า `/secret` ของเรา ในการที่จะสามารถเข้าใช้งานได้อย่างแรกผู้ใช้งานต้องเข้าไปที่หน้า `/login` เพื่อเข้าสู่ระบบและรับ เซสชั่น คุกกี้ เพื่อเข้าสู่ระบบ และเราสามารถเข้าหน้า `/logout` เพื่อยกเลิกการเข้าสู่ระบบได้ และนั่นจะทำให้ผู้ใช้งานไม่เห็น ข้อความลับของเราอีกจนกว่าเขาจะเข้าสู่ระบบอีกครั้ง

{{< edison >}}

{{< highlight go >}}
// sessions.go
package main

import (
	"fmt"
	"net/http"

	"github.com/gorilla/sessions"
)

var (
	// key must be 16, 24 or 32 bytes long (AES-128, AES-192 or AES-256)
	key = []byte("super-secret-key")
	store = sessions.NewCookieStore(key)
)

func secret(w http.ResponseWriter, r *http.Request) {
	session, _ := store.Get(r, "cookie-name")

	// Check if user is authenticated
	if auth, ok := session.Values["authenticated"].(bool); !ok || !auth {
		http.Error(w, "Forbidden", http.StatusForbidden)
		return
	}

	// Print secret message
	fmt.Fprintln(w, "The cake is a lie!")
}

func login(w http.ResponseWriter, r *http.Request) {
	session, _ := store.Get(r, "cookie-name")

	// Authentication goes here
	// ...

	// Set user as authenticated
	session.Values["authenticated"] = true
	session.Save(r, w)
}

func logout(w http.ResponseWriter, r *http.Request) {
	session, _ := store.Get(r, "cookie-name")

	// Revoke users authentication
	session.Values["authenticated"] = false
	session.Save(r, w)
}

func main() {
	http.HandleFunc("/secret", secret)
	http.HandleFunc("/login", login)
	http.HandleFunc("/logout", logout)

	http.ListenAndServe(":8080", nil)
}
{{< / highlight >}}
{{< highlight console >}}
$ go run sessions.go

$ curl -s http://localhost:8080/secret
Forbidden

$ curl -s -I http://localhost:8080/login
Set-Cookie: cookie-name=MTQ4NzE5Mz...

$ curl -s --cookie "cookie-name=MTQ4NzE5Mz..." http://localhost:8080/secret
The cake is a lie!
{{< / highlight >}}


