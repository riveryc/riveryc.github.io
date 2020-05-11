# Go Day 3


Sometime it’s just hard (lazy) to find an existing module (package in Go) for certain work… For something small or handy, we may just run a bash command from the app…

<!--more-->
- - - -

For this one, I’ll only cover the run basic bash command from Go as later on I’ll do struct (class in Go) to cover bit more for iptables.

Running bash command in Go it’s simple, we need following vars to be prepared:

- cmd: command string
- args: arguments string array
- import “os” and “os/exec” packages (this is normally done by IDE)

And we are good to go~ create a new HandleFunc in main function called “exec”, then assign a new function for this handler “execcmd”:

```golang
http.HandleFunc("/exec",execcmd)
```

This will make sure that when I access “http://localhost:8080/exec”, function `execcmd` will be executed.

Add new function `execcmd`:

```golang
func execcmd(w http.ResponseWriter, r *http.Request) {
   cmd := "ls"
   args := []string{"-al"}
   cmdout, err := exec.Command(cmd,args...).Output()
   if err != nil {
      fmt.Fprint(w, err)
   }
   out := string(cmdout)
   fmt.Fprintln(w,out)
}
```

I just used the very simple bash command here as example “ls -al”.

Line 8 is necessary as var “cmdout” by default type is byte[], so we have to convert it to string, so that it can be read from the console and showing on the site.

Here is the result by accessing that URL:

```bash
total 8
drwxr-xr-x  4 ryang  1825648216   136 Oct 16 07:23 .
drwxr-xr-x  7 ryang  1825648216   238 Oct 13 20:38 ..
drwxr-xr-x  7 ryang  1825648216   238 Oct 16 07:23 .idea
-rw-r--r--  1 ryang  1825648216  1199 Oct 16 07:23 webapp.go
```

Shows existing folder’s folder and files…

R
