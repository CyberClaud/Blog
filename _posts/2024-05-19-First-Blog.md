---
title: "Pi-Hole"
date: 2024-05-19 00:00:00 +0800
categories: [Cybersecurity, Raspberry Pi]
tags: [DNS, Networking]
---

# Pi-Hole

## What is Pi-hole? / How does it work?

The Pi-hole is a DNS sinkhole that protects your devices from unwanted content, without installing any client-side software.

When Pi-hole is installed, and your computers and devices are configured to use it for their DNS queries, ads and malware are blocked automatically in order to reduce the chances of being tracked, or having malware installed.

## Prerequisites

Pi-hole is very lightweight and does not require much processing power. Despite the name, you are not limited to running Pi-hole on a Raspberry Pi. Any hardware with Min. 2GB free space, 4GB recommended and 512MB RAM that runs one of the following supported operating systems will do!

- Raspberry Pi OS

- Armbian OS

- Ubuntu

- Fedora

- CentOS Stream

A microSD card – Does not have to be a big card – 8GB is totally fine. When finished, Raspberry Pi OS + Pi-hole will end up taking up about 1.6GB on the microSD card.

[Raspberry Pi Imager][1] – This software formats the microSD card and installs your Raspberry Pi OS of choice. It also allows you to set some of the Raspberry Pi defaults before you ever boot it up. Raspberry Pi Imager is available for download on Windows, macOS, and Ubuntu.

I will be using a Raspberry Pi 4 (8GB) and San Disk Ultra mircoSD card (32GB)

## Flashing Raspberry Pi on the mircoSD card

Download and run Raspberry Pi Imager on your desktop computer. You’ll also want to insert the microSD card into a microSD card reader. Take note of the drive letter of the microSD card – but no need to format it ahead of time as Raspberry Pi imager will do this for you.

CHOOSE OS -> Raspberry Pi OS (other) -> Raspberry Pi OS Lite (64-bit)

Next, click ‘CHOOSE STORAGE.’ You’ll want to select your microSD card.

Check the box next to ‘Set hostname:’ and pick a name. I’m going to call mine piserver.local

Check the box next to ‘Enable SSH’ and then leave the radio button on ‘Use password authentication.’

Set a username and password. I’m going to set 'cyberclaud' as the username.

If you are going to use wifi then set up the wireless LAN setting. If not you can leave 'Configure wireless LAN' unchecked.

Set locale settings and then pick your time zone and keyboard layout. When finished, click ‘SAVE.’

Finally, click ‘WRITE’ and your brand new Raspberry Pi OS will be written to the microSD card. This takes about 3-4 minutes. Once complete, remove the microSD card and insert it into your Raspberry Pi.

Plug power into your Raspberry Pi to boot it up – the boot process should only take a minute or so.

## Figuring out IP address of Raspberry Pi

Hook up a keyboard and monitor to the Raspberry Pi, log in, and then run:

```bash
ip a
```

If your raspberry pi is connected via wifi then the IP address will be under 'wlan0' in line:

```
inet [YOUR IP ADDRESS]
```

If your raspberry pi is connected via Etherenet then the IP address will be under 'eth0' in line:

```
inet [YOUR IP ADDRESS]
```

## SSH into the Pi

Once you have your Raspberry Pi (RPI)’s IP address, you can SSH from the terminal by typing:

```
ssh [YOUR RPI USERNAME]@[YOUR RPI IP ADDRESS]
```
Log in with your username and the password you set in the Raspberry Pi Imager.

## Update Raspberry Pi OS

The first thing we want to do is update all packages. Updating the Pi takes 4-5 minutes. To do so, run this command:

```bash
sudo apt update && sudo apt upgrade -y
```
## Set a Static IP Address

In order for Pi-hole to work, the raspberry pi will need to have a static IP address. This can be done by typing the following:

```bash
sudo nano -w /etc/dhcpcd.conf
```

And then scroll down until you find the section called 'Example Static IP configuration:'

This section is commented out by default, so you will need to uncomment some lines and input your own static IP address and network information. 

Here's an example:

```
# Example static IP configuration:
interface eth0
static ip_address=[YOUR STATIC IP ADDRESS]
# static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=[GATEWAY IP ADDRESS]
static domain_name_servers=[GATEWAY IP ADDRESS] 1.1.1.1
```

Interface eth0 was uncommented, and then set your static IPv4 IP address of (remember if your rpi is connected via wifi then replace 'eth0' with 'wlan0'). Then set ‘static routers’ to your gateway IP and set two DNS servers (your gateway IP and your DNS server of your choice)

DNS Server Selection - There’s a great breakdown of the various DNS servers, and which ones do phishing/malware/adult content filtering [HERE][2].

Press CTRL+X to exit followed by ‘Y’ and ENTER to save.

Now reboot the Pi to take advantage of the new network settings:

```bash
sudo reboot
```

## Install Pi-hole

Once you’ve set your RPI’s networking to a static IP address and rebooted, it’s time to install Pi-hole. Run this command to install Pi-hole:

```bash
curl -sSL https://install.pi-hole.net | bash
```

This will fire off the installation wizard. When the screen below appears, press ENTER (OK).

On this screen, double-check that the IP address shown is the static IP address that you want to use for the Pi-hole and then press ENTER (select Yes – Set static IP using current values).

NOTE: If you set a specific static IP, but that is not displayed on this screen, you probably forgot to reboot the Raspberry Pi. Cancel out – reboot, and then re-start the installation wizard.

On this screen we are going to pick an upstream DNS provider. This is the DNS server that the Pi-hole will use to do DNS lookups (initial lookups – then they get cached).

Next we’re asked if we want to use the default included block list – this block list is a list of domains that Pi-hole is going to block for us. This list is perfectly fine, and will block a significant chunk of suspect sites

Next we’re asked if we want to install the Admin Web Interface, which of course we do! Select YES and press ENTER.

Another notice about the web server – just choose YES and press ENTER.

Next, we’re asked if we want to enable query logging. Typically, you will want to say Yes here, but if you’re super security conscious and you don’t want any of your DNS lookups logged, you can also choose No.

Now we’re asked about the level of privacy – you can visit the [website][3] for more information. Choose what fits your situation.

This final screen is giving a summary of our install and also shows us our Admin Webpage login password. You don’t need to copy this down – we’re going to change that password in the next step.

## Change the Pi-hole Web GUI Admin Password

You’ll want to set a nice strong password for the Pi-hole admin GUI – to do that, run this command:

```bash
pihole -a -p
```

This will have you input a password and then confirm it.

## Log into the Pi-hole GUI

We’re now ready to log into the GUI for the first time! Open up a browser and input the static IP address followed by /admin in this format:

```
http://[YOUR STATIC IP ADDRESS]/admin
```


[1]: https://www.raspberrypi.com/software/
[2]: https://docs.pi-hole.net/guides/dns/upstream-dns-providers/
[3]: https://docs.pi-hole.net/ftldns/privacylevels/
