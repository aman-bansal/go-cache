# go-rcache

go-rcache is fork of https://github.com/patrickmn/go-cache

### Installation

`go get github.com/aman-bansal/go-rcache`

### Aim

The forked was build to provide the functionality of refresh in cache. A new function `WithRefreshStrategy` is provided
which takes two arguments, refresh interval and refresh function. Clients can define function to evaluate the new state
of cache based on the key provided in the argument.

`RefreshStrategy` helps to refresh the key asynchronously. The old value will be returned till the key is refreshed. 
One key thing to note is that, `RefreshStrategy` depends upon the performance of the refresh function. Even though keys 
get refreshed asynchronously with respect to the calling clients, all the keys are collected over a channel (size 1000) 
and then processed in a serial manner. Hence, if the performance of refresh function is degraded, and the channel gets 
overcrowded you might get performance issue over your client. So always keeps the timeouts handy in your refresh function.

### Usage

```go
import (
	"fmt"
	"github.com/aman-bansal/go-rcache"
	"time"
)

func main() {
	// Create a cache with a default expiration time of 5 minutes, and which
	// purges expired items every 10 minutes.
	// And Refresh ttl is defined to after 1 minute
	c := cache.New(5*time.Minute, 10*time.Minute).WithRefreshStrategy(time.Minute*1, func(key string) (interface{}, error) {
		value := ....
			...... // evaulate value for a key
        return value, nil
    })
}
```