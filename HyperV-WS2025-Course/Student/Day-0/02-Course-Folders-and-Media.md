# Module 02 — Course Folders and Media

## Goal

Prepare a predictable folder structure and verify the Windows Server 2025 ISO.

~~~powershell
$Root = "C:\HyperV-Course"
$Folders = @($Root,"$Root\ISO","$Root\VMs","$Root\VHDX","$Root\Export","$Root\Scripts","$Root\Logs")
$Folders | ForEach-Object { New-Item -ItemType Directory -Path $_ -Force | Out-Null }
~~~

Place the Windows Server 2025 x64 ISO in `C:\HyperV-Course\ISO` and use the local name `WS2025-EVAL-x64-EN.iso` where practical.

Verify:

~~~powershell
Get-FileHash "C:\HyperV-Course\ISO\WS2025-EVAL-x64-EN.iso" -Algorithm SHA256
~~~

Recommended host edition: Windows Server 2025 Datacenter with Desktop Experience.

## Validation

- [ ] Course folders exist.
- [ ] ISO exists.
- [ ] SHA-256 recorded/verified.