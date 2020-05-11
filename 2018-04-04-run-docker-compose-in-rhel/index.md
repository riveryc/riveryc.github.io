# Run docker-compose in RHEL


I was trying to run ‘docker-compose’ in our environment today, but found following message from RedHat officially:

<!--more-->

---

![screenshot](/img/post/20180404/run-docker-compose-in-rhel.png)

---

So we are on our own…

Get the latest version from Github: [https://github.com/docker/compose/releases]

Same as usual, link it to `/usr/local/bin/docker-compose`

Give it exe permission…

However, here is the problem happening in RHEL 7.4:

```bash
docker-compose: error while loading shared libraries: libz.so.1: failed to map segment from shared object: Operation not permitted
```

I tried to disable `SElinux`, not working…

Finally, I came across this github issue and I tried one of the workarounds which worked in my instance.

Solution was to remount tmp with exec permission by executing :

```bash
sudo mount /tmp -o remount,exec
```

Or have following ansible block:

```yml
- name: make mount tmp with exec
mount:
path: "/tmp"
state: mounted
src: /dev/mapper/rootvg-tmpvol # your own real path
fstype: xfs # your own real type
opts: nodev,nosuid # as long as you don't have noexec... :)
```

Problem resolved.

Happy docker-compose~

River
