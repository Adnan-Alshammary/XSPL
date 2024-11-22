
# "index" and "scan": 

query can start by either index attribute or scan command.

### index:
index is the path for the log file or registry key. a registry must contains "::" as separator between root key and the path. 

examples:

`index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx"`

`index="hkcu::Software\Microsoft\Windows\CurrentVersion\RunOnce"`

`index="hklm::system\CurrentControlSet\Services"`

conditions can be written in different ways:

`index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" (EventID="1" OR EventID="2") `

or

`index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID IN ("1","2") `

Notes: 
- keyword between conditions such as "AND", "OR", "IN" should be written in upper-case
- you can use wildcard conditions in the query without specifying the field name  ` "cmd.exe" OR "powershell" `



