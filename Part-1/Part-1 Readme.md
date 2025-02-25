##Set up your virtual environment
1) Download and install Ubuntu live server.
2) Download and install windows11 iso.
I am specifying the SERVER version of Ubuntu because it comes preinstalled with necessary packages. If you choose the Desktop flavor, you will have issues, and you are wasting unnecessary resources.
3)In the Ubuntu machine set username/password (this is just a lab)
-Your name: user
Your server’s name: attack
-Username: user
-Password: password
4)Install OpenSSH server?
-check/yes
5)Continue installing OS until “Install complete!”
6)After the reboot, let’s perform a quick connectivity check.
Logon with the credentials we defined during install
-Username: user
-Password: password
Make sure DNS and outbound pings are working with the command-ping -c 2 google.com

![Virtal Machine](https://github.com/user-attachments/assets/4daf8e13-420c-47e5-80d6-163b38e2552e)

##Set up your Windows VM
1)Disable the antivirus settings on windows
a)Turn off all the protection and windows defender
2)Permanently Disable Defender via Group Policy Editor
a)In search bar open "CMD" and type the command- gpedit.msc(which opens the registry editor)
3)Inside the Local Group Policy Editor
Click Computer Configuration > Administrative Templates > Windows Components > Microsoft Defender Antivirus
Double-click “Turn off Microsoft Defender Antivirus”
Select “Enabled”
4)Permanently Disable Defender via Registry
1)From the same administrative command prompt we previously opened, copy/paste this command and press Enter
REG ADD "hklm\software\policies\microsoft\windows defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f

5)Prepare to boot into Safe Mode to disable all Defender services
a)Click the “Start” menu icon
b)type “msconfig” into the search bar within the Start Menu
c)Go to “Boot” tab and select “Boot Options”
d)Check the box for “Safe boot” and “Minimal”

6)Now, in Safe Mode, we’ll disable some services via the Registry

a)Click the “Start” menu icon
b)Type “regedit” into the search bar and hit Enter
c)For each of the following registry locations, you’ll need to browse to the key, find the “Start” value, and change it to 4

![Registry](https://github.com/user-attachments/assets/455f0ef5-9746-4cd4-892b-0b3dead1b943)

##Install LimaCharlie EDR on Windows VM
LimaCharlie is a very powerful “SecOps Cloud Platform”. It not only comes with a cross-platform EDR agent, but also handles all of the log shipping/ingestion and has a threat detection engine.

1)Create a free LimaCharlie account.
2)Once logged into LimaCharlie, create an organization
![Organization](https://github.com/user-attachments/assets/fe59b417-ef50-4f0e-83fb-fdc7fd6b4d3d)

a)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Sense
b)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdBoot
c)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend
d)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisDrv
e)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisSvc
f)Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdFilter

7)Leave Safe Mode the same way we got into it
a)Click the “Start” menu icon
b)type “msconfig” into the search bar within the Start Menu
c)Go to “Boot” tab and select “Boot Options”
d)Uncheck the box for “Safe boot”
e)Click Apply and OK
f)System will restart into normal desktop environment, now (hopefully) Defender-free.

##Installing Sysmon on Windows VM
Sysmon, short for System Monitor, is a powerful Windows system service and device driver developed by Microsoft's Sysinternals team. It monitors and logs system activity to the Windows event log, providing detailed information about process creations, network connections, file modifications, and more.

1)Launch an Administrative PowerShell console for the following commands
Click the “Start” menu icon
Type “powershell” into the search bar within the Start Menu
Right+Click “Windows PowerShell” and click “Run as administrator”

2)Download Sysmon with the following command
-Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile C:\Windows\Temp\Sysmon.zip

3)Unzip the sysmon
-Expand-Archive -LiteralPath C:\Windows\Temp\Sysmon.zip -DestinationPath C:\Windows\Temp\Sysmon

4)Download SwiftOnSecurity’s Sysmon config.
-Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile ----C:\Windows\Temp\Sysmon\sysmonconfig.xml

5)Install Sysmon with Swift’s config
-C:\Windows\Temp\Sysmon\Sysmon64.exe -accepteula -i C:\Windows\Temp\Sysmon\sysmonconfig.xml

![sysmon](https://github.com/user-attachments/assets/370e02fb-65d7-43e9-aac7-e52ddd76425d)

6)Validate Sysmon64 service is installed and running
-Get-Service Sysmon64

7)Check for the presence of Sysmon Event Logs
-Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 10
![Event](https://github.com/user-attachments/assets/45c88ade-1b74-44c3-b4fb-9ca653af3506)

8)Once the org is created, click “Add Sensor”
![Screenshot (651)](https://github.com/user-attachments/assets/2987fbe3-bf25-4cec-8732-5fbd8973263d)

a)Select Windows
b)Provide a description such as: Windows VM - Lab
c)Click Create
d)Select the Installation Key we just created
e)Specify the x86-64 (.exe) sensor, but then skip ahead to my instructions versus the ones provided.

![Sensor](https://github.com/user-attachments/assets/94b3cf5f-1277-46ea-8088-4f2aa1153ddf)

9)IN THE WINDOWS VM, open an Administrative PowerShell prompt and paste the following commands:
cd C:\Users\User\Downloads
Invoke-WebRequest -Uri https://downloads.limacharlie.io/sensor/windows/64 -Outfile C:\Users\User\Downloads\lc_sensor.exe

10)Next, we will copy the install command provided by LimaCharlie which contains the installation key. Paste this command into your open terminal.

![Key](https://github.com/user-attachments/assets/a69014af-150e-46f5-bc3d-db4a42e99648)

11)This is the expected output

![output](https://github.com/user-attachments/assets/36281f9f-153d-4b36-86fc-0273367eb4a1)
After this in the LimaCharlie UI interface you will see a machine is detected and click on finish  and follow the next step

12)Now let’s configure LimaCharlie to also ship the Sysmon event logs alongside its own EDR telemetry

a)In the left-side menu, click “Artifact Collection”
b)Next to “Artifact Collection Rules” click “Add Rule”
c)Name: windows-sysmon-logs
d)Platforms: Windows
e)Path Pattern: wel://Microsoft-Windows-Sysmon/Operational:*
f)Retention Period: 10
g)Click “Save Rule”

LimaCharlie will now start shipping Sysmon logs which provide a wealth of EDR-like telemetry, some of which is redundant to LC’s own telemetry, but Sysmon is still a very power visibility tool that runs well alongside any EDR agent.

##Setup attack system
I highly recommend using an SSH client to access the Ubuntu VM so that you can easily copy/paste commands. MacOS/Linux/Modern Windows systems have builtin SSH abilities, but there are third party tools for this as well.

1)In your Ubuntu VM login with your credential and type the following command
-ip a
This command list your ethernet ip address 

2)Use this Ip address to SSH onto your VM machine with host system
-ssh user@[Linux_VM_IP](this command is used to SSH your VM machine)

3)after loggin its time to drop into root shell with the following command
-sudo su

4)Run the following commands to download Sliver, a Command & Control (C2) framework by BishopFox. I recommend copy/pasting the entire block as there is line-wrapping occurring.
-wget https://github.com/BishopFox/sliver/releases/download/v1.5.34/sliver-server_linux -O /usr/local/bin/sliver-server
-chmod +x /usr/local/bin/sliver-server
-apt install -y mingw-w64

5)Now let’s create a working directory we’ll use in future steps
-mkdir -p /opt/sliver

In part 2 we will go deeper with the help of Command and Control tool silver and will detect some logs with help of LimaCharlie and SysMOn.








