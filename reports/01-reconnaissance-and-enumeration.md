# Report 01: Reconnaissance and Enumeration

## Objective

The objective of this phase was to verify connectivity to the Metasploitable2 target VM and perform baseline service enumeration from Kali Linux inside a private host-only VMware lab network.

## Scope

- Analyst VM: Kali Linux
- Target VM: Metasploitable2
- Target IP: 192.168.56.20
- Network Type: Private host-only VMware lab network

## Tools Used

- ping
- Nmap

## Methodology

1. Confirmed Kali Linux network configuration.
2. Confirmed connectivity to Metasploitable2 using ICMP ping.
3. Performed baseline TCP service enumeration with Nmap.
4. Saved scan output in normal, grepable, and XML formats for documentation.

## Evidence Files

- `evidence/raw/connectivity-ping.txt`
- `evidence/raw/kali-ip-addresses.txt`
- `evidence/raw/kali-routes.txt`
- `evidence/raw/metasploitable-baseline.nmap`
- `evidence/raw/metasploitable-baseline.gnmap`
- `evidence/raw/metasploitable-baseline.xml`

## Scan Summary

Nmap identified 23 open TCP ports on the Metasploitable2 target. The host was reachable with low latency, and service/version detection identified multiple network services, legacy protocols, database services, web services, and remote access services.

## Open Ports and Services

| Port | Service | Version / Notes | Initial Risk Observation |
|---|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 | File transfer service exposed; should be reviewed for version risk and anonymous/default access. |
| 22/tcp | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 | Remote administration service exposed; should be restricted and monitored. |
| 23/tcp | Telnet | Linux telnetd | Legacy cleartext remote login protocol; high risk if credentials are transmitted. |
| 25/tcp | SMTP | Postfix smtpd | Mail service exposed; should be reviewed for relay, enumeration, and configuration issues. |
| 53/tcp | DNS | ISC BIND 9.4.2 | DNS service exposed; should be reviewed for zone transfer and version-related risks. |
| 80/tcp | HTTP | Apache httpd 2.2.8 Ubuntu DAV/2 | Web service exposed; should be reviewed for web application and server configuration risks. |
| 111/tcp | RPC | rpcbind 2 | RPC service exposed; may reveal additional network services. |
| 139/tcp | NetBIOS-SSN | Samba smbd 3.X - 4.X | Windows file-sharing related service exposed; should be reviewed for SMB/Samba risks. |
| 445/tcp | SMB | Samba smbd 3.X - 4.X | SMB service exposed; should be reviewed for file sharing, permissions, and version risks. |
| 512/tcp | exec | netkit-rsh rexecd | Legacy remote execution service; high risk due to insecure design. |
| 513/tcp | login | rlogind | Legacy remote login service; high risk due to insecure design. |
| 514/tcp | shell | Netkit rshd | Legacy remote shell service; high risk due to insecure design. |
| 1099/tcp | Java RMI | GNU Classpath grmiregistry | Java remote method service exposed; should be reviewed for remote object exposure. |
| 1524/tcp | bindshell | Metasploitable root shell | Critical lab finding; exposed shell-like service should never exist on production systems. |
| 2049/tcp | NFS | NFS 2-4 | Network file system exposed; should be reviewed for exported shares and access controls. |
| 2121/tcp | FTP | ProFTPD 1.3.1 | Additional FTP service exposed; should be reviewed for version and access risks. |
| 3306/tcp | MySQL | MySQL 5.0.51a | Database service exposed; should be restricted and monitored. |
| 5432/tcp | PostgreSQL | PostgreSQL 8.3.x | Database service exposed; should be restricted and monitored. |
| 5900/tcp | VNC | VNC protocol 3.3 | Remote desktop service exposed; should require strong authentication and access restrictions. |
| 6000/tcp | X11 | Access denied | Graphical display service exposed; should generally not be reachable by untrusted systems. |
| 6667/tcp | IRC | UnrealIRCd | IRC service exposed; should be reviewed for version and configuration risks. |
| 8009/tcp | AJP13 | Apache JServ Protocol v1.3 | Application server connector exposed; should be restricted to trusted systems only. |
| 8180/tcp | HTTP | Apache Tomcat/Coyote JSP engine 1.1 | Tomcat web service exposed; should be reviewed for management interfaces and default configuration. |

## Initial Risk Themes

The scan suggests several major risk categories:

- Legacy remote access protocols are exposed.
- Multiple file transfer and file-sharing services are exposed.
- Web and application services are exposed.
- Database services are directly reachable.
- Remote administration and remote shell-style services are present.
- The target has a broad attack surface due to many open ports.

## MITRE ATT&CK Mapping

This activity aligns with the Discovery tactic.

- Tactic: Discovery
- Technique: Network Service Discovery
- Technique ID: T1046

## SOC Detection Opportunities

A SOC analyst could look for:

- A single host connecting to many ports on another host
- Sequential port connection attempts
- Repeated failed login attempts against exposed services
- Connections to legacy services such as Telnet, rlogin, rsh, or VNC
- Unexpected access to database services from non-administrative systems
- Connections to exposed administrative or shell-like services

## Defensive Recommendations

At a high level, a production system should:

- Disable unnecessary services.
- Remove legacy cleartext protocols.
- Restrict remote administration services.
- Place databases behind proper network controls.
- Patch outdated software.
- Use host firewalls and network segmentation.
- Monitor authentication attempts and service access.
- Regularly perform vulnerability scanning and configuration review.

## Lessons Learned

This phase demonstrated how a baseline Nmap scan can quickly reveal the exposed services and overall attack surface of a target system. The results show why service enumeration is an important first step in both vulnerability assessment and SOC analysis. Before exploiting or validating any individual issue, an analyst should document scope, preserve evidence, and understand the services exposed by the target.
