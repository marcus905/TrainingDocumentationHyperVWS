# Module 03 — Initial Server Configuration

## Goal

Inspect and validate a newly deployed Windows Server host.

~~~powershell
Get-ComputerInfo
hostname.exe
Get-Date
Get-TimeZone
Get-NetAdapter
Get-NetIPConfiguration
Get-DnsClientServerAddress
Get-NetRoute -AddressFamily IPv4
Get-Service EventLog,WinRM,W32Time
~~~

Use Server Manager, Computer Management, Services and Event Viewer to locate the same information through the GUI.

## Validation

- [ ] Host identity confirmed.
- [ ] Time/time zone reviewed.
- [ ] Adapter, IP, DNS and default route understood.
- [ ] EventLog, WinRM and W32Time inspected.