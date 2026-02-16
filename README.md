# lipc

> [!IMPORTANT]
> `hashmap` properties are not supported

A pure Go (no C dependencies or C toolchain required) library for interacting
with the Kindle's proprietary message system, built on top of D-Bus.

Similar to [arkq's openlipc][], which provides open source headers for the
proprietary C library.

This Go library supports retrieving and setting both `integer` and `string`
LIPC properties. `hashmap` properties are not supported, as they're a more
complex interface involving shared memory, and so haven't been reversed yet.

For full documentation, check [go.pkg.dev][lipc go.pkg.dev]. Short usage
examples will follow:

## Fetching and setting properties

Getting and setting `com.lab126.winmgr`'s `orientationLock` property:

```go
package main

import (
	"context"
	"fmt"
	"log"

	"github.com/clintharrison/liblipc-go/lipc"
	"github.com/godbus/dbus/v5"
)

const (
	service = "com.lab126.winmgr"
	property = "orientationLock"
)

func main() {
	dbusConn, err := dbus.SystemBus()
	if err != nil {
		log.Fatal(err)
	}

	ctx := context.TODO()

	val, err := lipc.LipcGetProperty[string](ctx, dbusConn, service, property)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("Fetched %s->%s, value: %s\n", service, property, val)

	err = lipc.LipcSetProperty[string](ctx, dbusConn, service, property, "D")
	if err != nil {
		log.Fatal(err)
	}
}
```

Integer properties share the same API, but use `int32` for its type.

> [!NOTE]
> To get a list of all services and properties, and their current values, run
> `lipc-probe -av` on your Kindle.

You can then build this and run this program in your Kindle with:

```sh
GOOS=linux GOARCH=arm GOARM=7 go build -o lipc_test main.go
```


[arkq's openlipc]: https://github.com/arkq/openlipc
[lipc go.pkg.dev]: https://pkg.go.dev/github.com/clintharrison/liblipc-go/lipc
