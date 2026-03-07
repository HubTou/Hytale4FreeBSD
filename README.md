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
  # pkg install maven maven-wrapper git cmake ninja rust autotools openjdk25 cfr screen
  ```
  ([tmux](https://github.com/tmux/tmux/wiki) can be used instead of [GNU Screen](https://www.gnu.org/software/screen/) if you prefer, though *hycmd* won't run without screen)
* create an unprivileged account to run **each** Hytale server instance and own their files
* fetch and uncompress the [latest version](https://github.com/HubTou/Hytale4FreeBSD/releases) of Hytale4FreeBSD in the home directory of this account, and modify permissions:
  ```
  chmod a+x hytale hycmd
  ```
* check the [variables](https://github.com/HubTou/Hytale4FreeBSD/blob/main/hytale_config) in the "hytale_config" companion script if you want to give more memory to the server, run it on a specific IP address / UDP port or use the Hytale pre-release version. And if yes, rename it to *.hytale_config* to activate it
* make sure that your server clock is synchronized, or you will get "Failed to connect to server | Invalid access token" messages when attempting to connect into it. 

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

## Is it compliant?
I believe it is, but I made a formal enquiry to Hypixel studios using the [Feedback submission form](https://accounts.hytale.com/feedback) to verify it (Report ID: 78ccf469-64ba-4a36-aa9a-f22793794ae3).

Checking [Hytale End-User License Agreement](https://hytale.com/eula) version 2.3, here are some relevant excerpts:
```diff
@@2. License Grant@@
@@2.1 License Grant.@@
+Subject to this EULA and the Terms, we grant you a limited, non-exclusive,
+non-transferable, non-sublicensable, revocable license to install and use the
+Game on devices you own or control for personal, non-commercial use. 

@@3. Permitted Uses & Modding Rights@@
@@3.1 Modding Philosophy.@@
+We support community creativity. You may create, use, and distribute
+modifications, plugins, scripts, maps, skins, models, and similar content for
+the Game (collectively, "Mods").
@@3.3 Distribution Rules.@@
+You may distribute Mods you create,
-but you may not distribute the Game (in whole or in part), including its
-source code, any decryption keys or any other files pertaining to the Game.

@@4. Restrictions & Prohibited Uses@@
@@4.1 Restrictions and Prohibited Uses.@@
-You may not: (a) reverse engineer, decompile, or disassemble the Game except as
-permitted by law;
-(b) modify, translate, or create derivative works of the Game except as
-expressly permitted in this EULA;
@@4.2 Interoperability Exception.@@
+Nothing in this EULA limits your rights under applicable law to conduct reverse
+engineering solely and to the extent necessary to achieve interoperability
+between the Game and independently created software, provided such activity is
+strictly limited to what the law permits and does not disclose or misuse our
+proprietary information. 
```

So, although minimalistic reverse engineering and decompilation were required in order to modify the game, it was done solely for the purpose of achieving interoperability with the FreeBSD operating system.

The game itself is not redistributed, although a few context lines of 2 source code files are displayed in the 2 included diff files. These tiny bits are non functional, pretty commonplace code.
