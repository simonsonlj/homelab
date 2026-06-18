**HP Envy: Troubleshooting**

June 1, 2026
> **Status**: Retired. Root cause assessed as likely drop damage.  
**Battery**: Replaced twice. First battery was defective (66% of design capacity at full charge). Second battery was fine. Returned both in the end.  
**Storage**: SSD swap performed. No change in performance. Kept SSD for use in other machines.  
**OS**: Clean Windows install done first as a sanity check on new hardware. Performance still poor. Switched to Bazzite for lower overhead. Performance still suffered. Kali install (originally planned to follow Bazzite, sequenced last due to USB boot compatibility) never reached.  
**Diagnostics**: HWiNFO64, GPU-Z, CPU-Z, and HP PC Hardware Diagnostics (UEFI) run across multiple stages; no software-level fix (e.g. drivers) resolved the underlying issue.  
**Conclusion**: Hardware symptoms persisted across OS reinstalls and component swaps, pointing to historical physical/drop damage rather than a software or single-component fault. Machine retired.  
**Salvage**: SSD and one DDR3 RAM stick pulled for reuse.  

May 22, 2026
>**Status**: Slow startup. Noticed battery issues; doesn't charge while on; drains quickly while unplugged. Will likely replace adapter, battery, and storage.  
**Battery/Charger**: Running boot up diagnostic using HP PC Hardware Diagnostics tool. Battery and charger both received warnings. Incompatible charger and battery status: weak (30), logic state: weak (30), full charge cap: 48%.  

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
