# Documented command examples

These commands describe the original authorized lab workflow. They are not intended for unrelated systems.

```powershell
ping 172.16.3.223
ftp 172.16.3.223
# Inside the FTP session: dir, cd VNC4, dir
Test-NetConnection 172.16.3.223 -Port 6667
Test-NetConnection 172.16.3.223 -Port 5900
Test-NetConnection 172.16.3.223 -Port 4444
Test-NetConnection 172.16.3.223 -Port 31337
Test-NetConnection 172.16.3.223 -Port 12345
```

The modern investigation workstation ran PowerShell checks. Internal Windows XP review used `ipconfig`, `netstat -ano`, Task Manager, and `msconfig`; this repository does not imply that modern PowerShell commands were run on XP.
