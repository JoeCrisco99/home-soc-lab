# Remote Access and Database Service Enumeration

## Objective

The objective of this phase is to perform safe, non-destructive enumeration of remote access and database services discovered during the baseline Nmap scan. This report focuses on identifying service exposure, visible banners, authentication surfaces, legacy protocols, database availability, and SOC-relevant detection opportunities.

## Scope

| Item          | Details                                                 |
| ------------- | ------------------------------------------------------- |
| Analyst VM    | Kali Linux                                              |
| Target VM     | Metasploitable2                                         |
| Target IP     | 192.168.56.20                                         |
| Network       | Private VMware host-only lab                            |
| Authorization | Private lab systems owned and controlled by the analyst |

## Target Services for This Phase

| Port     | Service    | Purpose                                          |
| -------- | ---------- | ------------------------------------------------ |
| 21/tcp   | FTP        | File transfer service enumeration                |
| 22/tcp   | SSH        | Secure remote administration service enumeration |
| 23/tcp   | Telnet     | Legacy remote access service enumeration         |
| 3306/tcp | MySQL      | Database service enumeration                     |
| 5432/tcp | PostgreSQL | Database service enumeration                     |
| 5900/tcp | VNC        | Remote desktop service enumeration               |

## Methodology

The enumeration process will use safe checks such as:

* Confirming service availability
* Capturing visible service banners
* Reviewing protocol exposure
* Identifying legacy or cleartext services
* Recording evidence output
* Documenting defensive risk observations
* Identifying SOC detection opportunities
* Writing remediation recommendations

This phase does not include brute-force attacks, password spraying, credential theft, destructive testing, malware deployment, or testing against any system outside the private lab.

## Evidence Files

Evidence for this phase will be saved in the evidence/raw directory.

| Evidence File                             | Description                                                        |
| ----------------------------------------- | ------------------------------------------------------------------ |
| evidence/raw/remote-db-targeted-nmap.txt  | Targeted Nmap output for selected remote access and database ports |
| evidence/raw/ftp-banner.txt               | FTP banner and initial service response                            |
| evidence/raw/ssh-banner.txt               | SSH banner response                                                |
| evidence/raw/telnet-banner.txt            | Telnet banner and initial service response                         |
| evidence/raw/mysql-service-check.txt      | MySQL service response and availability notes                      |
| evidence/raw/postgresql-service-check.txt | PostgreSQL service response and availability notes                 |
| evidence/raw/vnc-service-check.txt        | VNC service response and availability notes                        |

## Findings

### Finding 1: FTP Service Exposed on Port 21

Port 21 is open and running vsftpd 2.3.4. The service returned a visible FTP banner during initial connection.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/ftp-banner.txt

**Observed Details:**

* Port: 21/tcp
* Service: FTP
* Version: vsftpd 2.3.4
* Banner: 220 (vsFTPd 2.3.4)

**Risk Observation:**

FTP is commonly considered a legacy file transfer protocol because it can expose sensitive data or credentials if not protected properly. In a production environment, FTP should be replaced with encrypted alternatives or restricted to trusted networks only.

### Finding 2: SSH Service Exposed on Port 22

Port 22 is open and running OpenSSH 4.7p1 Debian 8ubuntu1. The SSH banner was visible during initial connection.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/ssh-banner.txt

**Observed Details:**

* Port: 22/tcp
* Service: SSH
* Version: OpenSSH 4.7p1 Debian 8ubuntu1
* Banner: SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1

**Risk Observation:**

SSH is a common remote administration service and should be carefully protected. In a production environment, exposed SSH services should use strong authentication, access controls, logging, patching, and network restrictions.

### Finding 3: Telnet Service Exposed on Port 23

Port 23 is open and running a Linux Telnet service. The service responded to an initial connection attempt.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/telnet-banner.txt

**Observed Details:**

* Port: 23/tcp
* Service: Telnet
* Version: Linux telnetd

**Risk Observation:**

Telnet is a legacy cleartext remote access protocol. In a production environment, Telnet should be disabled because credentials and session data may be transmitted without encryption. SSH should be used instead.

### Finding 4: MySQL Database Service Exposed on Port 3306

Port 3306 is open and running MySQL 5.0.51a-3ubuntu5. The service responded with a MySQL protocol handshake during initial connection.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/mysql-service-check.txt

**Observed Details:**

* Port: 3306/tcp
* Service: MySQL
* Version: MySQL 5.0.51a-3ubuntu5

**Risk Observation:**

Database services should not be broadly reachable from untrusted networks. Exposed MySQL services can provide attackers with an authentication surface and may reveal software version information. In production, database access should normally be limited to trusted application hosts or administrative networks.

### Finding 5: PostgreSQL Database Service Exposed on Port 5432

Port 5432 is open and running PostgreSQL 8.3.x. The service was confirmed reachable during targeted enumeration.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/postgresql-service-check.txt

**Observed Details:**

* Port: 5432/tcp
* Service: PostgreSQL
* Version: PostgreSQL DB 8.3.0 - 8.3.7

**Risk Observation:**

PostgreSQL should not be directly exposed unless there is a clear business need and strong access control. Exposed database services increase the risk of unauthorized login attempts, service enumeration, and potential exploitation of outdated software.

### Finding 6: VNC Remote Desktop Service Exposed on Port 5900

Port 5900 is open and running VNC using protocol version 3.3. The service returned an RFB protocol banner.

**Evidence:**

* evidence/raw/remote-db-targeted-nmap.txt
* evidence/raw/vnc-service-check.txt

**Observed Details:**

* Port: 5900/tcp
* Service: VNC
* Protocol Banner: RFB 003.003

**Risk Observation:**

VNC provides remote desktop access and should be restricted carefully. In a production environment, VNC should not be exposed broadly and should be protected with strong authentication, encryption, network segmentation, and monitoring.


## Initial Security Considerations

Remote access and database services are high-value targets in real environments. Exposed services such as FTP, Telnet, SSH, MySQL, PostgreSQL, and VNC can provide attackers with authentication surfaces, service version details, and potential entry points. Legacy cleartext protocols such as Telnet and FTP are especially risky because they may transmit credentials without encryption.

## MITRE ATT&CK Mapping

| Tactic            | Technique                 | Relevance                                                                                                          |
| ----------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Discovery         | Network Service Discovery | The analyst is identifying exposed remote access and database services.                                            |
| Discovery         | Software Discovery        | Service banners may reveal software names or versions.                                                             |
| Credential Access | Brute Force               | Exposed authentication services may be targeted by brute-force or password-spraying attempts in real environments. |
| Initial Access    | External Remote Services  | Remote access services may become entry points if exposed and weakly secured.                                      |

## SOC Detection Opportunities

Potential detection opportunities include:

* Repeated connection attempts to SSH, Telnet, FTP, VNC, or database services
* Failed authentication attempts from a single source
* Authentication attempts against legacy cleartext services
* Unexpected database connections from non-application systems
* Connections to VNC or remote administration ports
* Service enumeration patterns against multiple remote access ports
* Use of unusual clients or user agents for remote access services

## Defensive Recommendations

Recommendations will be updated after enumeration is completed. Initial defensive considerations include:

* Disable unnecessary remote access services
* Remove legacy cleartext protocols such as Telnet and FTP
* Restrict SSH access by network segment or VPN
* Require strong authentication and account lockout controls
* Disable direct database exposure where possible
* Restrict database access to trusted application hosts
* Disable or restrict VNC access
* Monitor authentication logs and network connections
* Alert on repeated failed login attempts
* Keep remote access and database software patched

## Status

In progress.

