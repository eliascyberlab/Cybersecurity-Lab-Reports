Lab Report: Ubuntu System Hardening & Attack Surface Reduction
Date: February 14, 2026
Analyst: Elias Zgheib
Objective: Perform a baseline security audit of an Ubuntu 64-bit VM and reduce the attack surface by disabling non-essential services.

# 1. Executive Summary
During a routine security audit of a newly deployed Ubuntu VM, I identified an active print-related service (cupsd) listening on a network port. Given that this VM is a dedicated cybersecurity lab environment, printing capabilities are unnecessary. To align with the Principle of Least Privilege, I successfully neutralized this service to mitigate potential remote code execution (RCE) risks.

# 2. Methodology & Discovery
I used the ss (socket statistics) tool to inspect all active and listening network connections on the system.
Finding: The output revealed cupsd (Common Unix Printing System) listening on port 631.
Risk Assessment: CUPS has been associated with critical vulnerabilities, such as CVE-2024-47176, which could allow unauthenticated attackers to execute commands remotely. Keeping this service active provides an unnecessary entry point for an attacker.

# 3. Remediation (Hardening Steps)
To secure the system, I executed the following commands to stop the service immediately and prevent it from starting during future reboots:
Stop the service: sudo systemctl stop cups
Disable the service: sudo systemctl disable cups

# 4. Verification & Results

After performing the hardening steps, I re-ran the security scan to confirm the service was no longer active.
Result: The verification scan confirms that the system no longer listens on port 631, successfully reducing the VM's attack surface.

![Final Scan](../images/final_scan.png)
# 5. Conclusion
This exercise demonstrates the importance of Attack Surface Reduction. By identifying and disabling unused services, I reduced the system's vulnerability to known RCE exploits. This proactive approach is a foundational step in maintaining a hardened and secure computing environment.
