# Lab 1:0 - Advertisement to Access

## Objective

*What will students achieve by the end of this lab?*

- Clone a  PC cleaner tool website
- Modify the HTML file to point to malicious payload
- Optionally use ChatGPT to enhance provided HTML

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 15 minutes

---

# Step-by-Step Instructions

*The core of the lab. Include command blocks, expected outcomes, and notes.*

# Step 1: Clone a PC cleaner tool website

<aside>
⛔

Since cloning a legitimate website without permission could cause some legality issues such as Copyright Infringement, Trademark Misuse, Terms of Service Violations, Computer Fraud and Abuse Laws, etc, I have created a safe website to clone for our workshop. 

</aside>

Lets get our clone on and duplicate a website that mimics Top PC cleaner tools ([Top PC Cleaners](https://www.pc-cleaner-comparison.com/lp/the-best-pc-optimizer-software)). Since we are simulating this we don’t have to worry about looking through these to decide which ones are free and are not behind a login. We are going to use a common tool that has been around a long time called `wget`. You might have used it to fetch files but you can use it to make a backup a site that you own. Run the following command to clone our website, but first lets take a look at our Public facing website.

1. On your Kali VM open Firefox and type in `www.cleanfastpc.com` and `press enter`to access the website. 
    1. There isn’t a hosted executable on the website so the download button doesn’t download anything. I made it more of a placeholder to make it easier for you to modify just like an attacker might have too. You will modify it by adding your Kali IP where your payload will be hosted. Additionally I didn’t want to host a fake binary in the wild if I didn’t have to. 
2. Now lets use `wget` to kick off our site cloning for our simulated malvertising (aka Drive-by Download) campaign. Run the following command. First it makes sure we are in the home directory `cd ~` since other commands are setup to look at your /home directory. Then it runs `wget` to clone the site.
    1. Commands:
        
        ```bash
        cd ~ && wget "https://www.cleanfastpc.com/" --mirror --convert-links --adjust-extension --page-requisites -e robots=off -U "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
        ```
        
    2. What are the switches doing?
    
    | Alias | Full Switch Name | Meaning |
    | --- | --- | --- |
    | `-m` | `--mirror` | Mirror mode — enables recursion, time-stamping, and other defaults for site copying. |
    | `-k` | `--convert-links` | Convert links so the site works locally. |
    | `-E` | `--adjust-extension` | Adjust file extensions based on content type (e.g., `.html`). |
    | `-p` | `--page-requisites` | Download all required assets (CSS, images, JS). |
    | *(none)* | `-e robots=off` | Ignore `robots.txt` rules. |
    | `-U` | `--user-agent` | Set a custom browser **User-Agent** string. |

Example output:

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image.png)

<aside>
💡

If you own your own website and want to backup your site you could  additionally use a tool called HTTrack to backup your website. HTTrack has a GUI or can be used via command line.

</aside>

---

# Step 2: Modify the HTML file to point to malicious payload

Once you’ve cloned our super cool site 😉 with `wget`, you’ll have a local copy of **`index.html`**. You will need to modify it so that the **"Download CleanFast PC"** button points to *your* payload.

1. Wget will download the files to a new directory based on the website name, so for us our files will be located under `/home/kali/www.cleanfastpc.com` . Lets use VSCode to open the directory and the files within `www.cleanfastpc.com`.
    1. On the Kali VM desktop, double click the shortcut icon for Visual Studio Code 
    
    ![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%201.png)
    
2. Go to File and select Open Folder

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%202.png)

1. Click on Home and select www.cleanfastpc.com

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%203.png)

1. Visual Code will now display all the files in www.cleanfastpc.com directory. `Click on index.html`

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%204.png)

1. After selecting the file `index.html` , go to `Edit` at the top of Visual Studio and select `Replace`
    1. If you rather keep your hands on your keyboard, you can `hold done ctrl and press H` to bring up the replace panel.

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%205.png)

1. For convenance and to show that Visual Studio provides you a terminal inside of it while you might be looking at files/code. Go to `View` at the top of Visual Studio and select `Terminal`

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%206.png)

1. In the new terminal, run `ifconfig eth0` to get your Kali VM IP

Command:

```bash
ifconfig eth0
```

Example output

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%207.png)

1. Alright we have what we need to make our modification to our `index.html` file now, lets make an update that benefits the attacker side of us. Copy and paste the following into their respected location to make our changes

1. Find Window (marked as 1 below screenshot):
    
    ```bash
    index.html#http://<Kali_VM_IP>
    ```
    

<aside>
💡

Use the IP from our ifconfig output in the Replace Window below

</aside>

b.  Replace Window (marked as 2 below screenshot):

```bash
http://<Kali_VM_IP>
```

c.  Click on the `Replace ALL button` (marked as 3 in below screenshot)

Below is an example of what the find and replace would look like

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%208.png)

1. Now lets save our changes, by going to `File` at the top of Visual Studio and select `Save`
    1. If you want to keep your hands on the keyboard, `hold done ctrl and press S` to save our changes.

![image.png](Lab%201%200%20-%20Advertisement%20to%20Access/image%209.png)

1. That is it for setting up our website to deliver our payload.

---

## Next Steps

*What comes after this lab?*

- [Lab 1:1 - Enter Meterpreter](Lab%201%201%20-%20Enter%20Meterpreter.md)

---

## References

- HTTrack: https://www.httrack.com/
- WGet: https://man7.org/linux/man-pages/man1/wget.1.html
- Visio Studio Overview: https://learn.microsoft.com/en-us/visualstudio/get-started/visual-studio-ide?view=vs-2022

 © 2026 DEATHGroup Labs LLC. All rights reserved.