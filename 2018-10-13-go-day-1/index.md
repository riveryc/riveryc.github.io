# Go Day 1


Should I start with “Hello World”?

Or we should get started with Installation?

<!--more-->
- - - -

I’ll go direct to something I want… :)

I want to build a webapp:

- A website is listening on port 8080 (can be any)
- Get current iptables information from localhost (Ubuntu)
- Output to frontend with JSON data
- Have inbound and outbound rule: GET and POST

- - - -

So following points will be covered:

- Create a basic website instead of outputing “Hello world”, output JSON data
- Go: deal with different route as different functions in this WebApp
- Go: Run bash in golang
- Go: Get stdout from running bash
- Go: Create class (struct) in golang
- Go: Embedded types in golang
- Go: Create method in golang
- Go: Create interface in golang
- Go: String build in golang

I’ll see how long it will take me to cover all above… probably will be a while…

Day 1:

```golang
package main

import (
   "encoding/json"
   "net/http"
)

func main() {
   http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
      mapD := map[string]string{"Name": "River", "Hello": "World"}

      json.NewEncoder(w).Encode(mapD)
   })

    http.ListenAndServe(":8080", nil)
}
```

Need to go… but simple code to get started, always good. ;)

R
