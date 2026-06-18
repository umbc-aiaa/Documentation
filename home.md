---
title: Start
description: Main Page
published: true
date: 2026-06-18T02:56:33.907Z
tags: 
editor: markdown
dateCreated: 2026-02-27T01:39:11.701Z
---

# Setup + Nextcloud

A server is a versatile computing device that can be used for applications like file serving, remote storage, application hosting, etc. For us, it will primarily be used for Nextcloud, our own private cloud. These instructions outline the setup of the server.

Nextcloud [Link](https://nextcloud.com/)

# Hardware Required

1. Nextcloud Server [Link](https://www.amazon.com/TechMikeNY-E5-2630Lv3-1-80Ghz-PowerEdge-R730xd/dp/B0GFXB429P?sr=8-4)
2. Boot Drive (Hard Drive or SSD) (SSDs recommended because of their speed)
3. Hard Drives for mass storage(can use SAS if server supports it). [HDD drives Link](https://www.amazon.com/Dell-12Gbs-56M6W-Certified-Refurbished/dp/B07GDSRV2Z?sr=8-3)
3. Hard drive trays IF they are not included with the server.


# Software Setup

1. We will use TrueNas community edition OS
2. [Download link](https://www.truenas.com/download/) (2 GB)
3. [Balena Etcher](https://etcher.balena.io/) (for mac and windows) This will uncompress your iso file.
4. Select the iso file and the USB plugged in and flash it.
5. Take the USB out after it finishes.


# Flash the Server

1. Plug in the flashed USB stick to the server.
2. If the server boots in BIOS:
	1. Go to boot manager f11
	2. Select "one shot boot manager"
	3. Select the USB from the list
3. Click "Start TrueNAS scale installation".
4. On the Console setup screen:
	1. Select "Install/Upgrade"
	2. Select the drive you want to install the OS to (use SPACE bar to select).
	3. Web UI screen:
		1. username: `admin_user`
		2. password: `umbcaiaa`
5. WAIT.
6. Remove the USB stick
7. Reboot the system. (option 3)
8. On the GNU GRUB screen:
	1. Select "TrueNAS scale GNU/Linux x.x"


## Network Setup

TrueNAS requires internet connection to run. Internet can be provided using an ethernet cable. If an ethernet port is unavailable, connect the ethernet port to your computer and share your internet.

To share internet from your laptop(Windows only):

2. Control Panel
3. Network
4. change adapter settings
5. right click WIFI
6. properties
7. sharing tab
8. check the box and select your ethernet interface connected to the server
9. on server:
	1. Go to network configuration by slecting "1" once server is booted.
	2. ensure ALL interfaces have an IP
	3. set ipv4_DHCP and ipv6_auto to YES
	4. press "a" to apply settings
	5. Press "p" to persist
	6. press "q" to quit

At this point, the server will be connected to an ip address. In the network configuration screen, you should see an IPv4 address(something like 192.168.x.x, 10.x.x.x, or 172.16-31.x.x). Typing this address into your browser will give you access to yhe TrueNAS Web UI.

# To access the TrueNAS dashboard:
1. on laptop browser:
	1. go to the ip address shown on the server
  	1. Either look at the address through the network configuration screen or find it with `ip a` from the terminal.
	2. username: `truenas_admin`
	3. password: `umbcaiaa`
	

# Adding additional users:

For security reasons, you should disable the `truenas_admin` account as soon as possible. Each user that needs access to the dashboard **must** have their own account with the appropriate permissions set. This reduces the impact of one user exposing their credentials.

To add an account, perform the following steps.
1. Go to `Credentials > Users > Add`
2. Add a username and configure access to necessary services
	- Users that will manage the server should have shell and ssh access
  - Use public key authentication for SSH. It is easy to set up and much more secure than passwords
  - Refer to the [Adding SSH Keys]()

# Adding a storage pool on TrueNAS:
1. Click on "Storage" (Left Side)
2. Click on "Create Pool" 
3. Add a name such as "main"
4. Click on "Layout" and select "RAIDZ2"
5. Continue with automated disk selection (Click "Save And Go To Review")
6. Once in "Review" click "Create Pool"
7. Check "Confirm" and click "Continue"


# Using containers through YAML Configuration

TrueNAS supports running docker containers natively with docker compose files. To do this, we will make a containers folder in the pool we created earlier to house the compose files for all the different applications we will run. We will dedicate a sub-directory to each app.

Note that the pools will mount at the `/mnt` directory

1. On the left panel, open the shell by going to `System > Shell`
2. Assuming the pool is named `main`, change into the pool directory:
```
cd /mnt/main/
```
3. Create the `containers` folder
```
mkdir containers
```
4. Change into the `containers` folder and take ownership of the folder using your user ID (UID) and group ID (GID) (can be found by running the `id` command). You may need to run the latter command with `sudo`.
```
cd containers
chown -R <UID>:<GID> .
```
5. Create a folder for your app and `cd` into it. We will demonstrate app deployment by installing Wiki.js(the very app you are reading this on).
```bash
# From the containers folder
mkdir wiki
cd wiki
```
6. Copy the `docker-compose.yml` file from the [wiki.js website](https://docs.requarks.io/install/docker#using-docker-compose) into the directory.
7. Check that the ports section is not mounting the app to a used port on the server(e.g. if it is mapping 3000 to 80 with "80:3000", this will cause problems, so map it to a different port like "3000:3000")
8. Save the changes. Then navigate to `Apps > Configuration > Choose Pool`
9. Make sure the pool is set to the same pool that you made the `containers` folder in.
10. Navigate to `Apps > Discover Apps` and click on the kebab menu(⋮). Then go to click on `Install via YAML`
11. Name the app appropriately. In the custom config, include the path to you docker-compose file. Assuming you named everything as we did and put everything in the same directories, this would look like the following:
```yaml
include:
	- /mnt/main/containers/wiki/docker-compose.yml
```
12. Click `Save`, and the app should install!

# Guide to Troubleshooting
1. **READ THE ERROR MESSAGES**
	- TrueNAS in my experience has very good logging, as you can often find issues very quickly by reading the error messages/logs. Use the shell and `cat` commands to read logs if you need to.
2. Go to the original documentation. We try our best to keep this documentation current and relevant to the specific things we need to accomplish. However, this resource may not always be up to date, so go to the source(especially if you're trying to do something new)
3. **MAKE BACKUPS**, and then mess around and see what happens.
