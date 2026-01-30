## Home Lab Security Project: Exposed Service Analysis and Hardening


## Overview
This home lab project demonstrates how to identify, analyze, and remediate exposed network services on a cloud-hosted Linux virtual machine. The focus is on understanding attack surface, interpreting port scan results, and applying defensive hardening practices aligned with real-world cybersecurity operations.
The lab uses a public-facing Ubuntu server to observe how services appear to external scanners and how firewalling and service configuration affect exposure.
________________________________________
## Objectives
•	Enumerate open and filtered ports on a public Linux VM
•	Understand the difference between open, closed, and filtered ports
•	Analyze a high-risk service (SMB / Samba)
•	Correlate Nmap results with local system state
•	Apply secure hardening practices
•	Validate remediation through rescanning
________________________________________
## Environment
•	Operating System: Ubuntu Server (cloud-hosted VM)
•	Access: SSH (port 22)
•	Tools Used:
o	Nmap (external scanning from Windows host)
o	ss (local port inspection)
o	systemctl (service management)
o	UFW (host-based firewall)
o	Cloud provider firewall (network perimeter)
________________________________________
## Initial Reconnaissance
An external Nmap scan was performed against the VM’s public IP address:
•	Result: Only port 22/tcp (SSH) appeared open
•	Port 445/tcp appeared as filtered with service identified as Microsoft-DS
This indicated that while SMB traffic was recognized by Nmap, it was not externally reachable.
________________________________________
Service Analysis: SMB / Samba
On the Linux host, Samba (smbd) was installed and running. Local inspection showed: - The SMB daemon was active - Port 445 was listening only on local or internal interfaces - No public exposure was present
Important clarification: - smbd is the Linux daemon providing SMB - Microsoft-DS is the standardized service name Nmap associates with port 445 - Nmap reports protocol/service type, not OS-level daemon names
________________________________________
Firewall and Network Controls
Multiple layers of filtering were identified:
1.	Host firewall (UFW): Blocking inbound SMB by default
2.	Service binding: SMB not bound to the public interface
3.	Cloud firewall: No inbound rule allowing TCP 445
Because packets were silently dropped, Nmap correctly reported the port state as filtered.
________________________________________
## Risk Assessment
SMB (port 445) is considered a high-risk service when exposed publicly due to: - Extensive history of critical vulnerabilities - Frequent scanning by attackers - Use in lateral movement and ransomware campaigns
Best practice dictates that SMB should never be exposed directly to the internet.
________________________________________
## Remediation Steps
To fully harden the system, the following actions were taken:
•	Stopped and disabled Samba services:
o	smbd
o	nmbd
•	Removed any firewall rules permitting TCP 445
•	Confirmed no listening services on port 445 using ss
•	Ensured cloud firewall rules did not allow SMB traffic
________________________________________
## Validation
A follow-up external Nmap scan confirmed:
•	Port 22/tcp remained open for SSH
•	Port 445/tcp remained filtered, indicating no exposure
The filtered state was intentionally retained, as it provides less information to potential attackers than a closed state.
________________________________________
## Key Takeaways
•	Exposed services, not installed services, define attack surface
•	Nmap service labels are protocol-based, not OS-specific
•	Firewalls, service bindings, and cloud controls all affect exposure
•	A filtered port state is often preferable from a defensive standpoint
•	Default-secure configurations should be preserved unless a business need exists
________________________________________
## Skills Demonstrated
•	Network reconnaissance and interpretation
•	Linux service management
•	Firewall configuration and validation
•	Secure system hardening
•	Defensive security mindset
•	Clear documentation of security findings


________________________________________
## For Powershell and Nmap commands - see below

## Overview
This project demonstrates how to identify, analyze, and remediate exposed network services on a public-facing Linux virtual machine. The lab focuses on understanding attack surface, interpreting Nmap scan results, and applying layered defensive controls consistent with real-world cybersecurity best practices.

The target system is an Ubuntu Server VM accessible via a public IP address.

---

## Objectives
- Enumerate open and filtered ports on a public Linux VM
- Understand the difference between open, closed, and filtered ports
- Analyze SMB (port 445) exposure and risk
- Correlate external scan results with local system state
- Apply secure hardening practices
- Validate remediation through rescanning

---

## Environment
- **Target OS:** Ubuntu Server (cloud-hosted VM)
- **Public IP:** `155.138.175.251`
- **Client System:** Windows
- **Scanning Tool:** Nmap
- **Shells Used:** PowerShell (Windows), Bash (Linux)
- **Access Method:** SSH (port 22)

---

## Methodology

### 1. External Reconnaissance (PowerShell)
Nmap was executed from a Windows system using PowerShell to scan the public IP of the Linux VM.

powershell
nmap 155.138.175.251
nmap -p 445 155.138.175.251

________________________________________
## Initial Scan Results

Port 22/tcp (SSH) reported as open
Port 445/tcp reported as filtered
Service identified as Microsoft-DS
The filtered state indicates that packets are being blocked or dropped by a firewall, preventing Nmap from determining whether the port is open or closed.

________________________________________
## Local Service Inspection (Linux VM)

##On the Ubuntu server, local inspection was performed to determine whether SMB was installed or running.

systemctl status smbd
ss -tuln | grep 445

---
________________________________________
## Findings:

Samba (smbd) was installed
Port 445 was not exposed on the public interface
SMB traffic was restricted by firewall and/or service binding


________________________________________
## Service Analysis: SMB / Samba

smbd is the Linux daemon that provides SMB services
Microsoft-DS is the standardized service name Nmap associates with port 445
Nmap reports protocol/service types, not OS-specific daemon names
Even when Samba is installed, SMB may not be externally reachable if:
The service is not bound to a public interface
Host-based firewall rules block access
Cloud provider firewall rules restrict traffic

________________________________________
## Risk Assessment

SMB (TCP 445) is considered a high-risk service when exposed to the internet due to:
A long history of critical vulnerabilities
Frequent automated scanning by attackers
Use in ransomware and lateral movement attacks
Best practice dictates that SMB should never be publicly exposed.

________________________________________
## Remediation Steps

Stopped and disabled Samba services:
smbd
nmbd
Removed any firewall rules allowing inbound TCP 445
Verified no services were listening on port 445
Confirmed cloud firewall rules did not permit SMB access

________________________________________
## Conclusion
This project demonstrates a practical, real-world approach to identifying and reducing attack surface on a public Linux system. By combining external scanning with internal validation and layered defenses, the system was confirmed to follow secure-by-default principles expected in professional cybersecurity environments.
This lab reflects the same analysis and remediation workflow used by SOC analysts and cloud security engineers.
