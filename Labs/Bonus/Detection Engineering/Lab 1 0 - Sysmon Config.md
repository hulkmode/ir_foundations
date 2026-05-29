# Lab 1:0 - Sysmon Config

## Objective

*What will students achieve by the end of this lab?*

- Review Swift on Security Sysmon configuration
- Add an exclusion to configuration file

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 20 minutes

---

# Step-by-Step Instructions

This lab will walk you through looking at the sysmon configuration and identify what hashing is being logged during process creation, general concept on what is being logged for process creation events and finally adding our own exclusion rule.

# Step 1: Review Swift on Security Sysmon configuration

1. On your Windows VM, open File Explorer and navigate to `c:\Users\Administrator\Downloads\` where we downloaded our Sysmon binaries and the Swift On Security Sysmon configuration files.
    
    File Explorer Icon:
    
    ![image.png](Lab%201%200%20-%20Sysmon%20Config/image.png)
    
2. Right click on `sysmon.xml` , hover over `Open with` and select `Visual Studio Code`
    
    ![image.png](Lab%201%200%20-%20Sysmon%20Config/image%201.png)
    
3. Lets take a look at the Sysmon configuration to get a little bit more familiar with it.  Some of the benefits of starting off with this configuration is it’s straight forward and you don't have to know all the different `conditions` to include or exclude events and it doesn’t use compound rules to match events.
4. Ok lets scroll down from the top of the configuration until we get down to the first parts of the configuration where it shows what hashes will be logged. Out of the box this configuration is setup to log MD5, SHA256 and IMPHASH hashes. 
    
    ![image.png](Lab%201%200%20-%20Sysmon%20Config/image%202.png)
    
5. Now lets scroll down to our first event filter for `ProcessCreate` events. Working with this config, it is designed to log all process creation events except what you specify to exclude from being logged. Looking at the `groupRelation` it is using the `or` operator for this `RuleGroup` and when there is a matching event it will `exclude` it which is specified with `onmatch`

![image.png](Lab%201%200%20-%20Sysmon%20Config/image%203.png)

1. The exclude list is quite long and needs to be tailored to your environment so scroll down to `line 230` for the last exclude rule
2. Make a new line under `line 230`

![image.png](Lab%201%200%20-%20Sysmon%20Config/image%204.png)

<aside>
💡

Public Sysmon configurations are a great learning tool and starting point but ensure to tailor and tune the sysmon configuration to your environment

</aside>

# Step 2: Add an exclusion to configuration file

1. You are going to make your first Sysmon configuration change, so keep this open and lets `pivot to Elastic browser window/tab` to identify something noisy we might want to filter out so it doesn’t send to our SIEM. When it comes to sending data to your SIEM, just remember “*mo data mo money*” 😂 so try to be mindful in production stacks that you are not causing unnecessary costs and could technically slow down your queries.  If it’s in your index, it’s going to search through it, just saying 🙂.
2. In our `Discover` windows input `process.executable` in the fields search bar
    1. Left click on the field named `process.executable` , this will display the top counts by process executable.  This will help us identify an executable that we can exclude in our Sysmon configuration. 
    2. A common exclusion that you may want to make is excluding your SIEM agents or EDRs since they will be running all the time and can be quite the noise makers. 
    3. For us, VSCode seems to be noisy enough to make me want us to exclude it
    
    <aside>
    💡
    
    Your results may differ from the screenshot below
    
    </aside>
    
    ![image.png](Lab%201%200%20-%20Sysmon%20Config/image%205.png)
    
    d.  Paste the following on `line 231` 
    
    ```xml
    <Image condition="contains all">C:\Users\;\AppData\Local\Programs\Microsoft VS Code\Code.exe</Image>
    ```
    
    e. The the config should look like
    
    ![image.png](Lab%201%200%20-%20Sysmon%20Config/image%206.png)
    
3. Save the config by going to `File` and clicking on `Save`
    1. Or you can `hold down ctrl` and `press S` to save
    
4. Open up a PowerShell terminal and run the following command

```xml
sysmon -c .\Downloads\sysmon.xml
```

Example Output:

![image.png](Lab%201%200%20-%20Sysmon%20Config/image%207.png)

---

## Next Steps

*What comes after this lab?*
### Next IR Foundations Lab
- [Lab 1:0 - Advertisement to Access](/Labs/Lab01/metasploit/Lab%201%200%20-%20Advertisement%20to%20Access.md)


### Bonus Detection Engineering Labs
- [BLab 1:1 - Detect Random New Service](/Labs/Bonus/Detection%20Engineering/Lab%201%201%20-%20Detect%20Random%20New%20Service.md)

---

## References

- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
- https://github.com/trustedsec/SysmonCommunityGuide
- https://www.youtube.com/watch?v=2ce7-6eVwzQ

 © 2026 DEATHGroup Labs LLC. All rights reserved.