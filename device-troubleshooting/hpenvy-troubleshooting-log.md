**HP Envy: Troubleshooting**

May 13, 2026  
> **Status**: Slow startup. HDD likely bottleneck.  
**Storage**: Plan to replace/augment HDD with SSD.  
**Battery**: Not fully charging. Check. Replace only if needed.  
**OS**: Plan clean install of Windows 11 and Kali Linux.  

May 3, 2026  
> **Status**: Slow startup. Somewhat better after action taken.  
**Resource Drain**:  
`Antimalware Service Executable`: Draining memory.  
**Windows Defender**: In Task Scheduler, set to idle for one hour after startup.  
**Online Diagnostics**: Set to required only.  
**Disk Health**: `CrystalDiskInfo` indicates drive is healthy  

May 2, 2026  
> **Status**: Slow startup. Somewhat better after action taken.  
**Windows update**: Overnight.  
**BIOS Hardware Scan**: Passed.  
**SFC Scan**:  
`sfc /verifyonly`: Found integrity violations.   
`sft /scannow`: Repaired corrupt files.   
**Safe Mode**: No noticible change in startup speed.  
**Resource Drain**:  
`Windows Modules Installer Worker`: Draining CPU and memory.  
