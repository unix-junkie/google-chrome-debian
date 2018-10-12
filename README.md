
 `$Id$`

This document describes the steps required in order to prevent _Google Chrome_
[packaged](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
for _Debian Linux_ from silently auto-updating itself.

It is assumed that all commands are run as `root`.

1. Create the global configuration file:
   ```bash
   cat <<EOF >/etc/default/google-chrome
   repo_add_once="false"
   repo_reenable_on_distupgrade="false"
   EOF

   # Forbid future changes to the file:
   chattr +i /etc/default/google-chrome
   ```
1. Add the necessary diversions (will affect package files once those are installed):
   ```bash
   mkdir -p /etc/apt/sources.list.d.disabled
   dpkg-divert --local --rename --divert /etc/apt/sources.list.d.disabled/google-chrome.list /etc/apt/sources.list.d/google-chrome.list

   mkdir -p /etc/cron.daily.disabled
   dpkg-divert --local --rename --divert /etc/cron.daily.disabled/google-chrome /etc/cron.daily/google-chrome
   ```
1. Install the package as usual:
   ```bash
   dpkg -i google-chrome-stable_current_amd64.deb
   ```
1. Forbid the execution of `/opt/google/chrome/cron/google-chrome` (in case it
   is invoked by accident):
   ```bash
   chmod 000 /opt/google/chrome/cron/google-chrome
   ```
   You'll need to fix file permissions every time the package is upgraded,
   though.
1. _Apt_ may still have _Google_'s GPG key present in its trusted database
   (`/etc/apt/trusted.gpg`):
   ```
   $ apt-key list
   ...
   pub   rsa4096 2016-04-12 [SC]
         EB4C 1BFD 4F04 2F6D DDCC  EC91 7721 F63B D38B 4796
   uid           [ unknown] Google Inc. (Linux Packages Signing Authority) <linux-packages-keymaster@google.com>
   sub   rsa4096 2016-04-12 [S] [expires: 2019-04-12]
   sub   rsa4096 2017-01-24 [S] [expires: 2020-01-24]
   ...
   ```
   If this is the case, delete the key:
   ```bash
   apt-key del D38B4796
   ```
1. That's it.
