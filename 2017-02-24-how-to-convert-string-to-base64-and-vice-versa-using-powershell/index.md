# How to Convert String to Base64 and Vice Versa Using Powershell


Working with Consul without UI is the correct way to go, which also help you with the productivity!!! (True?, hahaha~~)

<!--more-->

Anyway, I’m not allowed to have a UI for it, come down to have read the correct value and make sure it’s the one we want it to be in there, I need to do heaps “Get” to get the value, however the value is encoded with Base64…

Here is the way to decode it:

```PowerShell
$response = Invoke-RestMethod -Method Get -Uri "http://localhost:8500/v1/kv/hello"
$b  = [System.Convert]::FromBase64String($($response.Value))
[System.Text.Encoding]::UTF8.GetString($b)
Hello World
```

To encode it:

```PowerShell
$b  = [System.Text.Encoding]::UTF8.GetBytes("Hello World")
[System.Convert]::ToBase64String($b)
SGVsbG8gV29ybGQ=
```

Have fun~~
