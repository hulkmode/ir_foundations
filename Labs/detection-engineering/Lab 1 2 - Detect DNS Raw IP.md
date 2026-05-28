# Lab 1:2 - Detect DNS Raw IP

## Objective

*What will students achieve by the end of this lab?*

- Create an EQL based detect

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 15 minutes

---

# Step-by-Step Instructions

We are going to add to our detection coverage from our attack scenario by creating a detection for [**Drive-by Compromise](https://attack.mitre.org/techniques/T1189/). Take some time to read over the technique to get a better understanding how attackers are utilizing this method. Below is an small excerpt from it for convenance.**

“*Adversaries may gain access to a system through a user visiting a website over the normal course of browsing. Multiple ways of delivering exploit code to a browser exist (i.e., [Drive-by Target](https://attack.mitre.org/techniques/T1608/004)), including:*

*A legitimate website is compromised, allowing adversaries to inject malicious code*

*Script files served to a legitimate website from a publicly writeable cloud storage bucket are modified by an adversary*

*Malicious ads are paid for and served through legitimate ad providers (i.e., [Malvertising](https://attack.mitre.org/techniques/T1583/008))*

*Built-in web application interfaces that allow user-controllable content are leveraged for the insertion of malicious scripts or iFrames (e.g., cross-site scripting)*”

<aside>
💡

Why would a threat actor use a raw IP (aka direct IP) connection? Take a moment and think about what type of defenses could cause an issue if they used a domain name? Web and DNS proxies could block it from making the callback based on categorization and domain reputation. Typically most legitimate software doesn’t use raw IP connections.

</aside>

Threat Reports to get some context on techniques used

- https://news.sophos.com/en-us/2023/07/26/into-the-tank-with-nitrogen/
- https://www.microsoft.com/en-us/security/blog/2023/08/24/flax-typhoon-using-legitimate-software-to-quietly-access-taiwanese-organizations/?utm_medium=email&utm_source=govdelivery

# Step 1: Create an EQL based detect

1. On the left hand side of the Security panel click on `Rules` then click on `Detection rules (SIEM)`

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image.png)

1. Click on `Create new rule`

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image%201.png)

1. Click on `Event Correlation` and scroll down to start creating your first detection 🫠

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image%202.png)

1. Click on `Data View` button
    1. Click on the drop down window and select the data view we created during setup called `Windows`
    2. The detection we are writing is trigger when Event ID 22 is ingested with the following conditions
        1. DNS Question Name resembles IP addresses (1-3 numbers with a dot 3 times followed by another 1-3 numbers). 
        
        <aside>
        💡
        
        Just to note that this regex would match non IP numbers such as 999.999.999.999 
        
        </aside>
        
    3. Input the following syntax into the EQL query textbox and click the `continue` button
    
    EQL Query:
    
    ```bash
    network where event.code == "22" and dns.question.name regex "(\\d{1,3}\\.){3}\\d{1,3}"
    ```
    
    <aside>
    💡
    
    EQL uses categories (`network` category is used above) to aid in writing detections on certain data types (iam, file, process, etc). This is helpful when we want to in a sense write a universal detection across different data sources like Sysmon process creation events or an EDR’s process creation events. Since Elastic utilizes field normalization using their ECS schema we can use those fields in this universal detection so data sources telemetry for a process path would be called process.executable and we can take advantage of that in our detections. There may be times were an integration doesn't include a mapping to ECS and you will have to create that mapping yourself. 
    
    </aside>
    
    Example of creating EQL detection:
    

![image.png](Lab%201%202%20-%20Detect%20DNS%20Raw%20IP/image.png)

1. Input the `Name` of the detection and `Description` in their respected textboxes and click on `Advanced settings`
    
    Name:
    
    ```bash
    Windows DNS Raw IP
    ```
    
    Description:
    
    ```bash
    This detection looks for when a executable makes a DNS request for a raw IP
    ```
    

Example:

![image.png](Lab%201%202%20-%20Detect%20DNS%20Raw%20IP/image%201.png)

1. Fill out the MITRE ATT&CK tactic, technique and click on `Continue` button
    1. Tactic: Initial Access (TA0001)
    2. Technique: Drive-by Compromise (T1189)

Example:

![image.png](Lab%201%202%20-%20Detect%20DNS%20Raw%20IP/image%202.png)

1. Click on `Continue` button again and click on `Create & enable rule` button
2. Like a boss, generating those detections … woot woot!

---

## Next Steps

*What comes after this lab?*

- [Lab 2:0 - Advertisement to Access](../Attack%20Labs/Lab%202%200%20-%20Advertisement%20to%20Access%2024f86e25cbe581afa128dff3b1f8337e.md)

---

## References

- https://www.elastic.co/docs/reference/query-languages/eql/eql-syntax
- https://www.elastic.co/docs/reference/ecs
- https://attack.mitre.org/techniques/T1189/

 © 2025 DEATHGroup Labs LLC. All rights reserved.