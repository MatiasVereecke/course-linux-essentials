---
description: Searching for and finding files is an important skill of any linux user
title: Locating Things
---

# Locating Things

As a beginning Linux user it is not easy to find your way through the structure of the filesystem. Being able to search for binaries and other files will bring you a long way. Linux has several tools available to make this task less daunting.

## Searching for Indexed Files

The `locate` command is one of the easiest commands to use for finding files on your filesystem. The locate command is **lightning fast** because there is a background process that runs on your system that continuously finds new files and stores them in a database (the index). When you use the `locate` command, it then searches that database for the filename instead of searching your filesystem while you wait.

::: tip Install locate
If you get the error `command not found` you may need to install the `locate` package using `sudo apt install locate`. Note that the index will also need time to build. On top of that, the index is only generated on a periodic base (which may be once a week). The indexing process can be forced to update using the command `sudo updatedb`.
:::

The serious downside of this technique is that recently created files might not have been indexed yet and may not even show up in your search result. It may also may list files that have already been removed from the system.

```bash
locate dhcp
```

::: output
<pre>
...
/usr/share/doc/dhcpdump
/usr/share/doc/dhcpdump/changelog.Debian.gz
/usr/share/doc/dhcpdump/copyright
/usr/share/doc/isc-dhcp-client
/usr/share/doc/isc-dhcp-client/NEWS.Debian.gz
/usr/share/doc/isc-dhcp-client/README.gz
/usr/share/nmap/nselib/dhcp6.lua
...
</pre>
:::

## Finding Binaries and Man-pages

If you are looking for a binary file on your system, `whereis` can help you it. It attempts to locate the desired program in the standard Linux places, and in the places specified by the environment variables `$PATH` and `$MANPATH`.

::: tip `$PATH`
The `$PATH` environment variable contains a list of the directories where linux should search for commands that are being executed from the command line. You can request the content of the variable by using the `echo` command as follows: `echo $PATH` or using the `env` command that will output all environment variables. If the user for example wants to execute the `touch` command, linux will look in the directories specified in `$PATH` one by one looking for the `touch` binary.
:::

```bash
whereis g++
```

::: output
<pre>
g++: /usr/bin/g++ /usr/share/man/man1/g++.1.gz
</pre>
:::

As an extra, `whereis` will also display the location of the source files and the manual pages if there are any.

The easiest way to know what paths `whereis` is looking in, just add the `-l` listing option.

```bash
whereis -l g++
```

::: output
<pre>
bin: /usr/bin
bin: /usr/sbin
...
bin: /snap/bin
...
man: /usr/share/info
src: /usr/src/system76-io-1.0.1~1559663713~19.10~ea5f61a
...
src: /usr/src/linux-headers-5.3.0-7625
g++: /usr/bin/g++ /usr/share/man/man1/g++.1.gz

</pre>
:::

## Locating a Command

The `which` command only shows the path to a binary that can be found in the `$PATH` environment variable. This means that it can only be used to find commands that can be executed from the terminal by the current user.

```bash
which g++
```

::: output
<pre>
/usr/bin/g++
</pre>
:::

## All around Searching

The most versatile command to search for files on the the filesystem is `find`. It allows the user to search for any type of file in a specified location and has quite a lot of options available to tweak the search criteria.

`find` is capable of looking for files based on:

* the filename using `-name`
* the type of file using `-type`
* the modification timestamp `-mtime`
* the owner using `-user`
* the group using `-group`
* the permissions `-perm`
* the file size `-size`
* and much more ...

A lot of documentation about the `find` command can of course be found in the man-pages.

The basic syntax for using the `find` command is: `find [starting-point...] [expression]`.

* The `starting-point` is the place where to start searching for the file. All subdirectories will be included in the search by-default. A option would be to always look from the top of the filesystem, namely root `/`. However this would be a slow search. The search operation is a lot faster if you have a general idea of where the file can be found and start searching from there.
* The `expression` part is a bit more complicated. In its essence it contains the search criteria. But `find` is a lot more versatile. It can even execute commands for each file found. One could for example set the `read` permissions for every directory that has a certain group set and is created after a certain data. The wildest things are possible.

### Examples of find

Below is a basic example that searches the `/etc` directory and all it's subdirectories for a file called `passwd`.

```bash
find /etc -name passwd
```

::: output
<pre>
find: ‘/etc/ssl/private’: Permission denied
find: ‘/etc/cups/ssl’: Permission denied
/etc/cron.daily/passwd
find: ‘/etc/polkit-1/localauthority’: Permission denied
/etc/pam.d/passwd
find: ‘/etc/chatscripts’: Permission denied
find: ‘/etc/ppp/peers’: Permission denied
/etc/passwd
</pre>
:::

