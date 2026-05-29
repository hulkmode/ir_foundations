# Bonus Lab - Echo<Threat

## Objective

*What will students achieve by the end of this lab?*

- Setup a custom Elastic Integration
- Setup Echo<Threat on Windows VM
- Utilize Echo<Threat to verify Random Service Detect
- Utilize Echo<Threat to verify DNS Raw IP Detect

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 30 minutes

---

# Step-by-Step Instructions

*The core of the lab. Include command blocks, expected outcomes, and notes.*

# Step 1: Setup a custom Elastic Integration

1. Navigate and click on `Assets` from the Security panel on the left hand side and then click on `Browse integrations`
2. Search for `custom logs de` in the search bar and click on `Custom Logs (Deprecated)`. Even though this is deprecated it still works great. I have to do some testing on their new method of ingest before converting Echo<Threat over to it.

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image.png)

1. Click on the `Add Custom Logs (Deprecated)` button

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%201.png)

1. Input the below file path Echo<Threat will be writing its logs to disk at

Log file path:

```python
c:\users\administrator\downloads\echothreat\output\*.ndjson
```

1. Keep the Dataset name as `generic` and click on `Advanced options`

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%202.png)

1. Enter the following Processors configurations under the Processors section:
    
    Processors:
    
    ```powershell
    add_host_metadata:
    enabled: false
    drop_fields:
    fields: ["host", "agent"]
    ```
    
2. Enter the following Custom configurations under the Custom configurations section:
    
    Custom configurations
    
    ```powershell
    json.keys_under_root: true
    json.add_error_key: true
    ```
    

Example:

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%203.png)

1. Click on `Existing host`, ensure our `Workshop Policy` is present and click on `Save and continue`

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%204.png)

1. Lets update our `Windows` data view under `Discover` by clicking on the `Windows` data view and then clicking on `Manage this data view`

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%205.png)

1. Update the Index pattern to include `logs-generic*` as shown below

Index Pattern:

```powershell
logs-windows*,logs-system*,logs-generic*
```

Example:

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%206.png)

# Step 2: Setup Echo<Threat on Windows VM

1. On your Windows VM, open a PowerShell terminal if you don't have one already open, change directory to `Downloads`,  clone Echo<Threat repository using git and change directory into `echothreat`

Command:

```powershell
cd ~\Downloads | git clone [https://github.com/hulkmode/echothreat.git](https://github.com/hulkmode/echothreat.git) 
cd echothreat
```

Example Output:

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%207.png)

1. Install Echo<Threat python requirements with the following command
    
    Command:
    
    ```powershell
    python -m pip install -r requirements
    ```
    

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%208.png)

1. Review Echo<Threat help by running the following command

Command:

```powershell
python.exe .\[echothreat.py](http://echothreat.py/) --help
```

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%209.png)

# Step 3: Utilize Echo<Threat to verify Random Service Detect

Now lets see some magic fly, run the following command to verify that our `Windows Random Service Name Created` detect is working

Command:

```powershell
python.exe .\[echothreat.py](http://echothreat.py/) -ls security -eid 4697 -tf elastic_rand_servicename -o filebeat
```

Example Output:

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%2010.png)

# Step 4: Utilize Echo<Threat to verify DNS Raw IP Detect

“Do it again, do it again, okay, okay” run the following command to verify that our `Windows DNS Raw IP` detect is working

Command:

```powershell
python.exe .\[echothreat.py](http://echothreat.py/) -ls sysmon -eid 22 -tf elastic -o filebeat
```

Example Output:

![image.png](/Labs/Bonus/Detection%20Engineering/Lab%201%203%20-%20Echo%20Threat/image%2011.png)

![](https://media1.giphy.com/media/v1.Y2lkPTc5NDFmZGM2NmowdzM2dWN5NXloenJ1ZWRzazVoM2huaXJhcDFvNjJkN2Ntb2JudSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/l0IykI5OLMhjtnB60/giphy.gif)

<aside>
💡

Pivot over to Elastic and check our detections to see that they triggered. Now wasn’t that a lot easier and faster to verify that your production detect is functioning like its supposed to? By default detects run on a 5 minute schedule so you may have to wait until the next run or if you are impatient like me 😁 edit the detects schedule to be 1 minute.

</aside>

If you want to make value changes in the logs, change them in the `user config files` under the configs directory. Modify the config related to the template.

---


## Next Steps

*What comes after this lab?*

### Next IR Foundations Lab
- [Lab 1:0 - Advertisement to Access](/Labs/Lab01/metasploit/Lab%201%200%20-%20Advertisement%20to%20Access.md)

### Bonus Detection Engineering Labs


![](https://media3.giphy.com/media/v1.Y2lkPTc5NDFmZGM2cDA5cWh4a3h2b3R6OWc5NzNnNzhrZnJqMThtdnFldmgzOXdpYmgzbyZlcD12MV9naWZzX3NlYXJjaCZjdD1n/upg0i1m4DLe5q/giphy.gif)

---

## References

- https://www.youtube.com/watch?v=-fQFkrZAWmM
- https://github.com/hulkmode/echothreat

 © 2026 DEATHGroup Labs LLC. All rights reserved.