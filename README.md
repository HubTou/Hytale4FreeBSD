# Hytale for FreeBSD
This repository contains a script that can download the [Hytale](https://hytale.com/) server, patch it for FreeBSD and be used to update and launch it.

This script automates all the steps mentioned in the [How to run a Hytale dedicated server on FreeBSD](https://forums.freebsd.org/threads/how-to-run-a-hytale-dedicated-server-on-freebsd.101510/) and the [Trying to make an Hytale server on FreeBSD](https://github.com/HubTou/HubTou/wiki/Trying-to-make-an-Hytale-server-on-FreeBSD) articles.

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
  pkg install openjdk25 zip maven rust cmake ninja screen
  ```
  ([tmux](https://github.com/tmux/tmux/wiki) can be used instead of [GNU Screen](https://www.gnu.org/software/screen/) if you prefer)
* create an unprivileged account to run the Hytale server and own its files

## How to use
As this hytale user account:
* start a Screen session:
  ```
  screen -S hytale
  ```
* run the script:
  ```
  ./hytale
  ```
* authenticate with Hytale:
  ```
  /auth login device
  ```
* detach your session from the terminal:
  ```
  ^a d
  ```
* you can then logout.

When you want to come back to the server:
* bring back your session to the foreground with:
  ```
  screen -r hytale
  ```
* use any [Hytale command](https://www.ouiheberg.com/en/documentation/article/list-of-hytale-commands-complete-documentation)
* stop the server with:
  ```
  /stop
  ```
  
## Caveats
* Authentication persistence (/auth persistence Encrypted) does not work with FreeBSD for the moment.
* If your server crashes, you'll need to provide your [Hytale authentication](https://support.hytale.com/hc/en-us/articles/45328341414043-Server-Provider-Authentication-Guide) manually again...

