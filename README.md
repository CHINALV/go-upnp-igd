# go-upnp-igd

[![GoDoc](https://godoc.org/github.com/emersion/go-upnp-igd?status.svg)](https://godoc.org/github.com/emersion/go-upnp-igd)

Minimal Go UPnP InternetGatewayDevice library. Based on
[Syncthing's library](https://github.com/syncthing/syncthing/tree/e8ba6d477182a73ba417d0d69999a104d04cd912/lib/upnp).

## Usage

```go
package main

import (
	"github.com/chinalv/go-upnp-igd"
	"log"
	"time"
)

func main() {
	devices := make(chan igd.Device)
	go func() {
		for d := range devices {
			log.Println(d)
			log.Println("localIPAddress:", d.GetLocalIPAddress())
			EIP, err := d.GetExternalIPAddress()
			if err != nil {
				log.Fatal(err)
			}
			log.Println("GetExternalIPAddress:", EIP)
			d.AddPortMapping("tcp", 5555, 5555, "test", 10)
			d.AddPortMapping("udp", 5555, 5555, "test", 10)

			time.Sleep(10 * time.Second)
			log.Println("DEL localIPAddress:", d.GetLocalIPAddress())
			err = d.DeletePortMapping("tcp", 5555)
			if err != nil {
				log.Fatal(err)
			}
			err = d.DeletePortMapping("udp", 5555)
			if err != nil {
				log.Fatal(err)
			}

		}
	}()
	log.Println("======== begin ========")
	err := igd.Discover(devices, 30*time.Second)
	if err != nil {
		log.Fatal(err)
	}
	log.Println("======== e n d ========")
}

```

## License

MPL 2.0
