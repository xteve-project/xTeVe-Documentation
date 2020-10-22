## Linux Systemd

Create a [systemd](https://en.wikipedia.org/wiki/Systemd) startup script.    

1. Create a new file (xteve.service) in:
```
/etc/systemd/system/
```
2. Open the file with an text editor and copy the following content:
```
[Unit]
Description=xTeVe Service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
ExecStart=/path_to_executable/xteve [-options]
ExecReload=/usr/bin/killall xteve
ExecStop=/usr/bin/killall xteve
KillMode=process
Restart=always
RestartSec=15

User=running_user
Group=running_group
[Install]
WantedBy=multi-user.target
```
**The path to the xteve binary, user and the group must be adjusted**

3. Save file and execute the following commands as root:
```
sudo systemctl enable xteve
sudo systemctl start xteve
```

## FreeBSD Service
Create a [rc.d](https://www.freebsd.org/doc/en_US.ISO8859-1/articles/rc-scripting/index.html) startup script. The script also works in FreeBSD Jails.  

1. Create a new file (/etc/rc.d/) in:
2. Open the file with an text editor and copy the following content:
```
#!/bin/sh
# PROVIDE: xTeVe
# REQUIRE: DAEMON

. /etc/rc.subr
name=xteve

user="running_user"
path="/path_to_executable/xteve"


config_path="/home/${user}"
rcvar=xteve_enable

start_cmd="start_cmd"
stop_cmd="stop_cmd"

start_cmd()
{
  export HOME="${config_path}"

  echo "starting $name..."
  su -m "$user" -c "$path" &
}

stop_cmd()
{
  pids=$(pgrep "$name")
  pkill -9 "$name"
  echo "stop $name PID: $pids"
}

xteve_enable=${xteve_enable-"YES"}
pidfile=${xteve_pidfile-"/var/run/${name}.pid"}
run_rc_command "$@"
```
**The path to the xteve binary and user must be adjusted.**

3. Save file and execute the following commands:
```
chmod +x /etc/rc.d/xteve
```
4. Open the file ``/etc/rc.conf`` with an text editor to add the script to the autostart.
5. Copy the following content to the end of the file:
```
# xTeVe
xteve_enable="YES"
```


Manual start of xTeVe (execute as root):
```
service xteve start
```

Manually quit xTeVe (execute as root):
```
service xteve stop
```


