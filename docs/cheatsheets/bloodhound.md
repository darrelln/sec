---
layout: default
title: BLOODHOUND
parent: CHEATSHEETS
has_children: true
has_toc: false
---

{% include toc.html %}

## References

[IppSec's Sauna Walkthrough](https://youtu.be/uLNpR3AnE-Y)

## Installation
`Bloodhound` is in the repos so can be installed using `apt`:
```
sudo apt install bloodhound
```

Download the `Bloodhound` ingestors from `GitHub`:
```
https://github.com/BloodHoundAD/BloodHound/tree/master/Ingestors
```

On first run, start `neo4j` and change the password:
```
sudo neo4j console
```

Once the console starts, a localhost URL will be printed. Open this in a browser and follow the instructions to change the password. For consistency, change it to `bloodhound`. Leave `neo4j` running and start `bloodhound` using:
```
bloodhound
```

`Bloodhound` can be a bit finicky with updating it's URL, so if the submit button stays disabled after entering the password make a change in the UI and undo the change and that should enable the submit button.

### Run Ingestors to Gather Data
Grab the ingestors from GitHub. Firefox will flag them as viruses, so `wget` is a better option:
```
wget https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Ingestors/SharpHound.exe
```

Upload `SharpHound.exe` to the target and execute. This will create a `.zip` file like `20200719080734_BloodHound.zip`. Download this file and drag and drop the `.zip` into `Bloodhound`.

## Bloodhound Queries
Once the ingestor data has been loaded into search for the accounts you have and select them to add them to the UI. Once added to the UI, right-click them and mark them as owned. Once all the accounts you own have been added to the UI, use the built-in queries from the dropdown to query the domain info and (hopefully) find paths to exploitation.