When searching system-wide for files using `find` you will get a lot of `Permission denied` errors from `find`. This error pops up for every directory `find` tries to enter and it does not have access too. This happens because `find` is running with the permissions of the logged in user. There are two approaches to solving this:

1. If you need to search all subdirs, even the ones you standard don't have access to, you can the `find` command with `sudo`. However this is not a good habbit so use it with caution, especially when running actions for every found entry.
2. Redirect the errors (standard error stream) to `/dev/null`, basically throwing them away: `find /etc -name passwd 2>/dev/null`. More about redirection later.

```bash
find /etc -name passwd 2>/dev/null
```

::: output
<pre>
/etc/cron.daily/passwd
/etc/pam.d/passwd
/etc/passwd
</pre>
:::

Basically `find` will only return results with the **exact name specified as the criteria**. However, often one looks for a file that contains a partial word. This can be achieved by using **wildcard operators**.

::: tip Wildcard operators
Wildcards (also referred to as meta characters) are symbols or special characters that represent other characters. You can use them with a lot of linux commands for matching a given criteria. There are three main wildcards in Linux:

* An asterisk `*` - matches one or more occurrences of any character, including no character.
* Question mark `?` - represents or matches a single occurrence of any character.
* Bracketed characters `[]` - matches any occurrence of character enclosed in the square brackets. It is possible to use different types of characters such as numbers, letters, other special characters etc. Example: `[a,b,c,1,5]`.
:::

When using wildcards it is important to surround the search criteria with double quotes `""`. If not, the shell will try to expand the wildcards and match them to files which is not our intention here.

The next example fill search for files in the `/etc` directory that contain the **keyword** `pass`.

```bash
find /etc -name "*pass*" 2>/dev/null
```

::: output
<pre>
/etc/passwd-
/etc/ssl/certs/Buypass_Class_2_Root_CA.pem
/etc/ssl/certs/Buypass_Class_3_Root_CA.pem
/etc/apparmor.d/abstractions/smbpass
/etc/cron.daily/passwd
/etc/security/opasswd
/etc/pam.d/passwd
/etc/pam.d/chpasswd
/etc/pam.d/common-password
/etc/pam.d/gdm-password
/etc/passwd
</pre>
:::

The following example performs a system-wide **search for a directory** that contains the word `bash`. This can be achieved by using the search criteria `-type` set to `d` for directory.

```bash
find / -name "*bash*" -type d 2>/dev/null
```

::: output
<pre>
/etc/bash_completion.d
/usr/share/cmake/bash-completion
/usr/share/bug/bash-completion
/usr/share/bash-completion
/usr/share/doc/bash
/usr/share/doc/bash-completion
/usr/lib/python2.7/dist-packages/bzrlib/plugins/bash_completion
</pre>
:::

The following `find` command will search for a file:

* called `README.md`
* belonging to the user `bioboost`
* with a size smaller than `100k`
* and was modified in the last `24h`

```bash
find ~ -user bioboost -name "README.md" -size -100k -mtime 0 2>/dev/null
```

::: output
<pre>
/home/bioboost/projects/challenges-linux-essentials/addendum-10-crazy-command-list/README.md
/home/bioboost/projects/course_linux_essentials/docs/07-locating-things/README.md
/home/bioboost/projects/course_linux_essentials/docs/addendum-10-crazy-command-list/README.md
</pre>
:::

For the hardcore users, `find` can also execute commands for each file found using the `-exec` argument. For example, the following search will perform a long listing with human readable output for files that are bigger than `500MB` and belong to the user `bioboost`.

```bash
find / -size +500M -user bioboost 2>/dev/null -exec ls -lh '{}' \;
```

Let's dissect this command:

* `/`: search system-wide
* `-size +500M`: a file with a size larger than 500MB
* `-user`: a file belonging to the user `bioboost`
* `2>/dev/null`: redirect the `permission denied` errors to `/dev/null`
* `-exec ls -lh '{}' \;`:
  * `-exec`: instruct find to execute command for each match found
  * `ls -lh`: the command to execute, so listing with long human readable formatting
  * `'{}'`: this passes the match from find into the command as an argument
  * `\;`: pass each match to separate `ls -lh` command. Basically this tells find to execute `ls -lh` once for each match.

::: output
<pre>
-rw-r--r-- 1 bioboost bioboost 870M Mar  5 13:23 /home/bioboost/Downloads/ubuntu-18.04.4-live-server-amd64.iso
-rw-r--r-- 1 bioboost bioboost 3.7G Jan 28 09:47 /home/bioboost/Downloads/Systeembeheer.zip
-rw-r--r-- 1 bioboost bioboost 1.8G Feb 13 17:10 /home/bioboost/Downloads/2020-02-13-raspbian-buster-lite.img
</pre>
:::

