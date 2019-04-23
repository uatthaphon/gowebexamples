+++
weight = 10
title = "เข้ารหัสแบบแฮช"
description = "ตัวอย่งต่อไปนี้เราจะทำการโชว์วิธีการเข้ารหัสพาสเวิร์ดด้วยการใช้ bcrypt ในภาษา Go"
+++

# เข้ารหัสแบบแฮช (bcrypt)

ตัวอย่งต่อไปนี้เราจะทำการโชว์วิธีการเข้ารหัสพาสเวิร์ดด้วยการใช้ bcrypt
เรามาเริ่มโดยการใช้ `go get` เพื่อใช้งาน golang bcrypt ไลบรารี

`$ go get golang.org/x/crypto/bcrypt`

จากตรงนี้ ทุกๆ แอปพลิเคชั่นที่เราสร้างขึ้นก็จะสามารถเรียกใช้งานเจ้าไลบรารีตัวนี้ได้

{{< edison >}}

{{< highlight go >}}
// passwords.go
package main

import (
	"fmt"

	"golang.org/x/crypto/bcrypt"
)

func HashPassword(password string) (string, error) {
	bytes, err := bcrypt.GenerateFromPassword([]byte(password), 14)
	return string(bytes), err
}

func CheckPasswordHash(password, hash string) bool {
	err := bcrypt.CompareHashAndPassword([]byte(hash), []byte(password))
	return err == nil
}

func main() {
	password := "secret"
	hash, _ := HashPassword(password) // ignore error for the sake of simplicity

	fmt.Println("Password:", password)
	fmt.Println("Hash:    ", hash)

	match := CheckPasswordHash(password, hash)
	fmt.Println("Match:   ", match)
}

{{< / highlight >}}
{{< highlight console >}}
$ go run passwords.go
Password: secret
Hash:     $2a$14$ajq8Q7fbtFRQvXpdCq7Jcuy.Rx1h/L4J60Otx.gyNLbAYctGMJ9tK
Match:    true
{{< / highlight >}}
