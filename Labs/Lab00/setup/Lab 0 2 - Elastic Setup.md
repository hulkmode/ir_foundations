# Lab 0:2 - Elastic Setup

---

## Objective

*What will students achieve by the end of this lab?*

- Create Elastic account
- Install Agent
- Add Integrations
- Create Windows Data View
- First Query

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 30 minutes

---

# Step-by-Step Instructions

This lab will walk you through setting up Elasticsearch Cloud, installing and enrolling your first agent and installing integrations that we will utilize during the workshop.

# Step 1: Create Elastic account

1. Register and create an account at MetaCTF
    1. URL: [https://cloud.elastic.co/registration?elektra=cloud-trial&storm=hero](https://cloud.elastic.co/registration?elektra=cloud-trial&storm=hero)
    2. Signup with Google account
        
        <aside>
        💡
        
        Tip, you could create a new Google account  for the workshop and dispose after ([https://support.google.com/accounts/answer/27441?sjid=6002150341222092946-NA](https://support.google.com/accounts/answer/27441?sjid=6002150341222092946-NA))
        
        </aside>
        
    
    ![image.png](Lab%200%202%20-%20Elastic%20Setup/image.png)
    

1. Fill out name and company
    1. There are a series of questions select the following
        1. How familiar are you with Elastic
            1. Example: I am new to Elastic
        2. What’s your primary goal in using Elastic
            1. Training or academic purpose
        3. Which use case are you looking to try first?
            1. Elastic for Security
    
    ![image.png](Lab%200%202%20-%20Elastic%20Setup/image%201.png)
    
2. Which deployment type would you like?
    1. Elastic Cloud Serverless

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%202.png)

1. How will you use Elastic for Security
    1. SIEM and Security Analytics
2. Are you migrating from an existing SIEM?
    1. No
3. Lastly. where would you like your data stored?
    1. Cloud provider: Amazon Web Services
    2. Region: N. Virginia (us-east-1) or whatever is closer to you and click on `Launch`
    
    ![image.png](Lab%200%202%20-%20Elastic%20Setup/image%203.png)
    
4. Once clicked on Launch then your Elastic environment will be built and will take 3 to 5 minutes.

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%204.png)

---

# Step 2: Install Agent

## Next Steps

Now that you have your own Elasticsearch Cloud instance, we need to install an Elastic agent on your Windows VM. This will send that beautiful telemetry back to Elasticsearch and give you the ability manage and push configuration changes to your Windows VM via Elastic Fleet. 

1. From the Security Panel on the left hand side of Elastic, click on `More` button (three dots), `Assets` and then `Agents`

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%205.png)

1. Click on `Add agent` button to start the process of creating an agent installer

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%206.png)

1. Input the policy name `Workshop Policy`
    1. Uncheck `Collect system logs and metrics`
    2. Click on `Create policy` button

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%207.png)

1. Ensure `Enroll in Fleet` is selected
    1. From the drop down menu select `Windows_x86_64`
    2. Copy and paste the powershell script
        1. Tip: use the Clipboard icon to copy the script
        
        <aside>
        💡
        
        The PowerShell script that we copied from Elasticsearch will download and install the Elastic Agent with your Elasticsearch enrollment key. Once it enrolls, data will start being collected from the agent and will be sent to your Elasticsearch Cloud instance.
        
        </aside>
        

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%208.png)

1. On your Windows VM browser window, open an Administrator PowerShell terminal
    1. Paste the PowerShell script from Elastic into the PowerShell terminal and press enter
    2. You will be asked if you want to continue installing the Elastic agent, type `Y` and press enter
        
        
        ![image.png](Lab%200%202%20-%20Elastic%20Setup/image%209.png)
        
    3. Once it completes the installation and enrolls, look at your Elasticsearch Cloud browser window, you should now see two green checkmarks indicating your agent is enrolled and data is coming into your tenant.

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2010.png)

1. Woot! 

---

# Step 3: Add Integrations

## Next Steps

Now that we have an agent enrolled and sending data to Elasticsearch, lets add some integrations to help use collect data from the endpoint. You will to install the `Windows` and `System` integrations.

1. From the left side Security panel, click on `Data management` and click on `Integrations`

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2011.png)

1. Search for the Windows integration by inputting `windows` in the search bar and click on Windows integration

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2012.png)

1. Looking at the details of the integrations we see Sysmon is listed as part of the integrations configurations, but not the native Windows event logs. We will add those next.
    1. Click on the `Add Windows` button

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2013.png)

1. Set the following options below

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2014.png)

1. Click on `Existing hosts` and ensure the policy name we created earlier `Workshop Policy` is present. If not type it in and click on `Save and continue` 

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2015.png)

