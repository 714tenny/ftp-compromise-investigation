# FTP Server Compromise Investigation — Investigation Notes

## Objective and scope

Assess whether the supplied Windows XP FTP server was compromised or being misused. The investigator selected `win-xp-13`, used the ISCS-Security address `172.16.3.223`, and initially examined it from `Win-Hunt-15`. The report then records an internal review of the suspect system.

## Workflow

1. Identify the correct target interface and confirm network connectivity.
2. Connect with the FTP client; observe the Microsoft FTP Service banner and successful anonymous login.
3. Enumerate the root and `VNC4` directory and inspect suspicious text/batch artifacts without executing the batch file.
4. Use `Test-NetConnection` to check selected TCP services.
5. On the suspect system, review `ipconfig`, Task Manager with the PID column, `netstat -ano`, startup items, and file paths.
6. Compare external observations with internal process and service evidence.

## Findings

| Finding | Observed evidence | Assessment |
| --- | --- | --- |
| Anonymous FTP | Login succeeded; root directory readable | Unauthenticated exposure confirmed; write permission not tested |
| Netcat | `nc.exe`; `C:\Inetpub\ftproot\nc.exe` | Dual-use network tool; presence does not prove a shell was active |
| VNC files | `VNC4`, `winvnc4.exe`, `vncconfig.exe` | Remote-access software present; TCP 5900 check failed |
| Alternate-credential tool | `runaspc.exe` | Requires authorization review; presence alone does not prove credential abuse |
| Workstation-lock batch | `lock.bat`; `rundll32.exe user32.dll LockWorkStation` | Content inspected; batch not executed |
| IRC information | `Razor.1911.IRC.nfo`; port 6667 reference | Supports investigation of unauthorized content; not proof of C2 |
| Suspect process | `poisonivy.exe`, PID 1504 | High-priority suspicious process in this live-system investigation |
| Suspect executable path | `C:\WINDOWS\system32\poisonivy.exe` | Corroborates process triage |

## Connectivity checks

| TCP port | Test-NetConnection result | Interpretation |
| --- | --- | --- |
| 6667 | `True` | TCP connection accepted; protocol and C2 purpose not independently established |
| 5900 | `False` | VNC listener not confirmed at that port from that vantage point |
| 4444 | `False` | No successful connection at test time |
| 31337 | `False` | No successful connection at test time |
| 12345 | `False` | No successful connection at test time |

A failed check can reflect filtering or reachability conditions as well as a closed port. It does not establish that all backdoors are absent. `netstat -ano` showed listening services including 21 and 6667; the report does not conclusively tie the 6667 listener to the Poison Ivy PID.

## Assessment and response recommendations

The combination of anonymously exposed tools, suspicious content, an observed Poison Ivy-named process, and matching file paths supports treating the host as suspected compromised or misused. Netcat and VNC are legitimate dual-use tools and should not be classified as malware solely because they exist on disk.

Recommended actions: preserve evidence, isolate the host, disable unauthorized anonymous FTP exposure, review transfer and authentication logs, investigate listening services and persistence, reset affected credentials, remove unauthorized tools after preservation, and restore a trusted supported system. No mitigation or clean rebuild was validated in the submitted report.

## Limitations and lessons

The investigation did not establish initial access, FTP upload capability, an active VNC session, a confirmed Netcat shell, malware hashes, or exfiltration. Reviewing a live suspect host changes state; a production investigation should document those changes and prioritize forensic collection. PID 1504 is specific to this investigation and must not be merged with PID 480 from the separate memory exercise.
