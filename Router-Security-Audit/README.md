Lab Report: Home Router Security Audit & Verification
Analyst: Elias Zgheib

Date: February 14, 2026
Target Hardware: Netis WF2409E Router (Internal IP: 192.168.1.1)
# 1. Objective
To perform a comprehensive security audit of the home gateway, verify external "stealth" status, and identify any unnecessary internal services that could increase the local attack surface.
# 2. Phase 1: External Auditing & False Positive Analysis
During initial testing using a mobile hotspot and nmap, two ports (21,5060) appeared as "Open." Recognizing that cellular networks often use transparent proxies or CGNAT, I sought to verify these results using a neutral third-party source.
Tools Used: GRC ShieldsUP!
Analysis: GRC shieldsUP confirmed that the two ports are Filtered.
Conclusion: The initial "Open" ports were False Positives caused by the mobile carrier's network architecture. The router is correctly dropping all unsolicited external traffic (Stealth Mode).
# 3. Phase 2: Internal Service Discovery
I performed a deep scan of the router from within the network to identify which services are available to local users.
Command: sudo nmap -sS -sV -Pn 192.168.1.1
Duration: 998.93 seconds seconds (approx. 16 minutes)
Findings:
Port 53 (TCP/UDP): DNS Domain service is active.
Port 80 (TCP): HTTP Web Management interface is active.
# 4. Risk Assessment & Hardening
Management Interface: While Port 80 is open internally, it is Filtered externally(using  Nmap Port Scanner at HACKERTARGET.COM) . I verified that a strong, non-default administrative password is required to access this interface.
Backdoor Check: I specifically checked for known Netis backdoors (e.g., UDP 53413). The scan confirmed this port is not active, indicating a more secure firmware version or configuration.
UDP Stealth: The 16-minute scan duration confirms the router's firewall is effectively "ignoring" probes rather than rejecting them, making reconnaissance more difficult for a local attacker.
# 5. Final Conclusion
The Netis router is currently in a Hardened State. It successfully masks its presence from the public internet (WAN) while providing only essential services (DNS and Web Admin) to the local network (LAN). No critical vulnerabilities or unauthorized listening services were discovered during this audit.
