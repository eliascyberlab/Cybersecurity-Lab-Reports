Lab Report: Secure Nextcloud Deployment and Validation
Overview
This project covers the full setup and security hardening of a self-hosted Nextcloud server. The deployment includes initial server installation, TLS transport encryption, mobile app integration, packet analysis verification, and server-side storage encryption.
1. Nextcloud Server Installation & Initial Account Setup
The Nextcloud server was deployed on the local machine. During the initial initialization phase, the core administrative user account and password were established to secure management access.
 Configuration: Admin credentials were generated and verified.
 [INSERT SCREENSHOT 1 HERE]
 Suggested Caption: Figure 1: Initial Nextcloud setup and admin account creation.
2. Server IP & Network Domain Binding
To allow local network devices to communicate with the instance, the static local IP address of the server (⁠192.168.1.4⁠) was added to the ⁠trusted_domains⁠ array within the Nextcloud ⁠config.php⁠ file.
 Configuration: Bound ⁠192.168.1.4⁠ as a trusted domain to enable internal network routing.
3. TLS Certificate Generation & Web Server Hardening
To secure traffic in transit and prevent plaintext credential exposure, a TLS certificate was generated and signed on the server. The web server configuration was updated to enforce HTTPS across all incoming connections.
 [INSERT SCREENSHOT 2 HERE]
 Suggested Caption: Figure 2: TLS certificate generated and applied to web server configuration.
4. Browser Verification (Kali Linux & Firefox)
Access to the Nextcloud interface was tested using Firefox on Kali Linux over HTTPS (⁠[https://192.168.1.4](https://192.168.1.4)⁠).
 Verification: Logged into the administrator account and verified the security lock icon in the browser URL bar, confirming an encrypted TLS connection.
 [INSERT SCREENSHOT 3 HERE]
 Suggested Caption: Figure 3: Secure browser session with visible TLS lock icon on Kali Linux.
5. Mobile Synchronization (iPhone Integration)
The official Nextcloud application was installed on the iPhone. Connection to ⁠[https://192.168.1.4](https://192.168.1.4)⁠ was established, and credentials were verified without network errors.
 Verification: Successfully authenticated and accessed the Nextcloud file directory on the iOS app.
 [INSERT SCREENSHOT 4 HERE]
 Suggested Caption: Figure 4: Authenticated Nextcloud mobile app session on iOS.
6. Traffic & Packet Analysis via Wireshark
To independently verify transport encryption, a live packet capture was conducted using Wireshark during active file requests and app sync operations.
 Verification: Captured network frames showed all data payloads encapsulated under TLS/SSL protocols. No plaintext HTTP data or sensitive payload content was visible across the network stream.
 [INSERT SCREENSHOT 5 HERE]
 Suggested Caption: Figure 5: Wireshark packet capture confirming encrypted TLS traffic.
7. Server-Side Data-at-Rest Encryption
To protect data at rest against physical drive access or theft, server-side encryption was configured via the Nextcloud administrative settings panel.
 Configuration: Enabled the Default encryption module under Apps management and toggled Enable server-side encryption under Administration Security settings.
 [INSERT SCREENSHOT 6 HERE] (Use the screenshot you captured from the admin panel)
 Suggested Caption: Figure 6: Server-side encryption enabled in Nextcloud Admin settings.
Conclusion
This lab successfully implemented, hardened, and verified a self-hosted Nextcloud server environment. By systematically applying security controls at multiple layers, the infrastructure achieves full end-to-end data protection:
 Transport Layer Security: The implementation of custom TLS certificates ensures that all network communications—whether originating from local web browsers or mobile applications—are fully encrypted in transit. This was independently validated via packet capture analysis in Wireshark, which confirmed that sensitive user data and authentication payloads remain secure against local network eavesdropping.
 Storage Security: Enforcing Nextcloud’s Default Encryption Module guarantees data protection at rest, mitigating the risk of unauthorized data access in the event of physical disk loss or host storage compromise.
 Operational Readiness: Integrating the system with the Nextcloud iOS client verified that security hardening can be achieved without sacrificing cross-platform accessibility or mobile user experience.
Overall, the deployed architecture satisfies core cybersecurity principles (Confidentiality, Integrity, and Availability) and serves as a resilient, secure foundation for private cloud storage.
