1️⃣ Setting Up Your Lab Environment
Step 1: Setting Up Ubuntu Virtual Machine

    Download and install Ubuntu Live Server.

    Download and install Windows 11 ISO.
        The server version of Ubuntu is used because it includes necessary packages by default.
        The desktop version consumes unnecessary resources.

    During Ubuntu installation, set up:
        Your name: user
        Your server’s name: attack
        Username: user
        Password: password

    Enable OpenSSH Server (Check "Yes" during installation).

    Complete the installation and reboot.

    After reboot, log in using:

**Username**: user  
**Password**: password  

Check connectivity using:
ping -c 2 google.com

![Virtal Machine](https://github.com/user-attachments/assets/4daf8e13-420c-47e5-80d6-163b38e2552e)

2️⃣ Setting Up Windows Virtual Machine
Step 1: Disable Windows Defender & Security Protections

    Disable Windows Defender manually:
        Go to Settings → Update & Security → Windows Security
        Turn off Real-time Protection
        Turn off Cloud-delivered Protection

    Disable Windows Defender via Group Policy Editor
        Open CMD and type:
        gpedit.msc
       

Navigate to:
Computer Configuration → Administrative Templates → Windows Components → Microsoft Defender Antivirus

    Double-click "Turn off Microsoft Defender Antivirus"
    Select "Enabled"

Disable Windows Defender via Registry Editor

    Open Command Prompt as Administrator and enter:
    
    REG ADD "hklm\software\policies\microsoft\windows defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
    Boot into Safe Mode to disable Defender services completely

    Open Run (Win + R) → Type msconfig → Press Enter
    Go to Boot tab → Enable Safe boot → Minimal → Click Apply & OK

In Safe Mode, disable Defender services via Registry

    Open Registry Editor (regedit)
    Navigate to each of the following keys and change the "Start" value to 4:
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Sense  
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdBoot  
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend  
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisDrv  
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisSvc  
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdFilter  
![Registry](https://github.com/user-attachments/assets/455f0ef5-9746-4cd4-892b-0b3dead1b943)    

Exit Safe Mode by disabling Safe boot in msconfig.

3️⃣ Installing LimaCharlie EDR on Windows VM
What is LimaCharlie?

LimaCharlie is an Endpoint Detection & Response (EDR) tool that helps in log collection, threat detection, and security monitoring.
How to Install LimaCharlie on Windows?

    Create a free LimaCharlie account.
    Create a new organization.
![Organization](https://github.com/user-attachments/assets/fe59b417-ef50-4f0e-83fb-fdc7fd6b4d3d)
    Go to Add Sensor → Select Windows.
![Screenshot (651)](https://github.com/user-attachments/assets/2987fbe3-bf25-4cec-8732-5fbd8973263d)

a)Select Windows
b)Provide a description such as: Windows VM - Lab
c)Click Create
d)Select the Installation Key we just created
e)Specify the x86-64 (.exe) sensor, but then skip ahead to my instructions versus the ones provided.

![Sensor](https://github.com/user-attachments/assets/94b3cf5f-1277-46ea-8088-4f2aa1153ddf)

IN THE WINDOWS VM, open an Administrative PowerShell prompt and paste the following commands:
cd ~/Downloads
wget -O C:\Users\User\Downloads\lc_sensor.exe https://downloads.limacharlie.io/sensor/windows/64

Next, we will copy the install command provided by LimaCharlie which contains the installation key. Paste this command into your open terminal.

![Key](https://github.com/user-attachments/assets/a69014af-150e-46f5-bc3d-db4a42e99648)

This is the expected output

![output](https://github.com/user-attachments/assets/36281f9f-153d-4b36-86fc-0273367eb4a1)
After this in the LimaCharlie UI interface you will see a machine is detected and click on finish  and follow the next step

Now let’s configure LimaCharlie to also ship the Sysmon event logs alongside its own EDR telemetry

a)In the left-side menu, click “Artifact Collection”
b)Next to “Artifact Collection Rules” click “Add Rule”
c)Name: windows-sysmon-logs
d)Platforms: Windows
e)Path Pattern: wel://Microsoft-Windows-Sysmon/Operational:*
f)Retention Period: 10
g)Click “Save Rule”

LimaCharlie will now start shipping Sysmon logs which provide a wealth of EDR-like telemetry, some of which is redundant to LC’s own telemetry, but Sysmon is still a very power visibility tool that runs well alongside any EDR agent.

4️⃣ Installing Sysmon on Windows VM
What is Sysmon?

Sysmon (System Monitor) is a Windows logging tool that provides detailed logs on:
✅ Process Creation
✅ Network Connections
✅ File Modifications
How to Install Sysmon?

    Open PowerShell as Administrator
Download Sysmon:
    Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile C:\Windows\Temp\Sysmon.zip
Unzip Sysmon:
    Expand-Archive -LiteralPath C:\Windows\Temp\Sysmon.zip -DestinationPath C:\Windows\Temp\Sysmon
    Download Sysmon Configuration:
    Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile 
    C:\Windows\Temp\Sysmon\sysmonconfig.xml
    Install Sysmon with the config:
    C:\Windows\Temp\Sysmon\Sysmon64.exe -accepteula -i C:\Windows\Temp\Sysmon\sysmonconfig.xml
    Verify Sysmon is running:
    Get-Service Sysmon64
    Check if Sysmon is logging:
    Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 10

![sysmon](https://github.com/user-attachments/assets/370e02fb-65d7-43e9-aac7-e52ddd76425d)

![Event](https://github.com/user-attachments/assets/45c88ade-1b74-44c3-b4fb-9ca653af3506)

5️⃣ Installing Silver C2 Framework

Silver is a Command & Control (C2) Framework used for red teaming and adversary simulation.
How to Install Silver on Ubuntu?



    
