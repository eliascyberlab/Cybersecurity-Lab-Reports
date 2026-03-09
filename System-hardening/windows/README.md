Analyst: Elias Zgheib

Date: March 8, 2026

Environment: Windows 10 (Home Lab)

# 1 Objective:
Identify active network listeners, verify system process integrity, and reduce the attack surface by disabling unnecessary services.


# 2. Methodology & Discovery

I utilized a customized PowerShell command to map active network connections directly to their owning processes. This provides a level of visibility beyond standard tools by identifying exactly which application is "listening" on which port.

Command Used:

powershell
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, @{Name="ProcessName";Expression={(Get-Process -Id $_.OwningProcess).ProcessName}} | Format-Table -AutoSize

Upon executing this command, I identified several high-risk active listeners and background services that were not required for my current operations:

a. Print Spooler (spoolsv): Active on Port 10565.

b. SMB Server (System): Active on Port 445.

c. Remote Access Tools: AnyDesk and TeamViewer were found running in an "Automatic" persistent state, maintaining listeners on Ports 7070 and 5939.




# 3. Remediation (Hardening Steps)
Based on the findings above, I initiated a hardening phase to terminate these listeners and close potential entry points for attackers.
A. Legacy Service Mitigation (Print & File Sharing)
Action: Terminated and Disabled the Print Spooler and Server (LANMAN) services.
Reasoning:
Spooler: Mitigates risks associated with "PrintNightmare" style vulnerabilities (CVE-2021-34527).
SMB (Port 445): Prevents unauthorized lateral movement and file discovery on the local network—a primary vector for ransomware.
B. Remote Access Tool (RAT) Hardening
Action: Transitioned AnyDesk and TeamViewer startup types from "Automatic" to Manual via services.msc.
Reasoning: Ensures these tools only run when explicitly launched by the user, preventing unauthorized background access and reducing persistent listening ports.
[INSERT SCREENSHOT 1 HERE]
Caption: Initial PowerShell scan identifying active Spooler (10565) and SMB (445) listeners before hardening actions were applied.
3. Process Integrity Audit: svchost.exe & explorer.exe
To ensure no malware was masquerading as a legitimate system process, I conducted a deep-dive audit of all active system binaries.
svchost.exe (Service Host): I manually audited all 80+ active instances. For every instance, I verified the file path (C:\Windows\System32) and the Digital Signature (Microsoft Windows). 100% of instances were confirmed legitimate.
explorer.exe: Verified running from C:\Windows with a valid signature. Outbound connections were cross-referenced and confirmed as standard Microsoft CDN traffic.
[INSERT SCREENSHOT 2 HERE]
Caption: Representative evidence of a verified 'Digital Signature' for a system process. This methodology was applied to every svchost instance on the system.
4. Final Verification (The "After" State)
A final execution of the PowerShell discovery command was performed to verify the success of the hardening actions.
Final Results:
Port 445 (SMB): REMOVED
Port 10565 (Spooler): REMOVED
AnyDesk/TeamViewer: Successfully transitioned to a Manual trigger state.
The system now operates with a significantly reduced attack surface, maintaining only essential communication channels.
Pro-Tip for your GitHub:
Once you post this, you can add a "Lessons Learned" section at the bottom. Mentioning that you are in Week 9 of the Google Cybersecurity Certificate shows you are effectively applying your coursework to real-world scenarios.

