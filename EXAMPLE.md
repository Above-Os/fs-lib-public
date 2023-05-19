### go.mod

```
module fsnotify-example

go 1.18

require (
	bytetrade.io/web3os/fs-lib v0.0.0
	k8s.io/klog/v2 v2.90.1
)

require (
	github.com/Microsoft/go-winio v0.4.16 // indirect
	github.com/go-logr/logr v1.2.3 // indirect
	github.com/james-barrow/golang-ipc v1.0.0 // indirect
	github.com/smallnest/goframe v1.0.0 // indirect
	golang.org/x/sys v0.0.0-20220722155257-8c9f86f7a55f // indirect
)

replace bytetrade.io/web3os/fs-lib => github.com/Above-Os/fs-lib-public v0.0.1
```

### example.go
```go
// Copyright 2023 bytetrade
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//     http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

package main

import (
	"bytetrade.io/web3os/fs-lib/jfsnotify"
	"k8s.io/klog/v2"
)

func main() {
	watcher, err := jfsnotify.NewWatcher("mywatcher")   // a single name of every watcher
	if err != nil {
		panic(err)
	}

	go func() {
		for {
			select {
			case e := <-watcher.Errors:
				klog.Error(e)
			case ev := <-watcher.Events:
				klog.Info(ev.Name, " - ", ev.Op)
			}
		}
	}()

	klog.Info("add /tmp/jfs/test to watch")
	err = watcher.Add("/tmp/jfs/test")
	if err != nil {
		klog.Error(err)
	}

	err = watcher.Add("/tmp/jfs/test1")
	if err != nil {
		klog.Error(err)
	}

	c := make(chan int)
	<-c
}
```

### in clutser
Set the env

POD_NAME=your pod
NAMESPACE=your namespace
CONTAINER_NAME=your container in pod
NOTIFY_SERVER=fsnotify proxy addr
