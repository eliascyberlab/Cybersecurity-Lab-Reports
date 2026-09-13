Defending the Edge: Setting Up and Hardening a Multi-Platform SIEM/XDR Security System.

Project Overview1.1

ObjectiveThe goal of this project was to set up a central security monitoring system using a SIEM/XDR architecture (Security Information and Event Management / Extended Detection and Response). This system acts as a central "security brain," collecting security data and monitoring threats across several different computers and devices on a network.

1.2 The Lab Setup
This project builds on foundations from the Google Cybersecurity Professional Certificate and defensive training from the TryHackMe Junior Penetration Tester path.

The testing lab mimics a real business network using these operating systems:Central Security Server: Debian 13 Linux (a clean, minimal server setup)

Device 1: Windows 11 Pro (acts as the main virtual machine host)
Device 2: Windows 11 Home (represents a remote employee workstation)
Device 3: Kali Linux (used for security testing and ethical hacking)

2. Executive Summary

This report shows exactly how the central security system was installed, secured, and connected to target devices.
First, the core system was built inside an isolated, lightweight Linux virtual environment. To prevent network drops, the server's network settings were changed from a temporary address to a permanent, static IP address. This ensures that even if the power goes out or the system reboots, the server never changes its location on the network.

Next, a built-in firewall (UFW) was locked down to a strict "deny-by-default" setting. This blocks all outside traffic except for the exact, secure lanes needed for management and data collection.

Finally, three distinct devices were connected to the system. To prove the system works, out-of-date apps were tracked down on the network, isolated, and updated—successfully bringing all security threat counters back down to a safe zero baseline.


3. Server Setup and Initialization

3.1 The Virtual Environment
The central security manager was set up inside an isolated sandbox using VMware Workstation. Running it on a minimal Debian 13 Linux base keeps the server fast, leaving plenty of system memory for sorting data, indexing logs, and running the secure web dashboard.

3.2 Downloading the InstallerTo begin, the official, all-in-one installation script was downloaded directly from the vendor's secure distribution network:

curl -L -O https://packages.wazuh.com/4.14/wazuh-install.sh

3.3 Running the Installation
The installation script was run with an automated flag (-a), which handles the setup automatically. This script checked the system specifications, set up space for log files, recorded errors to an installation log (/var/log/wazuh-install.log), and bound the web console to secure port 443 (HTTPS):

sudo bash wazuh-install.sh -a

4. Hardening the Server Firewall

4.1 Locking Down the Ports (UFW)
To prevent unauthorized users from tampering with the security server, the firewall was reset and told to block all incoming network requests.
Specific exceptions were added to allow administrators to access the command line and to let monitored devices send their security logs safely:

# Reset the firewall to factory defaults
sudo ufw --force reset

# Block all incoming traffic, allow standard outgoing traffic
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Open port 22 (SSH) and ports 443, 1514, 1515 for secure log ingestion
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw allow 1514/tcp
sudo ufw allow 1515/tcp

# Turn the firewall on
sudo ufw enable

5. Setting a Permanent Static IP Address

5.1 Why a Static IP is Vital
Most home and business routers use DHCP, meaning they hand out network addresses randomly. If your server is on a dynamic lease, a simple system reboot or a brief power outage will cause the router to give it a brand-new IP address.
Because every monitored device on your network is programmed to send data to one specific IP address, a shifting address breaks the pipeline. The entire dashboard goes blind until you manually reconfigure every single device. A static IP locks the address permanently.

5.2 Finding the Active Network Card

ip route show | grep default

The output confirmed the server was using a physical network interface card named enp8s0 and its current address was 192.168.1.5

5.3 Locking the IP via Netplan

The network configuration file was opened for editing:

sudo nano /etc/netplan/50-cloud-init.yaml

The dynamic configuration was replaced with manual, unchangeable rules. The server was locked to 192.168.1.5, pointed to the router gateway (192.168.1.1), and given stable backup public DNS servers (Cloudflare 1.1.1.1 and Google 8.8.8.8).
The new settings were applied immediately:

sudo netplan apply

6. Testing Web Console Access
Once the network was locked down and stable, a status check confirmed that the database indexing engine was running smoothly:

sudo systemctl status wazuh-indexer

From a separate computer on the same network, a web browser successfully connected to the security manager dashboard using the secure web address: https://192.168.1.5.


7. Connecting My Devices (Agent Enrollment)

7.1 Device 1: The Hypervisor Host (win11-host-manager)
To start monitoring the network, the Agent Enrollment Wizard was opened on the dashboard. The operating system was set to Windows, the server's static IP address (192.168.1.5) was entered, and the device was named win11-host-manager.

The system automatically generated a custom PowerShell command. This command was copied and pasted into an administrative command prompt on the Windows 11 host computer to install the monitoring agent and secure the connection.

confirming the lines Connected to the server and Agent is now online

7.2 Device 2: The Second Windows Laptop (win11-workstation-02)
To expand the deployment testing, the exact same process was mirrored to establish telemetry tracking for a remote physical laptop workspace running Windows 11 Home.A new deployment string was generated via the setup wizard, and this asset was labeled win11-workstation-02. The tracking daemon package was then installed via an administrative terminal window. The endpoint successfully registered with the system.

7.3 Device 3: The Security Auditing Node (kali-cyberlab)
The endpoint registration pipeline was then extended to include a specialized Linux host running Kali Linux (kali-cyberlab).The tracking agent deployment engine was downloaded, updated with the server connection markers, and mapped to agent.id: 003. This successfully brought the staging environment infrastructure to three concurrently actively monitored security assets.


8. Vulnerability Detection & Active RemediationWith

all three endpoints actively streaming telemetry to the central server, the system's Vulnerability Detector engine began analyzing the lab's attack surface.

8.1 Cleaning Up Device 2: The Windows Workstation (win11-workstation-02)
When checking the dashboard for the second Windows laptop, the server flagged exactly 5 High-Severity Vulnerabilities (including CVE-2023-38931 and CVE-2025-8088).The dashboard tracked the root cause down to a single program: an old, unpatched version of WinRAR 6.11 (64-bit).To fix this risk, the workstation was logged into directly, the official WinRAR website was accessed, and the application was updated to the secure WinRAR 7.23 release. This single software update completely patched all 5 security holes, immediately dropping the machine's high-severity threat counter to zero.


8.2 Securing Device 3: The Kali Auditing Node (kali-cyberlab)
When the system scanned the specialized Kali Linux auditing machine, the central console flagged exactly 1 High-Severity Vulnerability under agent.id: 003.The dashboard specifically called out CVE-2026-44432 and pinpointed the culprit: an outdated python package library called urllib3.

During this update loop, the terminal confirmed that 334 packages could be upgraded, illustrating how quickly system components become outdated. Upgrading the specific Python component successfully updated the underlying code library.

9. Project Conclusion

This hands-on engineering deployment successfully proves how an isolated SIEM/XDR environment can be designed, hardened, and maintained to protect a diverse, multi-platform network.By mapping out a permanent static network layout and locking down the server firewall, the monitoring core was built to resist unexpected power cuts and network changes. More importantly, discovering the vulnerable WinRAR package on the Windows workstation and the outdated Python library on the Kali node shows the real-world value of this ecosystem. Instead of guessing where security risks are, the system gives defenders immediate visibility, allowing them to catch, track down, and patch network flaws before they can be exploited.






