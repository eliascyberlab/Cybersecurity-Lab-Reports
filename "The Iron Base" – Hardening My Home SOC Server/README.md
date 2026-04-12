Lab Report: hardening my Home SOC server

Analyst: Elias Zgheib

Date: April 9,2026

# 1. Objective

In this project, I transformed an old Toshiba laptop into a dedicated security server. My goal was to create a "headless" Linux environment that is secure by design, using industry-standard hardening techniques before deploying any security monitoring tools.

# 2. Phase:1 Physical to Headless Conversion

The Goal: Make the server operate like a professional data center node.

What I did: Installed Ubuntu Server 24.04 LTS and configured the systemd power management settings.

Why: By setting the server to "ignore" the lid switch, I can close the laptop and store it on a shelf while it continues to run 24/7. This maximizes physical space and mimics how real servers are managed remotely.

📸 Post Screenshot 1 here: Your physical photo of the Toshiba laptop setup.

Step 2: Remote Management (The Secure Bridge)
The Goal: Establish a secure management connection.

What I did: Configured OpenSSH and established a remote session from my Kali Linux machine.

Why: Managing a server directly from its own keyboard is rare in the professional world. By using SSH, I can manage my infrastructure from anywhere on the network securely.

📸 Post Screenshot 2 here: The screenshot of your Kali terminal showing "Welcome to Ubuntu" after you logged in via SSH.

Step 3: Network Hardening (UFW)
The Goal: Apply the Principle of Least Privilege.

What I did: Configured the Uncomplicated Firewall (UFW) to a "Default Deny" posture. I explicitly opened only the ports required for SSH (22), SIEM dashboards (443), and security agents (1514/1515).

Why: A server should only "speak" when necessary. By blocking all incoming traffic by default, I significantly reduced the attack surface of my home lab.

📸 Post Screenshot 3 here: The command list where you allowed the specific ports (22, 443, etc.).
📸 Post Screenshot 4 here: The output of sudo ufw status verbose showing the firewall is ACTIVE.

Step 4: Automated Defense (Fail2Ban)
The Goal: Implement automated incident response.

What I did: Installed and initialized Fail2Ban.

Why: While the firewall blocks closed ports, Fail2Ban protects the open ones. It monitors system logs for "Brute Force" patterns (like repeated failed logins) and automatically bans the offending IP address. This adds a layer of "intelligence" to my server's defense.

📸 Post Screenshot 5 here: The terminal screen showing the successful installation of Fail2Ban.

Summary of Skills Demonstrated:
Linux System Administration: Configured system services and power management.

Network Security: Built a custom firewall policy.

Infrastructure as a Service: Built a "headless" server environment.

Defensive Security: Implemented automated intrusion prevention.
