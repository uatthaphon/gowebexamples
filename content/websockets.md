+++
weight = 11
title = "เว็บซ็อกเก็ต"
description = "ตัวอย่างต่อไปนี้จะแสดงวิธีการใช้งานเว็บซ็อกเก็ตในภาษา Go เราจะทำการสร้างเซิร์ฟเวอร์อย่างง่ายที่ทำการแสดงค่าออกมาหลังจากรับค่าที่เรารับมาทุกอย่าง"
+++

# เว็บซ็อกเก็ต

ตัวอย่างต่อไปนี้จะแสดงวิธีการใช้งานเว็บซ็อกเก็ตในภาษา Go เราจะทำการสร้างเซิร์ฟเวอร์อย่างง่ายที่ทำการแสดงค่าออกมาหลังจากรับค่าที่เรารับมาทุกอย่าง
เรามาเริ่มโดยการใช้ `go get` เพื่อใช้งาน <a target="_blank" href="https://github.com/gorilla/websocket">gorilla/websocket</a> ซึ่งเป็นไลบรารีอีกตัวที่ได้รับความนิยมกันเลยดีกว่า

`$ go get github.com/gorilla/websocket`

จากตรงนี้ ทุกๆ แอปพลิเคชั่นที่เราสร้างขึ้นก็จะสามารถเรียกใช้งานเจ้าไลบรารีตัวนี้ได้

{{< edison >}}

{{< highlight go >}}
// websockets.go
package main

import (
	"fmt"
	"net/http"

	"github.com/gorilla/websocket"
)

var upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

func main() {
	http.HandleFunc("/echo", func(w http.ResponseWriter, r *http.Request) {
		conn, _ := upgrader.Upgrade(w, r, nil) // error ignored for sake of simplicity

		for {
			// Read message from browser
			msgType, msg, err := conn.ReadMessage()
			if err != nil {
				return
			}

			// Print the message to the console
			fmt.Printf("%s sent: %s\n", conn.RemoteAddr(), string(msg))

			// Write message back to browser
			if err = conn.WriteMessage(msgType, msg); err != nil {
				return
			}
		}
	})

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		http.ServeFile(w, r, "websockets.html")
	})

	http.ListenAndServe(":8080", nil)
}
{{< / highlight >}}
{{< highlight html >}}
<!-- websockets.html -->
<input id="input" type="text" />
<button onclick="send()">Send</button>
<pre id="output"></pre>
<script>
	var input = document.getElementById("input");
	var output = document.getElementById("output");
	var socket = new WebSocket("ws://localhost:8080/echo");

	socket.onopen = function () {
		output.innerHTML += "Status: Connected\n";
	};

	socket.onmessage = function (e) {
		output.innerHTML += "Server: " + e.data + "\n";
	};

	function send() {
		socket.send(input.value);
		input.value = "";
	}
</script>
{{< / highlight >}}
{{< highlight console >}}
$ go run websockets.go
[127.0.0.1]:53403 sent: Hello Go Web Examples, you're doing great!
{{< / highlight >}}
<div class="demo">
	<input type="text">
	<button>Send</button>
	<pre>Status: Connected
Server: Hello Go Web Examples, you're doing great!</pre>
</div>
