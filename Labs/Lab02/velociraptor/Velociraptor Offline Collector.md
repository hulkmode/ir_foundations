# LAB 2:0 - Velociraptor Offline Collector

## Objective

*What will students achieve by the end of this lab?*

- Import Extra VR Artifacts
- Build a Velociraptor (VR) Offline Collector
- Parse collected forensic artifacts with KAPE

---

## Estimated Time

*Approximate time to complete the lab.*

- Example: 30 minutes

---

# Step-by-Step Instructions

In Incident Response we don’t always have the luxury of triaging a machine with all the bells and whistles for monitoring and response. In those instances we can turn to VR Offline Collector to gather the critical forensics artifacts without having to perform dead box forensics. Before we can arm our adhoc collection we need to ensure we have all of VR’s current capabilities. To unlock additional functionality that is outside Velociraptor’s code base, we can import additional `artifacts`. One of the artifacts that we want to utilize is `Windows.Triage.Targets`. Windows.Triage.Targets is a mirror of functionality that KAPE provides under its Target options but its built right into VR (pretty slick).

![image.png](Velociraptor%20Offline%20Collector/image.png)

Earlier we performed attacks against our Windows machine that has Windows Defender broken on it. Lets walk through the steps of generating our VR Offline collector and collecting some forensic artifacts.

# Step 1: Import Extra VR Artifacts

1. From our Windows VM, open up a PowerShell prompt and type the following to launch VR in standalone mode. This will allow us to build our offline collector through the UI. 

```powershell
C:\tools\Velociraptor\velociraptor-v0.76.1-windows-amd64.exe gui
```

<aside>
💡

VR provides the ability to create offline collectors via CLI using the `collector` argument and passing it the spec file for the options you want configured. 

</aside>

![image.png](Velociraptor%20Offline%20Collector/image%201.png)

1. Once VR starts loading a new browser window will open and may have to select proceed to view VR’s UI.
2. From the launch page select `Import Extra artifacts` .

![image.png](Velociraptor%20Offline%20Collector/image%202.png)

1. This will bring up a pop up window and you don’t have to search for anything just select `Launch` on the bottom right. This will kick off adding all the extra capabilities VR has to offer.

![image.png](Velociraptor%20Offline%20Collector/image%203.png)

# Step 2: Build & Configure VR Offline Collector

1. From the `Server Artifacts` tab select the paper airplane button to start building our offline collector.

![image.png](Velociraptor%20Offline%20Collector/image%204.png)

1. Type `triage.targets` in the search window and select Windows.Triage.Targets from the artifacts. Then click on `Configure Parameters` 

![image.png](Velociraptor%20Offline%20Collector/image%205.png)

1. Under `HighLevelTargets` select `_KapeTriage` from the options and select `Configure Collection`

![image.png](Velociraptor%20Offline%20Collector/image%206.png)

1. Under `Output format` select `CSV and JSON` from the options and select `Launch`. Lets be real JSON is cool for machines but CSV rocks for humans ;).

![image.png](Velociraptor%20Offline%20Collector/image%207.png)

1. Congratulations on building your first VR Offline Collector.

# Step 3: Download and Run VR Collector

1. From the `Server Artifacts` tab the flow for creating the offline collector will be visible. Click on it to see details about the flow.
2. Select the `Uploaded Files` tab and click on the exe name to download it.

![image.png](Velociraptor%20Offline%20Collector/image%208.png)

1. The browser might complain that the collector could be harmful, live dangerously and click on the `Keep` button. 

![image.png](Velociraptor%20Offline%20Collector\e8256114-125f-401e-a2cd-b1a8847cd152.png)

1. Execute our newly minted collector binary from `c:\users\administrator\downloads\`
2. SmartScreen really doesn’t want us to have any fun, so click on `More info`

![image.png](Velociraptor%20Offline%20Collector/image%209.png)

1. Click on `Run anyway` to finally run our collector

![image.png](Velociraptor%20Offline%20Collector/image%2010.png)

1. The collector will take several minutes to collect our forensic artifacts and once completed will generate a zip in `c:\users\administrator\downloads\` .
2. Press the `Enter` key when it finished to exit.

![image.png](Velociraptor%20Offline%20Collector/image%2011.png)

---

## Next Steps

*What comes after this lab?*

- [Lab 3:0 Endpoint KAPE Triage Data Analysis](/Labs/Lab03/kape/Endpoint-KAPE-Triage-Data-Analysis.md)

---

## References

- [https://docs.velociraptor.app/](https://docs.velociraptor.app/)

© 2026 DEATHGroup Labs LLC. All rights reserved.
