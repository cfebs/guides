# php from scratch

Want to document how to setup a php project from scratch.

We will setup
- ubuntu server in virtualbox
  - no vagrant, we just want a light solid VM running X and openbox.

We will build (or rewrite)
- phpvm a version manager
- a simple web framework with limited scope

We will not
- be using docker or vagrant, all local development

The motiviation and purpose of this is to provide good from-scratch documentation and to embody the spirit of simplicty.

I love PHP for this. Despite it's faults, PHP still remains a fantasitc way to develop websites. It's stdlib and toolset is optimized for working with web requests - I know of no other langauge in this class!

> Other languages provide great built in web servers (node, Go etc.), but none have the crazy built in globals like `$_GET` and `$_POST`. It's part evil part amazing.

## Dev env with virtualbox

##### Pre-reqs

- Install virtualbox: https://www.virtualbox.org/wiki/Downloads
- Download ubuntu server: http://www.ubuntu.com/download/server

Create a new VM and insert the Ubuntu server disk. Run through the instalation process, do not install any extras.

> Will not document this process, there are pently of resources that can guide you through.

The goal is to get to this state: ![image](https://cloud.githubusercontent.com/assets/302375/13906505/1062414a-eeae-11e5-82f9-f0eec851c5af.png)

##### X and WM

Get up to date.

```
sudo apt-get update
sudo apt-get upgrade
```

Install gui

```
sudo apt-get install xorg openbox terminator leafpad chromium-browser
```

> terminator is my choice for a good solid term emulator.

Lets hop into openbox for fun.

```
xinit /usr/bin/openbox-session
```

You should get the openbox desktop. Right click opens a menu, open a terminal.

Insert the guest additions CD in the Devices menu in virtualbox.

```
sudo apt-get install dkms build-essential
sudo mount /dev/cdrom /media/cdrom
sudo bash /media/cdrom/VBoxLinuxAdditions.run
```

Now lets add our custom X start script using `.xinitrc`. https://wiki.archlinux.org/index.php/xinitrc

Open a new file `~/.xinitrc` and drop this in:

```
#!/bin/sh

# source etc xinit shell files
if [ -d /etc/X11/xinit/xinitrc.d ]
then
    for f in /etc/X11/xinit/xinitrc.d?*.sh
    do
        [ -x "$f" ] && . "$f"
    done
    unset f
fi

exec openbox-session
```

Now when you start your VM and login, you just have to run:

```
startx
```

So:

```
sudo reboot
```

And we'll continue.

Lets get a simple task bar setup.

```
# copy system defaults to local openbox config
cp -R /etc/xdg/openbox/ ~/.config/
# install tint2
sudo apt-get install tint2
```

Now lets edit our local openbox autostart (a script that starts stuff on session start).

```
# in ~/.config/openbox
...
(tint2) &
```

Now `sudo reboot` and `startx` and see what we have!

![image](https://cloud.githubusercontent.com/assets/302375/13906769/1381b110-eeb5-11e5-9f8c-6dc06190f715.png)

A pretty awesome and super lite environment to work in.

##### Recap

- VM is setup with guest additions and tools.
- Start a gui with `startx` after login.
- Openbox running tint2

Running xterm + htop after a reboot, it sits at **87MB** of memory. That's insane.

## phpvm

phpvm is a pet project that I have let sit for too long. I wrote it initially as a fun experiment, but I actually want to use it now!

Here was the project before working on it: https://github.com/cfebs/phpvm/tree/985fabca8514c4c9932fb9c6e29f7ac1a0428912

phpvm should do a few things

- list php versions from php.net
- download a version of php
- compile a version of php with options
- manage versions of php
- install composer
- do not mess w/ the shell environment too much, provide a tools to get paths to bins

These task are just a collection of bash scripts and functions.

After a few hours of work, i reproduced the desired behaviors from scratch:

