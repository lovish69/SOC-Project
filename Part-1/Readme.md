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
'''bash
ping -c 2 google.com
'''
![Virtal Machine](https://github.com/user-attachments/assets/4daf8e13-420c-47e5-80d6-163b38e2552e)

2️⃣ Setting Up Windows Virtual Machine
Step 1: Disable Windows Defender & Security Protections

    Disable Windows Defender manually:
        Go to Settings → Update & Security → Windows Security
        Turn off Real-time Protection
        Turn off Cloud-delivered Protection

    Disable Windows Defender via Group Policy Editor
        Open CMD and type:
        '''bash
        gpedit.msc
        '''

Navigate to:
'''bash
Computer Configuration → Administrative Templates → Windows Components → Microsoft Defender Antivirus
'''
    Double-click "Turn off Microsoft Defender Antivirus"
    Select "Enabled"

Disable Windows Defender via Registry Editor

    Open Command Prompt as Administrator and enter:
    '''bash
    REG ADD "hklm\software\policies\microsoft\windows defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
    '''
