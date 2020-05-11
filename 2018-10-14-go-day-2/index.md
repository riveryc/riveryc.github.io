# Go Day 2


While you have multiple pages (or functions)…

<!--more-->
- - - -

In my case, I’ll have basic following pages:

- Index: list all iptable rules (Get)
- Inbound: list all inbound nat rules (Get)
- Outbound: list all outbound nat rules (Get)
- Yesterday, I’ve used “http.HandleFunc()” to send json as index, so I’ll do the same for in/outbound list, and write them into different functions, call functions from the main script:

Nothing fancy, nothing new, just code here:

```golang
package main

import (
   "encoding/json"
   "net/http"
)

func index(w http.ResponseWriter, r *http.Request)  {
   mapindex := map[string]interface{}{"tittle": "iptables", "region_name": "region_name", "region_number": 52, "iptables":"172.26.52.11"}

   json.NewEncoder(w).Encode(mapindex)
}

func inbound(w http.ResponseWriter, r *http.Request)  {
   mapinbound := map[string]interface{}{"tittle":"inbound", "region_name": "region_name", "region_number": 52, "inbound rules": "inbound"}

   json.NewEncoder(w).Encode(mapinbound)
}

func outbound(w http.ResponseWriter, r *http.Request) {
   mapinbound := map[string]interface{}{"tittle":"inbound", "region_name": "region_name", "region_number": 52, "inbound rules": "outbound"}

   json.NewEncoder(w).Encode(mapinbound)
}

func main() {
   http.HandleFunc("/", index)
   http.HandleFunc("/inbound", inbound)
   http.HandleFunc("/outbound", outbound)

   http.ListenAndServe(":8080", nil)
}
```

Will do bash run tomorrow…

R
