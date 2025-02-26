Adversarial Simulation & Detection

1️⃣ Checking Privileges on the Windows Host

Before executing privileged actions, verify the necessary permissions:
```bash
getprivs
```
2)2️⃣ Dumping LSASS Memory (Credential Dumping)

Attackers often target LSASS.exe to extract credentials using tools like Mimikatz. In this step, we simulate the attack using ProcDump:
```bash
procdump -ma lsass.exe -o lsass.dmp
```
This captures a full memory dump of lsass.exe.
In real-world attacks, this technique is used for stealing passwords.
EDR solutions (like Defender ATP, CrowdStrike, etc.) can detect this activity.

3️⃣ Detecting & Blocking Attacks with LimaCharlie
Step 1: Simulating a Ransomware Attack on Ubuntu

Attackers often try to delete Volume Shadow Copies to prevent system recovery. We simulate this attack:
```bash
sudo vssadmin delete shadows /all /quiet
```
This command attempts to delete all system restore points, which is a common ransomware behavior.

Step 2: Viewing LimaCharlie Telemetry

    Open the LimaCharlie Dashboard.
    Navigate to Telemetry Logs.
    Observe how LimaCharlie detects the shadow copy deletion attempt.

Step 3: Writing a Detection & Blocking Rule

Now, we create a custom detection rule in LimaCharlie to block this attack in the future.
Rule Example (LimaCharlie YAML Format)
```yaml
detect:
  name: "Block Shadow Copy Deletion"
  event: "process_create"
  condition:
    contains:
      command_line: "vssadmin delete shadows"
  action: "block"
```
What happens now?

✅ LimaCharlie detects the attack before execution.
✅ The block action prevents the Ubuntu machine from running the command again.
✅ The alert is logged for incident response.
    
