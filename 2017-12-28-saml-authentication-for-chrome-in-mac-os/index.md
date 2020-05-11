# SAML Authentication for Chrome in OSX


The SAML authentication for Chrome in OS X is not supported as well as it's running in Windows.

<!--more-->

Here is what we need to do:

```bash
#!/bin/sh
loggedInUser=`python -c 'from SystemConfiguration import SCDynamicStoreCopyConsoleUser; import sys; username = (SCDynamicStoreCopyConsoleUser(None, None, None) or [None])[0]; username = [username,""][username in [u"loginwindow", None, u""]]; sys.stdout.write(username + "\n");'`
echo $loggedInUser
sudo -u $loggedInUser defaults write com.google.Chrome AuthServerWhitelist "Your SAML URL"
sudo -u $loggedInUser defaults write com.google.Chrome AuthNegotiateDelegateWhitelist "Your SAML URL"
echo $(date)
exit 0
```

There you go, restart your Chrome, and enjoy. :)
