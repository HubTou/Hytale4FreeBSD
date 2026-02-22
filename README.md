# Hytale for FreeBSD
This repository contains a Shell script that can download the [Hytale](https://hytale.com/) server, patch it for FreeBSD and be used to update and launch it.

This script automates all the steps mentioned in the [How to run a Hytale dedicated server on FreeBSD](https://forums.freebsd.org/threads/how-to-run-a-hytale-dedicated-server-on-freebsd.101510/), [Trying to make an Hytale server on FreeBSD](https://github.com/HubTou/HubTou/wiki/Trying-to-make-an-Hytale-server-on-FreeBSD) and [Modifying Hytale for FreeBSD](https://github.com/HubTou/HubTou/wiki/Modifying-Hytale-for-FreeBSD) articles.

It should probably be included in a Hytale FreeBSD port/package, along with its dependencies...

## Pre-requisites
As root, you must:
* enable the [Linux binary compatibility](https://docs.freebsd.org/en/books/handbook/linuxemu/) layer:
  ```
  # sysrc linux_enable="YES"
  # service linux start
  ```
* install the following packages:
  ```
  # pkg install openjdk25 maven rust cmake ninja screen
  ```
  ([tmux](https://github.com/tmux/tmux/wiki) can be used instead of [GNU Screen](https://www.gnu.org/software/screen/) if you prefer)
* create an unprivileged account to run the Hytale server and own its files
* fetch and uncompress the [latest version](https://github.com/HubTou/Hytale4FreeBSD/releases) of Hytale4FreeBSD in the home directory of this account
* check the variables at the beginning of the script if you want to give more memory to the server or run it on a specific IP address / UDP port.

## How to use
As this hytale user account:
* start a Screen session:
  ```
  $ screen -S hytale
  ```
* run the script:
  ```
  $ ./hytale
  ```
* authenticate with Hytale and save your credentials:
  ```
  /auth login device
  /auth persistence Encrypted
  ```
* detach your session from the terminal:
  ```
  ^a d
  ```
* you can then logout.

When you want to come back to the server:
* bring back your session to the foreground with:
  ```
  $ screen -r hytale
  ```
* use any [Hytale command](https://www.ouiheberg.com/en/documentation/article/list-of-hytale-commands-complete-documentation)
* stop the server with:
  ```
  /stop
  ```

If you want to use commands without bringing back your session to the foreground do (replacing COMMAND by what you want):
```
$ screen -S hytale -p 0 -X stuff "COMMAND\n"
```

If the server crashes, it'll automatically be restarted.

Automatic updates will only occur when you launch the script.
It's a design choice to favour stability as Hytale seems to be picky with mod compatibility between updates (I noticed that with u3!).
When you do so, verify that your server does not immediately crash, which will avoid it to attempt relaunching continuously...

When you stop the server with the "/stop" command, you'll have to manually relaunch it, which enables you to choose when you want to update.

## Possible future directions
* Make a little config file in order to avoid modifying the script (could be useful if we [make a FreeBSD port](https://docs.freebsd.org/en/books/handbook/ports/) out of this)
* Provide a way to use the Hytale pre-release channel instead of the stable release channel
* Stop relaunching the servers after too many failed attempts
  * Check if the crash is caused by a mod and disable it if yes
* Compress former session logs upon exit (or crash?)
* Check if the installed mods have new versions available and install them if yes
  * Could be a bit tedious as [CurseForge](https://www.curseforge.com/hytale) doesn't seem to provide an end user API / CLI... but probably do-able :-)
