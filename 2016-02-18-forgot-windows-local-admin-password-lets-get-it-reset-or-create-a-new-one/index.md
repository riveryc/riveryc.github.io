# Forgot Windows Local Admin Password? Let's Get it Reset or Create a New One!


I don’t know how to Crack the current local admin password, but I know how to reset or become one. :)

<!--more-->

Following tools is all you need:

* Ability to Insert CD (ISO) to the VM (Server)
* Ability to boot VM (Server) from CD
* Windows Installation CD (ISO), 2012R2, 2012, Win7, 2008R2, 2008, etc… all good

Steps:

* Shutdown the Server (VM)
* Insert windows installation CD (ISO) to the Server (VM)
* Boot up the Server (VM) from CD
* When you see the welcome installation page, press “Shift + F10”
* Backup “osk.exe”

```dos
move C:\windows\system32\osk.exe c:\windows\system32\osk.bak
```

* Copy “cmd.exe” as “osk.exe”

```dos
copy C:\windows\system32\cmd.exe c:\windows\system32\osk.exe
```

* Eject CD (ISO)
* Reboot the server
* At the login page, click On-Screen Keyboard, you will see command prompt popped up… :)
* Now, you have two options:
* Add a new user: Username: tempadmin Password: password, and add it into local admin group

```dos
net user tempadmin password /add
net localgroup “administrators” “tempadmin” /add
```

* Reset existing administrator’s password: Assume “Administrator” is the local admin, and we are resetting its password to “P@ssw0rd”

```dos
net user administrator P@ssw0rd
```

* Then quit this command prompt

You are good to login with your new username and password as administrator. Just don’t forget to copy the osk.bak back to osk.exe and delete the tempadmin user if you created after you fix your local admin password!

Enjoy!



