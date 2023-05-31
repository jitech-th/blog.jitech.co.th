---
tags:
  - Linux
  - NTP
---
# Set up NTP server and NTP clients

Here we will use chrony as NTP server

By default chrony is available in Linux distriutions

## Configurations

- #### Navigate to `/etc/chrony.conf`

edit the pool section
```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool ntp.kku.ac.th # <-- edit this
```

- #### Verify we are using the pool that is specified

```
$ chronyc sources
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* ntp.kku.ac.th                 1   6    37     0  -3916us[  -10ms] +/-   25ms
```
or 
```
$ chronyc tracking 
Reference ID    : CA0C612D (ntp.kku.ac.th)
Stratum         : 2
Ref time (UTC)  : Wed Apr 19 11:37:43 2023
System time     : 0.001978023 seconds slow of NTP time
Last offset     : -0.001525384 seconds
RMS offset      : 0.001525384 seconds
Frequency       : 26.668 ppm slow
Residual freq   : -41.607 ppm
Skew            : 1.111 ppm
Root delay      : 0.040866069 seconds
Root dispersion : 0.006877917 seconds
Update interval : 65.1 seconds
Leap status     : Normal
```
