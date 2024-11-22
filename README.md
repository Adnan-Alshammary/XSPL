
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


### scan:
scan command is used to perform search for file in disk or process in memory.

syntax:
`scan  {"Hex_regex"}  "String_Regex"`

options:
- directory: can be directory or specific file path or "memory" for memory scanning. default is current directory
- max_size: avoid file or memory regions greater than this option. to perform full scan  max_size="-1"
- depth: this option used with directory file scanning to put limit for recursive search. default is 0 which means search in the provided directory only. to perform full recursive search you can use depth="-1"

   
examples:

- scan memory and extract strings that contains "http":

`scan directory="memory" "http"`

- scan specific file and extract strings that contains "http":

`scan directory="C:\Users\adnan\Desktop\pwshem\HxD.exe" "http"`

- scan a directory and extract string contains "http" from executable files only:

`scan directory="C:\Users\adnan\Desktop\" "http"  {"^4d5a"} `


# "stats":

stats command is used to calculate statistics such as average, sum or standard deviation.

available functions:
- count
- dc (or distinct_count)
- avg (or average)
- range
- max
- min
- stdev
- mode
- list
- values
- sum
- median
- mean
- var
- first
- last

exmples: 

```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| stats values(Image) as all_Child_proc count by  ParentImage
```

Notes:
- each statistical function must receive one argument except "count" can be used without argument
- you can rename the result field of statistical function using the "as" keywork  `values(Image) as all_Child_proc`
- when you used "by" clause, one row is returned for each distinct value in the field specified in the "by" clause

