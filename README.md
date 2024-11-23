
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
- **directory**: can be directory or specific file path or "memory" for memory scanning. default is current directory
- **max_size**: avoid file or memory regions greater than this option. to perform full scan  max_size="-1"
- **depth**: this option used with directory file scanning to put limit for recursive search. default is 0 which means search in the provided directory only. to perform full recursive search you can use depth="-1"

   
examples:

- scan memory and extract strings that contains "http":

`scan directory="memory" "http"`

- scan specific file and extract strings that contains "http":

`scan directory="C:\Users\adnan\Desktop\pwshem\HxD.exe" "http"`

- scan a directory and extract string contains "http" from executable files only:

`scan directory="C:\Users\adnan\Desktop\" "http"  {"^4d5a"} `


# "stats":

stats command is used to calculate statistics such as average, sum or standard deviation.

syntax:

`stats <FUNCTIONS>  <BY>  <FIELD_NAMES>`


available functions:
- **count**
- **dc** (or **distinct_count**)
- **avg** (or **average**)
- **range**
- **max**
- **min**
- **stdev**
- **mode**
- **list**
- **values**
- **sum**
- **median**
- **mean**
- **var**
- **first**
- **last**

exmples: 

```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| stats values(Image) as all_Child_proc count by  ParentImage
```

Notes:
- each statistical function must receive one argument except "count" can be used without argument
- you can rename the result field of statistical function using the "as" keywork:  `values(Image) as all_Child_proc`
- when you used "by" clause, one row is returned for each distinct value in the field specified in the "by" clause



# "eventstats":

similar to stats function. the main deffierence is that eventstats does not change the incoming result, it only add the result to it. eventstats support same functions in stats command.

exmples: 

```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| eventstats values(Image) as all_Child_proc count by  ParentImage
```


# "streamtstats":
incrementaly adds a cumulative statistical value to each event in the result. it support same functions in stats and eventstats.

options:
- **current**: "true" or "false" to includes the current event in the summary calculations. Default is "true"
- **window**: the number of events to use when computing the statistics. The default is window="-1" to include all events 
- **global**: "true" or "false" and used only if window option is set. it determine if a separate window is used for each group of values of the field specified in the by clause. Default is "true"


exmples: 

```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="3" 
| streamstats current="true" window="2"  first(UtcTime) last(UtcTime)

```

# "rename":

used to rename one or more field.

example:
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| rename Image as NewProcess ParentImage as ParentProcess 
```

# "fields":

use fields command to select/remove fields name from the result

example:
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| rename Image as NewProcess ParentImage as ParentProcess 
| fields NewProcess ParentProcess
```


# "proctree":
this command is used to construct the full process tree for each process. the command expect that the result contains four fields "Image", "ParentImage", "ProcessGuid", "ParentProcessGuid". if your logs contains different field names you can use the command "rename" before using the "proctree".

example:

```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| rename Image as NewProcess ParentImage as ParentProcess 
| fields NewProcess ParentProcess ProcessGuid ParentProcessGuid
| proctree
```

# "search":
this command used to apply more filter on the result.

example: 
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| rename Image as NewProcess ParentImage as ParentProcess 
| fields NewProcess ParentProcess ProcessGuid ParentProcessGuid
| proctree
| search ParentProcTree="*cmd.exe*" AND ParentProcTree="*svchost.exe*"
```


# "where":
this command used to apply more filter on the result including the operators (==,!=,>,<). the difference between "where" and "search"  is that "search" only support the (=, !=) and support wildcard *.

example: 
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| eventstats dc(Image) as total_proc by ParentImage
| where total_proc > 10
```



# "head":
Returns only N number of specified results in search.

example: 

show only 10 events 
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| head 10
```


# "sort":
used to sort the result by specific field in descending of ascending order.
- (-) used to sort in descending order
- (+) used to sort in ascending order

example: 
```
index="C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx" EventID="1" 
| eventstats dc(Image) as total_proc by ParentImage
| where total_proc > 10
| sort - total_proc
```



