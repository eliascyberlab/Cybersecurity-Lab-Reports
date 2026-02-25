Lab Report: IoT Network Discovery & Isolation Audit

Analyst: Elias Zgheib

Date: February 25/2026

# 1. Objective:
The "Invisible" Device
I added a Chinese wireless heater switch (Tuya-based) to my home network. While it functioned in the Smart Life app, it was invisible to the router’s basic device list and standard Nmap scans. In a cybersecurity context, a hidden device is a "black box" that could be a pivot point for attackers.

![lan devices](/IoT-Network-Audit-Isolation/images/router`s_attached_devices.png)

# 2. Phase 1: Passive Reconnaissance (The "Listen" Phase)
Standard ping scans failed because many IoT devices are "stealthed" (ICMP disabled). I used tcpdump on my Linux VM (bridged mode) to listen for broadcast traffic, suspecting the device was "shouting" its presence rather than "listening."

The Discovery
Almost immediately, I captured a heartbeat packet. While common Tuya documentation suggests Port 6667, my device was broadcasting on UDP Port 49154.

Technical Note: This indicates the use of the Tuya "Pegasus" Discovery Protocol, which utilizes the dynamic port range (49152+) for high-speed device pairing and synchronization.

The Command:

sudo tcpdump -i any udp port 49154 -vv

![port 49154 udp](/IoT-Network-Audit-Isolation/images/UDP_49154_packets.png)

# 3. Phase 2: Active Scanning (Mapping the Attack Surface)
With the IP confirmed via passive sniffing, I used Nmap to find the local control ports. I bypassed the "ping" check (-Pn) to force a scan of the stealthed host.

The Command:


sudo nmap -sS sU -Pn -T4 192.168.0.101
The Finding:
Nmap confirmed Port 6668/tcp and port 49154/udp were open. port 6668/tcp is the local control port used by the Smart Life app for encrypted Command & Control (C2) handshakes, and port 49154/udp is used by the Chinese wireless heater switch to "shout" its presence to the network

![open ports](/IoT-Network-Audit-Isolation/images/Nmap_output_scan.png)

# 4. Phase 3: Security Remediation (Network Segmentation)
Leaving an unverified IoT device on a primary LAN allows for Lateral Movement. If the device's cloud server is compromised, an attacker could jump from the heater to my personal workstation.

The Solution: The "Sandbox"
I used my TP-Link Archer C20 ac750 to create an isolated Guest Network.

Hardening Steps:

Allow Guests to Access My Local Network: Disabled.

Guest Network Isolation: Enabled (Prevents the heater from communicating with other guest devices).

[INSERT SCREENSHOT 4]: TP-Link "Guest Network" settings page showing these options disabled/enabled.

# 5. Final Verification (Proving the Sandbox)
To verify the segmentation, I attempted to scan the heater's new IP from my main Ubuntu VM on the primary LAN.

The Result:
Nmap reported "Host seems down." Even though the heater remained fully functional via the Smart Life app (through the cloud), it could no longer "see" or be "seen" by my private devices. The network is now successfully segmented.



# 6. final conclusion
The "invisible" heater was a perfect example of how IoT devices can create hidden blind spots in a network. By using passive sniffing and targeted scanning, I was able to unmask a device that didn't want to be found.
The successful isolation of this device proves that you don't need expensive enterprise gear to secure a network—you just need the right methodology. My home network is now safer, and the "blast radius" of this IoT device is officially contained.
