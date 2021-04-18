---
layout: post
title:  "Low Battery Notifications"
date:   2021-04-17 23:04:55 -0400
categories: jekyll update
published: true
---

When my laptop's battery hits 5%,
it makes a small beep sound.
However, sometimes I would miss it if I happen to be away
when it beeps.
So I wrote a simple systemd service that beeps every 10 seconds
after the battery drops below 10%.


The logic is very simple: invoke a script every 10 seconds;
the script checks if battery is not being charged and
is less than 10%, and beep if so.


Systemd config files are kept in /etc/systemd/system.
Here I have a timer file that tells the computer
to call the service every OnUnitActiveSec=10s
(AccuracySec=1s means the time is checked every 1 second)

/etc/systemd/system/battery-beep.timer:

	[Unit]
	Description=Battery beep when low
	
	[Timer]
	OnUnitActiveSec=10s
	OnBootSec=10s
	AccuracySec=1s
	
	[Install]
	WantedBy=timers.target
	

By default, the service file with the same name is invoked.

/etc/systemd/system/battery-beep.service:

	[Unit]
	Description=Beep regularly if battery is low
	
	[Service]
	Type=oneshot
	ExecStart=/bin/bash /path/to/battery-beep.sh


The script can be put anywhere, I put them somewhere in my
home directory.
/path/to/battery-beep.sh:

	#!/bin/bash
	
	BATINFO=`acpi -i`
	MYPOWER=$(cat /sys/class/power_supply/BAT0/energy_now)
	MYPOWERFULL=$(cat /sys/class/power_supply/BAT0/energy_full)
	
	if [[ `echo  $BATINFO | grep Discharging` && $((MYPOWER * 100 / MYPOWERFULL)) -le 10 ]] ; then beep; fi

Of course, you should have `beep` installed.
Oddly, beeps start to kick in around 9%,
I think because MYPOWERFULL is not actually the amount of charge
when battery is 100%; indeed, my fully charged laptop shows 80%
and not 100%...


Load the service as follows:

	$ systemctl daemon-reload		#takes in changes in config files
	$ systemctl start battery-beep.timer
	$ systemctl enable battery-beep.timer	#load at boot


