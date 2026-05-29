# Lab 1:1 - Detect Random New Service

## Objective

*What will students achieve by the end of this lab?*

- Create an EQL based detect

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 30 minutes

---

# Step-by-Step Instructions

We are going to add to our detection coverage from our attack scenario by creating a detection for [**Create or Modify System Process: Windows Service (MITRE ATT&CK T1543.003)](https://attack.mitre.org/techniques/T1543/003/). Take some time to read over the technique and the sub-techniques to get a better understanding how attackers are utilizing this method. Below is an small excerpt from it for convenance.**

“*Adversaries may create or modify Windows services to repeatedly execute malicious payloads as part of persistence. When Windows boots up, it starts programs or applications called services that perform background system functions.[[1]](https://technet.microsoft.com/en-us/library/cc772408.aspx) Windows service configuration information, including the file path to the service's executable or recovery programs/commands, is stored in the Windows Registry.*”

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
    2. The detection we are writing is trigger when Event ID 4697 is ingested with the following conditions
        1. Service Name is uppercase alpha with a length of 6 characters 
        2. AND
        3. Service File Name contains .bat OR .exe
    3. Input the following syntax into the EQL query textbox and click the `continue` button
    
    EQL Query:
    
    ```bash
    any where event.code == "4697" and [service.name](http://service.name/) regex "([A-Z]{6})" and winlog.event_data.ServiceFileName regex ".*\\.(bat|exe).*"
    ```
    
    <aside>
    💡
    
    EQL uses categories (`any` category is used above) to aid in writing detections on certain data types (iam, file, process, etc). This is helpful when we want to in a sense write a universal detection across different data sources like Sysmon process creation events or an EDR’s process creation events. Since Elastic utilizes field normalization using their ECS schema we can use those fields in this universal detection so data sources telemetry for a process path would be called process.executable and we can take advantage of that in our detections. There may be times were an integration doesn't include a mapping to ECS and you will have to create that mapping yourself. For example, in this detection the service file name (service path) doesn’t have a ECS mapping so we have to use its native field name. 
    
    </aside>
    
    Example of creating EQL detection:
    

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image%203.png)

1. Input the `Name` of the detection and `Description` in their respected textboxes and click on `Advanced settings`
    
    Name:
    
    ```bash
    Windows Random Service Name Created
    ```
    
    Description:
    
    ```bash
    This detection looks for when a new service is created with upper case letters and the service name is up to 9 characters long and when the service path includes a .bat or .exe file
    ```
    

Example:

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image%204.png)

1. Fill out the MITRE ATT&CK tactic, technique and subtechnique and click on `Continue` button
    1. Tactic: Persistence (TA0003)
    2. Technique: Create of Modify System Process (T1543)
    3. Subtechnique: Windows Service (T1543.003)

Example:

![image.png](Lab%201%201%20-%20Detect%20Random%20New%20Service/image%205.png)

1. Click on `Continue` button again and click on `Create & enable rule` button
2. Congratulations your first detection in this workshop 🥳

<aside>
🚧

Quick note, you will have to modify this rule later 😉

</aside>

---

## Next Steps

*What comes after this lab?*

- [Lab 1:2 - Detect DNS Raw IP](Lab%201%202%20-%20Detect%20DNS%20Raw%20IP%2025186e25cbe5802fb1afdac6a7817976.md)

---

## References

- https://www.elastic.co/docs/reference/query-languages/eql/eql-syntax
- https://www.elastic.co/docs/reference/ecs
- https://attack.mitre.org/techniques/T1543/003/

 © 2026 DEATHGroup Labs LLC. All rights reserved.