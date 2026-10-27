---
tags: [moc]
cssclasses: dashboard
---

# 🏠 Home

> [!multi-column] 

>> [!info] Learning Vault
>> **Purpose:** Learn Linux through notes, practical labs, and troubleshooting.


>> [!todo | full] Quick Actions
>>  - [ ] Triage [[Inbox]] - `= length(this.file.inlinks)` 0
>>  - [ ] Review `#todo`

>> [!info | full ] Vault
>> `= "Notes: " + length(filter(file.lists, (x) => false))`
>> `VIEW[{ length(this.file.inlinks) }]`


---

## 🐧 Linux


>> [!multi-column] 
>> > [!note] Bash
>> > - [[01. Linux/Bash/Arrays|Arrays]]
>> > - [[01. Linux/Bash/Functions|Functions]]
>> > - [[01. Linux/Bash/Conditionals|Conditionals]]
>> > - [[01. Linux/Bash/Loops|Loops]]
>>
>>>[!note] 
>>>  - [[01. Linux/Process Management/Process Monitoring|Process Monitoring]]
>>>  - [[01. Linux/Backup|Backup]]



---

## Recent Linux Notes
```dataview
TABLE file.mtime AS "Updated"
FROM "01. Linux"
SORT file-mtime DESC
LIMIT 5
```
## 🧪 Labs

```dataview
TABLE status AS "Status", file.mtime AS "Last Edit"
FROM "02. Labs"
SORT file.mtime DESC
LIMIT 10
```




## 🗂️ Tag Index

#bash · #backup · #linux · #monitoring · #network · #troubleshooting · #lab · #ctf · #todo
