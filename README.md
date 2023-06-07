# How To Setup A Steam Game Dedicated Server With DigitalOcean

## Structure of this guide
This guide can be used for setting up a Steam game dedicated server even if you are not using DigitalOcean as your host, but there are a few optional steps that assume you are using DigitalOcean.

Text such as "(in server)" or "(in client)" before a block of commands indicates whether the commands should be run in the terminal on the server or on your computer.

Optional steps are at the end, but should not necessarily be done last. Optional steps may also require minor changes to a few commands.

Other than that, this guide should be able to be followed linearly.

## Setup Terminal Environment
If your computer is running Mac or Linux, then you're all good.

If you are on Windows, you need to install [wsl](https://learn.microsoft.com/en-us/windows/wsl/install)

wsl is just a way of simulating a Linux terminal environment

## Create A Droplet
In DigitalOcean, create a project and then add an Ubuntu droplet to that project.

You can decide to [setup an SSH key](#optional-setup-ssh-key) or just use a password to access your Droplet.

(If you are merely testing things out, I suggest just using a password)

## Creating A New User On The Server
You don't want to execute every command as root, so create a new user.

First, connect to your server:

(in client)
```shell
$ ssh root@ip-of-server
```

Then add a new user:

(in server)
```shell
$ adduser name-of-user
$ usermod -aG sudo name-of-user
```
Now switch to the user:
```shell
$ su - name-of-user
```

## Installing steamcmd
(in server)
```shell
$ sudo apt update
$ sudo apt-get update
$ sudo dpkg --add-architecture i386
$ sudo add-apt-repository multiverse
$ sudo apt-get update
$ sudo apt-get dist-upgrade
$ sudo apt install steamcmd
```

## Installing The Server With steamcmd

Note: you may want to [set up a disk volume](#optional-setup-disk-volume) if the game you want to install is larger than what your server can store.

```shell
$ steamcmd

Steam> force_install_dir /path/to/put/server/
```
If you created a disk volume, make sure `/path/to/put/server/` goes through your volume (e.g. `/mnt/my-volume/my-server`)
```shell
Steam> login anonymous

Steam> app_update app-id validate
```
You will need to replace `app-id` with the app id of the game you wish to install (e.g. 740 for csgo)
```shell
Steam> quit
```

## Running The Server
You should now be able to run `srcds_run` by doing
```shell
$ ./srcds_run
```

Note that for most games you need extra parameters when running `srcds_run`

For example, the command I run for a CSGO dedicated server is
```shell
$ ./srcds_run +sv_setsteamaccount <account-key> -net_port_try 1 -authkey <api-key> -nobots
```

## (Optional) Setup SSH Key

(in client)
```shell
$ mkdir .ssh
$ cd .ssh
$ ssh-keygen
```
Paste the entire `.pub` file into DigitalOcean


Then log in to your server as root:
```shell
$ ssh -i ~/.ssh/key-file-name root@ip-of-server
```

Now create a new SSH key for a new user

(in server)
```shell
$ su - name-of-user
$ mkdir ~/.ssh
$ cd ~/.ssh
$ vim authorized_keys
```
paste the contents of the same `.pub` file from the client

## (Optional) Setup Disk Volume
First, create a volume in DigitalOcean

your-droplet->volumes->add volume->create volume

Now setup a directory for your server

(in server)
```shell
$ cd /mnt/name-of-volume/
$ sudo mkdir name-of-game
$ sudo chown name-of-user name-of-game
``` 
