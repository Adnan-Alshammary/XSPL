
# "index" and "scan": 

query can start by either index attribute or scan command.

### index:
index is the path for the log file or registry key. a registry must contains "::" as separator between root key and the path. 

examples:

`index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx"`

`index="hkcu::Software\Microsoft\Windows\CurrentVersion\RunOnce"`

`index="hklm::system\CurrentControlSet\Services"`



