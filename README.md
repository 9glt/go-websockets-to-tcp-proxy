# go-websockets-to-tcp-proxy
websockets to tcp proxy ( for nats.io old way )


1. start nats.io pubsub server 
2. start this go-websockets-to-tcp-proxy with:  ```go run main.go```
3. client core for test

```go
package main

import (
	"bytes"
	"fmt"
	mqs "github.com/9glt/go-nats-cli-ws"
	"time"
)

func main() {
	conn, err := mqs.New("ws://127.0.0.1:8080/mq?token=secret", "nats://127.0.0.1:4222")
	if err != nil {
		panic(err)
	}

	go func() {
		conn.Subscribe("topic", func(msg *mqs.Msg) {
			fmt.Printf(" %d %s\n", len(msg.Data), msg.Data)
			fmt.Println("===========================")
			fmt.Printf(" %d\n", len(msg.Data))
		})
	}()

	go func() {
		body := bytes.Repeat([]byte("A"), 32000)
		for {
			conn.Publish("topic", body)
			time.Sleep(1 * time.Second)
		}

	}()

	m := make(chan struct{})
	<-m
	// ...
}

}
```