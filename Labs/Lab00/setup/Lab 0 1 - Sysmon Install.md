# Lab 0:1 - Sysmon Install

# Objective

---

*What will students achieve by the end of this lab?*

- Install Sysmon in preparation for workshop

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 10 minutes

---

# Step-by-Step Instructions

<aside>
⛔

Don’t do this as a Production rollout method. This is great for quick and easy lab setups. Use current software deployment solutions such as Remote Monitoring and Management (RMM) solutions for example Intune, SCCM, GPOs, etc

</aside>

# Step 1: Install Sysmon

1. Open your Windows VM, open an Administrator PowerShell terminal 
2. The following commands will change directories to `Downloads` directory and download the latest version of Sysmon using PowerShell
    
    Commands:
    
    ```json
    cd ~\Downloads
    ```
    
    ```powershell
    curl https://download.sysinternals.com/files/Sysmon.zip -o sysmon.zip
    ```
    
3. Extract contents of Sysmon zip file
    
    Command:
    
    ```powershell
    Expand-Archive sysmon.zip . -f
    ```
    

1. Download Swift on Security Sysmon configuration file
    
    Command:
    
    ```powershell
    curl [https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml](https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml) -o sysmon.xml
    ```
    

1. Install Sysmon and accept End User License Agreement (EULA)
    
    Command:
    
    ```powershell
    .\Sysmon.exe -accepteula -i sysmon.xml
    ```
    

### Cheat code: PowerShell one-liner

```powershell
cd ~\Downloads; curl [https://download.sysinternals.com/files/Sysmon.zip](https://download.sysinternals.com/files/Sysmon.zip) -o sysmon.zip; Expand-Archive sysmon.zip . -f; curl [https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml](https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml) -o sysmon.xml; sleep 10; .\Sysmon.exe -accepteula -i sysmon.xml
```

![](https://media1.tenor.com/m/Czfb_LkSDL4AAAAC/fancy-homer.gif)

---

## Next Steps

*What comes after this lab?*

- [Lab 0:2 - Elastic Setup](Lab%200%202%20-%20Elastic%20Setup%2024f86e25cbe581a0be6ae3ffa5a669e1.md)

---

## References

- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
- https://github.com/trustedsec/SysmonCommunityGuide
- https://www.youtube.com/watch?v=2ce7-6eVwzQ

 © 2026 DEATHGroup Labs LLC. All rights reserved.