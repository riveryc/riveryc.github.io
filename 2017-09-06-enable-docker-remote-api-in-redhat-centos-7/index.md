# Enable Docker Remote API in Redhat/CentOS 7


Tried different ways to enable the Remote API, never get any luck on anything…

But finally, here is what I did, that works!

<!--more-->

```Bash
vi /etc/sysconfig/docker
```

Here is my docker content:

```bash
#/etc/sysconfig/docker
# Modify these options if you want to change the way the docker daemon runs
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false'
if [ -z "${DOCKER_CERT_PATH}" ]; then
    DOCKER_CERT_PATH=/etc/docker
fi
# Do not add registries in this file anymore. Use /etc/containers/registries.conf
# from the atomic-registries package.
#
# docker-latest daemon can be used by starting the docker-latest unitfile.
# To use docker-latest client, uncomment below lines
#DOCKERBINARY=/usr/bin/docker-latest
#DOCKERDBINARY=/usr/bin/dockerd-latest
#DOCKER_CONTAINERD_BINARY=/usr/bin/docker-containerd-latest
#DOCKER_CONTAINERD_SHIM_BINARY=/usr/bin/docker-containerd-shim-latest
```

Add `'-H tcp://127.0.0.1:4243 -H unix:///var/run/docker.sock'` in the `'OPTIONS'`:

```bash
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false -H tcp://127.0.0.1:4243 -H unix:///var/run/docker.sock'
```

Then restart your docker daemon by systemctl restart docker

Test:

```bash
curl 127.0.0.1:4243/version
{"Version":"1.12.6","ApiVersion":"1.24","GitCommit":"0fdc778/1.12.6","GoVersion":"go1.8.3","Os":"linux","Arch":"amd64","KernelVersion":"3.10.0-327.10.1.el7.x86_64","BuildTime":"2017-07-20T00:06:39.987144400-04:00","PkgVersion":"docker-1.12.6-48.git0fdc778.el7.x86_64"}
```

Done~ This only enables the local port, so it's secure for testing purpose.

