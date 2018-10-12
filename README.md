
 `$Id$`

This document describes the steps required in order to prevent _Google Chrome_
[packaged](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
for _Debian Linux_ from silently auto-updating itself.

It is assumed that all commands are run as `root`.

Create the global configuration file:

```bash
cat <<EOF >/etc/default/google-chrome 
repo_add_once="false"
repo_reenable_on_distupgrade="false"
EOF

# Forbid future changes to the file:
chattr +i /etc/default/google-chrome
```
