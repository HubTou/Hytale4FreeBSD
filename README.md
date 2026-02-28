# Hytale for FreeBSD
This repository contains a Shell script that can download the [Hytale](https://hytale.com/) server, patch it for FreeBSD and be used to update it, launch it and automatically relaunch it in case of crash.

This script automates all the steps mentioned in the [How to run a Hytale dedicated server on FreeBSD](https://forums.freebsd.org/threads/how-to-run-a-hytale-dedicated-server-on-freebsd.101510/), [Trying to make an Hytale server on FreeBSD](https://github.com/HubTou/HubTou/wiki/Trying-to-make-an-Hytale-server-on-FreeBSD) and [Modifying Hytale for FreeBSD](https://github.com/HubTou/HubTou/wiki/Modifying-Hytale-for-FreeBSD) articles.

It should probably be included in a Hytale [FreeBSD port](https://docs.freebsd.org/en/books/handbook/ports/)/package, along with its dependencies...

## Pre-requisites
As root, you must:
* enable the [Linux binary compatibility](https://docs.freebsd.org/en/books/handbook/linuxemu/) layer:
  ```
  # sysrc linux_enable="YES"
  # service linux start
  ```
* install the following packages:
  ```
  # pkg install maven git cmake ninja rust openjdk25 cfr screen
  ```
  ([tmux](https://github.com/tmux/tmux/wiki) can be used instead of [GNU Screen](https://www.gnu.org/software/screen/) if you prefer)
* create an unprivileged account to run **each** Hytale server instance and own their files
* fetch and uncompress the [latest version](https://github.com/HubTou/Hytale4FreeBSD/releases) of Hytale4FreeBSD in the home directory of this account, and modify permissions:
  ```
  chmod a+x hytale hycmd
  ```
* check the variables in the ".hytale_config" companion script if you want to give more memory to the server, run it on a specific IP address / UDP port or use the Hytale pre-release version.

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

If you want to use commands without bringing back your session to the foreground do (replacing COMMAND and PARAMETERS by what you want):
```
$ hycmd /COMMAND PARAMETERS
```

You can exit the script with the "/stop" or "/update" commands.

Automatic updates will only occur when you relaunch the script.
It's a design choice to favour stability as Hytale seems to be picky with mod compatibility between updates (I noticed that with u3!).

If your server crashes just after launch, the script will attempt to identify an offending mod and disable it to restart automatically.
If it can't, you'll then need to investigate and restart manually.

## Possible future directions
* Check if the installed mods have new versions available and install them if yes
  * Could be a bit tedious as [CurseForge](https://www.curseforge.com/hytale) doesn't seem to provide an end user API / CLI... but probably do-able :-)