1. A windows will popup informing you are about to deploy this policy
    1. Click on `Save and deploy changes`
    2. This will push the integrations and its configuration to your enrolled agent.

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2016.png)

1. After clicking `Save and deploy changes` you should be on the Integrations page. Search for `system` in the search bar and click on the System integration.

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2017.png)

1. Click on `Add System` button

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2018.png)

1. Match your configurations to below screenshot

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2019.png)

1. Click on `Existing hosts` and ensure the policy name we created earlier `Workshop Policy` is present. If not type it in and click on `Save and continue` 

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2015.png)

1. A windows will popup informing you are about to deploy this policy
    1. Click on `Save and deploy changes`
    2. This will push the integrations and its configuration to your enrolled agent.

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2020.png)

---

# Step 4: Create Windows Data View

In Elastic you can setup different data views to narrow the amount of data your queries will search through. Typically you want to create data views by data source but there is nothing wrong with having several data views that mix and match data sources. Keep in mind when making a data view you can specify that data view in a detection rule, so an example of a data view data would mix and match data sources is including Sysmon telemetry with your EDR telemetry in one data view. In this step we will be creating a data view just for Windows telemetry which includes Sysmon.

1. Click on `Discover` in the top left under the Security panel  
    1. The Discover window will open and this is the area you will be performing your queries
2. Click on the current data view `Default security data view`
    1. A drop down window will appear showing all the available data views
3. Click on `Create a data view` 

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2021.png)

1. Input `Windows` in the Name textbox
    
    ```json
    Windows
    ```
    
2. Input `logs-windows*, logs-system*` into the Index pattern textbox
    
    ```json
    logs-windows*, logs-system*
    ```
    
3. Take a look at the matching data sources we have with the index pattern we used
    1. These data sources were created for us when we added the integrations earlier
4. Click on `Save data view to Kibana`

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2022.png)

1. Sweet now you have your own data view that is just targeting Windows data sources and when we create detections later we can use this data view.
    
    ![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2023.png)
    

# Step 5: First Query

If you are not too familiar with Elastic query languages (yes they have more than one 🤪), take a look at their documentation. I will get your first query out of the way if you are not too familiar with Elastic.

- https://www.elastic.co/docs/explore-analyze/discover/discover-get-started
- https://www.elastic.co/docs/reference/query-languages/esql/esql-syntax
- https://www.elastic.co/docs/reference/query-languages/eql/eql-syntax
- https://www.elastic.co/docs/reference/query-languages/kql
1. Since we are still in `Discover` and already using the `Windows` data view we just created, lets do our first query to see what our Sysmon data looks like so far.
2. Input our below query into the search bar
    
    Query:
    
    ```json
    event.provider: “Microsoft-Windows-Sysmon”
    ```
    

1. From the time picker select `Today` and `press enter` 
2. If everything is working properly, you should have a Document count
    1. In below screenshot there are 537 Documents (events) from Sysmon

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2024.png)

1. Search through the available fields on the left side of the `Discover` . Lets find our Event IDs real quick. Input the following into the field search bar. 

<aside>
💡

Event.code is incomplete below to help demonstrate the field search capabilities. It is a fuzzy match search bar so as long as the field name contains some of the letters it will show below

</aside>

Field name search:

```json
event.c
```

Example of field name search bar:

![image.png](Lab%200%202%20-%20Elastic%20Setup/image%2025.png)

1. Now see if you can add to our current query to only look at Event IDs of 1 which is our Sysmon Process Creation events.
2. If you want to know more about Elastic check out the links I referenced earlier. They are also down below in the references section
3. Aaah yeah lets get this party started!

![](https://media2.giphy.com/media/DhstvI3zZ598Nb1rFf/source.gif)

---

## Next Steps

*What comes after this lab?*

### Next IR Foundations Lab
- [Lab 1:0 - Advertisement to Access](/Labs/Lab01/metasploit/Lab%201%200%20-%20Advertisement%20to%20Access.md)


### Bonus Detection Engineering Labs 
- [BLab 1:0 - Sysmon Config](/Labs/Bonus/Detection%20Engineering/Lab%201%200%20-%20Sysmon%20Config.md)

---

## References

- Elastic Agent Install: https://www.elastic.co/docs/reference/fleet/install-fleet-managed-elastic-agent
- Elastic Windows Integration: https://www.elastic.co/docs/reference/integrations/windows
- Elastic System Integration: https://www.elastic.co/docs/reference/integrations/system
- https://www.elastic.co/docs/explore-analyze/discover/discover-get-started
- https://www.elastic.co/docs/reference/query-languages/esql/esql-syntax
- https://www.elastic.co/docs/reference/query-languages/eql/eql-syntax
- https://www.elastic.co/docs/reference/query-languages/kql

 © 2026 DEATHGroup Labs LLC. All rights reserved.
