# eve4pve-zstorage
ZFS Sync storage to Host for Proxmox VE

ZFS Documentation

[Send recive](https://docs.oracle.com/cd/E18752_01/html/819-5461/gbchx.html)

[zfs - configures ZFS file systems](http://www.manpagez.com/man/8/zfs/)
```
    ______      __                       _              _    ________
   / ____/___  / /____  _________  _____(_)_______     | |  / / ____/
  / __/ / __ \/ __/ _ \/ ___/ __ \/ ___/ / ___/ _ \    | | / / __/
 / /___/ / / / /_/  __/ /  / /_/ / /  / (__  )  __/    | |/ / /___
/_____/_/ /_/\__/\___/_/  / .___/_/  /_/____/\___/     |___/_____/
                         /_/
                         
EnterpriseVE ZFS Sync storage to Host for Proxmox VE   (Made in Italy)

Usage:
    eve4pve-zstorage <COMMAND> [ARGS] [OPTIONS]
    eve4pve-zstorage help
    eve4pve-zstorage version

    eve4pve-zstorage create  --storage=<string> --host=<string> --syslog
    eve4pve-zstorage destroy --storage=<string> --host=<string>
    eve4pve-zstorage enable  --storage=<string> --host=<string>
    eve4pve-zstorage disable --storage=<string> --host=<string>

    eve4pve-zstorage status  --storage=<string> --host=<string>

    eve4pve-zstorage sync    --storage=<string> --host=<string> --syslog

Commands:
    version                  Show version program.
    help                     Show help program.
    create                   Create sync job from scheduler.
    destroy                  Remove sync job from scheduler.
    enable                   Enable sync job from scheduler.
    disable                  Disable sync job from scheduler.
    status                   Get list of all sync.
    sync                     Will sync one time.

Options:
    --storage=string         Storage Proxmox VE type ZFS.
    --host=string            Destination ip addres host Proxmox VE (es 192.168.0.1).
    --syslog                 Write messages into the system log.

Report bugs to <support@enterpriseve.com>.
```

# Introduction
ZFS Sync storage to Host for Proxmox VE.
This tools sync storage configurated in PVE (es. test: tank/test) from two or more.
Requirement the storage name will be exists in destination host.
The VM/CT in storage are copied in /etc/pve/qemu /etc/pve/lxc.
The snapshots are recursive.
```
Es.
+-------+--------------------------+       +-------+---------------------------+
| Host1 | test (tank/test_local)   |  ---> | Host2 | test  (tank/test_remote1) |
+-------+--------------------------+       +-------+---------------------------+

+-------+--------------------------+       +-------+---------------------------+
| Host1 | test2 (tank/test_remote2)|  <--- | Host2 | test2 (tank/test_local)  |
+-------+--------------------------+       +-------+---------------------------+

more hosts...
```

# Main features
* For KVM and LXC
* Syslog integration

# Configuration and use
Download package eve4pve-zstorage_?.?.?-?_all.deb, on your Proxmox VE host and install:
```
wget https://github.com/EnterpriseVE/eve4pve-zstorage/releases/latest
dpkg -i eve4pve-zstorage_?.?.?-?_all.deb
```
This tool need basically no configuration.

## Sync a VM one time

```
root@pve1:~# eve4pve-zstorage sync --storage=test --host=192.168.1.25 

VM 106 - Found in poll 'tank/test'
VM 106 - Copy config
VM 106 - Copy firewall
VM 106 - Disable auto boot
update VM 106: -onboot 0
VM 115 - Found in poll 'tank/test'
VM 115 - Copy config
VM 115 - Disable auto boot
update VM 115: -onboot 0
Creating local snapshot tank/test@zstorage192.168.1.25-17-01-31_16:44:02
Send snapshot tank/test@zstorage192.168.1.25-17-01-31_16:44:02 to remote host
Remove local snapshot tank/test@zstorage192.168.1.25-17-01-24_15:12:08
Remove remote snapshot tank/testhost1@zstorage192.168.1.25-17-01-24_15:12:08
```

## Create a recurring sync job
```
root@pve1:~# eve4pve-zstorage create --storage=test --host=192.168.1.25
```

## Delete a recurring sync job
```
root@pve1:~# eve4pve-zstorage destroy --storage=test --host=192.168.1.25
```

## Pause a sync job
```
root@pve1:~# eve4pve-zstorage disable --storage=test --host=192.168.1.25
```

## Reactivate a sync job
```
root@pve1:~# eve4pve-zstorage enable --storage=test --host=192.168.1.25
```

## Reactivate status sync
```
root@pve1:~# eve4pve-zstorage status --storage=test --host=192.168.1.25 

PVE Storage             | test
Local  ZFS pool name    | tank/test
Local  ZFS pool update  | zstorage192.168.1.25-17-01-24_15:12:08
Remote ZFS pool name    | tank/testhost1
Remote ZFS pool update  | zstorage192.168.1.25-17-01-24_15:12:08
```

## Changing parameters
You can edit the configuration in /etc/cron.d/eve4pve-zstorage or destroy the job and create it new.