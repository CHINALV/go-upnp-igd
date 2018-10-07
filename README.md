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
			d.AddPortMapping("tcp", 55555, 55555, "test", 30)
			d.AddPortMapping("udp", 55555, 55555, "test", 30)

		}
	}()

	err := igd.Discover(devices, 30*time.Second)
	if err != nil {
		log.Fatal(err)
	}
	log.Println("Clean....")
	for d := range devices {
		log.Println("DEL localIPAddress:", d.GetLocalIPAddress())
		d.DeletePortMapping("tcp", 55555)
		d.DeletePortMapping("udp", 55555)
	}

}
```

## License

MPL 2.0
