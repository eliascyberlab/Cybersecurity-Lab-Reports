Analyst: Elias Zgheib

Date: March 8, 2026

Environment: Windows 10 (Home Lab)

# 1 Objective:
Identify active network listeners, verify system process integrity, and reduce the attack surface by disabling unnecessary services to establish a baseline 'Hardened State' for a Windows 10 home lab

# 2. Methodology & Discovery
I utilized a customized PowerShell command to map active network connections directly to their owning processes. This provides a level of visibility beyond standard tools by identifying exactly which application is "listening" on which port.

Command Used:

powershell Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, @{Name="ProcessName";Expression={(Get-Process -Id $_.OwningProcess).ProcessName}} | Format-Table -AutoSize

Upon executing this command, I identified several high-risk active listeners and background services that were not required for my current operations:

a. Print Spooler (spoolsv): Active on Port 49668

Risk assessment: The Print Spooler has a history of critical vulnerabilities, most notably PrintNightmare (CVE-2021-34527), which allows remote authenticated attackers to execute arbitrary code with SYSTEM privileges.

b. SMB Server (System): Active on Port 445.

Risk assessment: Ransomware (like WannaCry) uses SMB vulnerabilities to encrypt files not just on the infected machine, but on every reachable server and workstation on the network.

c. Remote Access Tools: AnyDesk and TeamViewer were found running in an "Automatic" persistent state, maintaining listeners on Ports 7070 and 5939.

Risk assessment: These tools are designed to be "firewall-friendly," meaning they use outgoing connections to bypass the router's security, and since these services start with Windows, they are always listening. If an attacker gains access to AnyDesk ID or TeamViewer credentials (via password leaks or "brute-forcing"), they can log in whenever they want.


![first scan](/System-hardening/windows/images/powershell_1st_scan.png)

# 3. Remediation (Hardening Steps)
Based on the findings above, I initiated a hardening phase to terminate these listeners and close potential entry points for attackers. A. Legacy Service Mitigation (Print & File Sharing) Action: Terminated and Disabled the Print Spooler and Server (LANMAN) services.


![spooler and server disabled](/System-hardening/windows/images/server_and_spooler_disabled.png)



B. Remote Access Tool (RAT) Hardening Action: Transitioned AnyDesk and TeamViewer startup types from "Automatic" to Manual via services.msc. Moving RATs to "Manual" breaks persistence. This forces a "Human-in-the-Loop" requirement



![any desk and teamviewer](/System-hardening/windows/images/anydesk_and_teamviewer_manual.png)


Process Integrity Audit: svchost.exe to ensure no malware was masquerading as a legitimate system process, I conducted a deep-dive audit of all active system binaries. svchost.exe (Service Host): I manually audited all active instances. For every instance, I verified the file path (C:\Windows\System32) and the Digital Signature (Microsoft Windows). 100% of instances were confirmed legitimate.


![svchost check](/System-hardening/windows/images/checking_svchost_filelocation.png)



This methodology was applied to every svchost instance on the system. 

# 4. Final Verification
(The "After" State) A final execution of the PowerShell discovery command was performed to verify the success of the hardening actions.
Final Results: Port 445 (SMB): REMOVED
Port 49668 (Spooler): REMOVED
AnyDesk/TeamViewer: Successfully transitioned to a Manual trigger state.

![final powerhsell scan](/System-hardening/windows/images/final_powershell_scan.png)



# 5.Conclusion
The system now operates with a significantly reduced attack surface, maintaining only essential communication channels. 