::: tip explainshell.com
Try copy pasting this command in [explainshell.com](https://explainshell.com/) and enjoy the beauty of it all.
:::

## Challenges

Try to solve the challenges without using google. Better to use the man-pages to find the information you need.

Mark challenges using a ✅ once they are finished.

### ✅ Locate

*Install the `locate` command and update the index database.*

*Locate the following files on your system:*

* `sudoers.dist`
* the configuration file `ssh_config`
* `auth.log`

```bash
locate ssh_config
/usr/share/doc/sudo/examples/sudoers.dist

locate ssh_config
/etc/ssh/ssh_config
/etc/ssh/ssh_config.d
/mnt/c/Program Files/Git/etc/ssh/ssh_config
/mnt/c/Users/matia/.vscode/extensions/ms-vscode-remote.remote-ssh-edit-0.66.1/syntaxes/ssh_config.tmLanguage.json
/usr/share/man/man5/ssh_config.5.gz

locate auth.log 
cannot be found
```

### ✅ Python man-pages

*Use the `whereis` tool to determine the location of the man-pages of `python`.*

```bash
whereis -m python
cannot be found
```

### ✅ Python man-pages

*Use the `whereis` tool to determine the location of the `find` binary.*
```bash
whereis -b find
find: /usr/bin/find /mnt/c/Windows/system32/find.exe /mnt/c/Program Files/Git/usr/bin/find.exe
```

### ✅ Which

*What is the location of the following commands for the current user:*

* `passwd`
* `locate`
* `fdisk`

```bash
which passwd
/usr/bin/passwd

which locate
/usr/bin/locate

which fdisk
/usr/sbin/fdisk
```

*Why are the location of `passwd` and `fdisk` different? What is `fdisk` used for?*

programs deemed essential for using the operating system. The distinction is usually that /sbin contains the more dangerous commands that are available to the system administrator

### Use find for the following challenges

Make sure to redirect the `permission denied` errors to `/dev/null` for all searches unless specified otherwise.


#### ✅ kernel.log

*Find the file `kernel.log`.*
```bash
find / -type f -name "kernel.log" 2>/dev/null
cannot be found
```

#### ✅ .bashrc

*Find the files `*.bashrc`.*

```bash
  find / -type f -name ".bashrc" 2>/dev/null
  /etc/skel/.bashrc
  /home/barry/.bashrc
  /home/jarno/.bashrc
  /home/lukas/.bashrc
  /home/maggie/.bashrc
  /home/matias/.bashrc
  /home/steve/.bashrc
```

#### ✅  System Configuration Files

*Search for files that end with the extension `.conf` and contain a filename with the keyword `system` in the `/etc` directory.*
```bash
find /etc -type f -name "*system*.conf" 2>/dev/null
/etc/systemd/system.conf
```

#### ✅ User Readable Files

*What option can we use on `find` to make sure the current user can read the file? Don't use the `-perm` option. There is a better option. Give a nice example.*

We can use the option -readable to list files.

```bash
 ~
> find /var/log -type f -name "*.log" -readable 2>/dev/null -exec ls -lh {} \;
-rw-r--r-- 1 root root 18K Dec 13 18:04 /var/log/alternatives.log
-rw-r--r-- 1 root root 17K Dec 13 18:11 /var/log/apt/history.log
-rw-r----- 1 root adm 80K Dec 13 18:11 /var/log/apt/term.log
-rw-r----- 1 syslog adm 4.9K Nov 11 15:06 /var/log/auth.log
-rw-r--r-- 1 root root 173K Dec 13 18:11 /var/log/dpkg.log
```


#### ✅ Altered Log Files

*Find all log files in `/var/log` that were modified in the last 24 hours. Make sure to only include files and not directories. Now extend the command to perform a long listing human readable `ls` for each file.*

```bash
matias@matias-VirtualBox:~$ find /var/log -type f -mtime -1 2>/dev/null
/var/log/kern.log
/var/log/boot.log.1
/var/log/boot.log
/var/log/wtmp
/var/log/faillog
/var/log/dmesg
/var/log/kern.log.1
/var/log/dmesg.1.gz
/var/log/dpkg.log
/var/log/cups/access_log.1
/var/log/cups/access_log
/var/log/syslog.2.gz
/var/log/dmesg.0
/var/log/gpu-manager.log
/var/log/apt/eipp.log.xz
/var/log/apt/history.log
/var/log/apt/term.log
/var/log/auth.log
/var/log/lastlog
/var/log/unattended-upgrades/unattended-upgrades-dpkg.log
/var/log/unattended-upgrades/unattended-upgrades.log
/var/log/syslog
/var/log/syslog.1
/var/log/auth.log.1
/var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/system.journal
/var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/user-1000.journal
/var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/system@0005d1d638088a20-c8be9b3afea1f8a1.journal~
/var/log/apport.log
/var/log/dmesg.2.gz
/var/log/ubuntu-advantage-timer.log
/var/log/boot.log.2

----------------------------------------------------------------

matias@matias-VirtualBox:~$ find /var/log -type f -mtime -1  -exec ls -lh '{}' \; 2>/dev/null
-rw-r----- 1 syslog adm 58K Nov 28 10:48 /var/log/kern.log
-rw------- 1 root root 18K Nov 28 10:43 /var/log/boot.log.1
-rw------- 1 root root 11K Nov 28 10:47 /var/log/boot.log
-rw-rw-r-- 1 root utmp 15K Nov 28 10:48 /var/log/wtmp
-rw-r--r-- 1 root root 32K Nov 27 12:05 /var/log/faillog
-rw-r--r-- 1 root adm 45K Nov 28 10:48 /var/log/dmesg
-rw-r----- 1 syslog adm 181K Nov 28 10:43 /var/log/kern.log.1
-rw-r--r-- 1 root adm 13K Nov 27 12:23 /var/log/dmesg.1.gz
-rw-r--r-- 1 root root 1,2M Nov 27 12:12 /var/log/dpkg.log
-rw-r----- 1 root adm 2,1K Nov 27 12:41 /var/log/cups/access_log.1
-rw-r----- 1 root adm 784 Nov 28 10:48 /var/log/cups/access_log
-rw-r----- 1 syslog adm 52K Nov 27 12:04 /var/log/syslog.2.gz
-rw-r--r-- 1 root adm 43K Nov 28 10:43 /var/log/dmesg.0
-rw-r--r-- 1 root root 1,3K Nov 28 10:47 /var/log/gpu-manager.log
-rw-r--r-- 1 root root 59K Nov 27 12:12 /var/log/apt/eipp.log.xz
-rw-r--r-- 1 root root 128K Nov 27 12:12 /var/log/apt/history.log
-rw-r----- 1 root adm 119K Nov 27 12:12 /var/log/apt/term.log
-rw-r----- 1 syslog adm 3,8K Nov 28 10:57 /var/log/auth.log
-rw-rw-r-- 1 root utmp 286K Nov 27 12:05 /var/log/lastlog
-rw-r--r-- 1 root adm 37K Nov 27 12:12 /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
-rw-r--r-- 1 root root 2,7K Nov 27 12:12 /var/log/unattended-upgrades/unattended-upgrades.log
-rw-r----- 1 syslog adm 296K Nov 28 11:03 /var/log/syslog
-rw-r----- 1 syslog adm 506K Nov 28 10:43 /var/log/syslog.1
-rw-r----- 1 syslog adm 17K Nov 28 10:43 /var/log/auth.log.1
-rw-r-----+ 1 root systemd-journal 8,0M Nov 28 11:03 /var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/system.journal
-rw-r-----+ 1 root systemd-journal 16M Nov 28 11:02 /var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/user-1000.journal
-rw-r-----+ 1 root systemd-journal 16M Nov 28 10:47 /var/log/journal/efac3a405b2b4b1abf9825ba56c34a81/system@0005d1d638088a20-c8be9b3afea1f8a1.journal~
-rw-r----- 1 root adm 0 Nov 27 12:04 /var/log/apport.log
-rw-r--r-- 1 root adm 13K Nov 27 12:04 /var/log/dmesg.2.gz
-rw------- 1 root root 314 Nov 27 12:08 /var/log/ubuntu-advantage-timer.log
-rw------- 1 root root 8,8K Nov 27 12:04 /var/log/boot.log.2
```
#### ✅ Steal All Logs

*Create a directory `logs` in `/tmp` and copy all `*.log` files you can find on the system to that location.*

```bash
find / -type f -name "*.log" -exec cp '{}' /tmp/logs ; 2>/dev/null
```

#### ✅ Markdown README files

*Find all `README.md` files on your system. Can you make it so the case of the filename does not matter? In other words, you should also be able to find `readme.md`, `Readme.md`, `readme.MD`, ...*


```bash
> find / -type f -mount -iname "readme.md" 2>/dev/null
/home/matias/.oh-my-zsh/.github/actions/spelling/README.md
/home/matias/.oh-my-zsh/custom/themes/powerlevel10k/gitstatus/README.md
/home/matias/.oh-my-zsh/custom/themes/powerlevel10k/README.md
/home/matias/.oh-my-zsh/plugins/adb/README.md
/home/matias/.oh-my-zsh/plugins/ag/README.md
/home/matias/.oh-my-zsh/plugins/alias-finder/README.md
/home/matias/.oh-my-zsh/plugins/aliases/README.md
/home/matias/.oh-my-zsh/plugins/ansible/README.md
/home/matias/.oh-my-zsh/plugins/ant/README.md
/home/matias/.oh-my-zsh/plugins/apache2-macports/README.md
/home/matias/.oh-my-zsh/plugins/arcanist/README.md
...

```