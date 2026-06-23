# File Sharing and RPC Service Enumeration

## Objective

The objective of this phase is to perform safe, non-destructive enumeration of file sharing and RPC-related services discovered during the baseline Nmap scan. This report focuses on identifying exposed SMB, NetBIOS, NFS, and RPC services, documenting visible service information, and describing defensive risks and SOC detection opportunities.

## Scope

| Item          | Details                                                 |
| ------------- | ------------------------------------------------------- |
| Analyst VM    | Kali Linux                                              |
| Target VM     | Metasploitable2                                         |
| Target IP     | 192.168.56.20                                         |
| Network       | Private VMware host-only lab                            |
| Authorization | Private lab systems owned and controlled by the analyst |

## Target Services for This Phase

| Port     | Service         | Purpose                                     |
| -------- | --------------- | ------------------------------------------- |
| 111/tcp  | rpcbind         | RPC service discovery                       |
| 139/tcp  | NetBIOS / Samba | Windows-style file sharing enumeration      |
| 445/tcp  | SMB / Samba     | SMB file sharing enumeration                |
| 2049/tcp | NFS             | Unix/Linux network file sharing enumeration |

## Methodology

The enumeration process will use safe checks such as:

* Confirming service availability
* Capturing service versions
* Identifying exposed file-sharing services
* Reviewing RPC and NFS exposure
* Recording evidence output
* Documenting defensive risk observations
* Identifying SOC detection opportunities
* Writing remediation recommendations

This phase does not include brute-force attacks, password spraying, credential theft, destructive testing, malware deployment, or testing against any system outside the private lab.

## Evidence Files

Evidence for this phase will be saved in the evidence/raw directory.

| Evidence File                                   | Description                                                  |
| ----------------------------------------------- | ------------------------------------------------------------ |
| evidence/raw/file-sharing-rpc-targeted-nmap.txt | Targeted Nmap output for SMB, NetBIOS, NFS, and RPC services |
| evidence/raw/rpcinfo.txt                        | RPC service listing from the target                          |
| evidence/raw/smb-shares.txt                     | SMB share enumeration output                                 |
| evidence/raw/nfs-exports.txt                    | NFS export enumeration output                                |

## Findings

### Finding 1: RPC Services Exposed on Port 111

Port 111 is open and running rpcbind/portmapper. RPC enumeration identified multiple registered services, including portmapper, status, NFS, nlockmgr, and mountd.

**Evidence:**

* evidence/raw/file-sharing-rpc-targeted-nmap.txt
* evidence/raw/rpcinfo.txt

**Observed Details:**

* Portmapper/rpcbind exposed on TCP and UDP port 111
* NFS versions 2, 3, and 4 registered on port 2049
* mountd registered on dynamic TCP and UDP ports
* nlockmgr registered on dynamic TCP and UDP ports
* status service registered on dynamic TCP and UDP ports

**Risk Observation:**

RPC services can reveal internal service mappings and dynamically assigned ports. In a production environment, unnecessary RPC exposure can assist attackers during reconnaissance and may expose NFS-related services that should be limited to trusted systems only.

### Finding 2: SMB and NetBIOS Services Exposed on Ports 139 and 445

Ports 139 and 445 are open and running Samba file-sharing services. Targeted Nmap identified Samba smbd 3.x to 4.x, and SMB enumeration showed the system in the WORKGROUP workgroup.

**Evidence:**

* evidence/raw/file-sharing-rpc-targeted-nmap.txt
* evidence/raw/smb-shares.txt

**Observed Details:**

* Port 139/tcp: NetBIOS / Samba
* Port 445/tcp: SMB / Samba
* Workgroup: WORKGROUP
* Server name: METASPLOITABLE
* Samba banner observed in SMB output: Samba 3.0.20-Debian

**Risk Observation:**

SMB services provide file-sharing and remote resource access. In a production environment, exposed SMB should be restricted to trusted internal networks and monitored closely because SMB is commonly targeted for share enumeration, credential attacks, data access, and lateral movement.

### Finding 3: Anonymous SMB Share Enumeration Successful

SMB share enumeration allowed anonymous access and returned a list of available shares.

**Evidence:**

* evidence/raw/smb-shares.txt

**Observed Shares:**

* print$
* tmp
* opt
* IPC$
* ADMIN$

**Risk Observation:**

Anonymous SMB access can expose share names, system roles, and internal structure without authentication. In a production environment, anonymous or guest SMB access should be disabled unless explicitly required and tightly controlled. Share permissions should be reviewed regularly.

### Finding 4: NFS Service Exposed on Port 2049

Port 2049 is open and running NFS. RPC enumeration confirmed NFS versions 2, 3, and 4 are registered.

**Evidence:**

* evidence/raw/file-sharing-rpc-targeted-nmap.txt
* evidence/raw/rpcinfo.txt
* evidence/raw/nfs-exports.txt

**Observed Details:**

* Port: 2049/tcp
* Service: NFS
* Versions observed through RPC: 2, 3, and 4

**Risk Observation:**

NFS is used for Unix/Linux file sharing and should be restricted carefully. Exposed NFS services can reveal shared directories and may allow unauthorized access if export rules or file permissions are misconfigured.

### Finding 5: NFS Root Export Available to Wildcard Hosts

NFS export enumeration showed the root path `/` exported to `*`.

**Evidence:**

* evidence/raw/nfs-exports.txt

**Observed Export:**

* `/ *`

**Risk Observation:**

Exporting the root filesystem path to wildcard hosts is a high-risk configuration in a production environment. This may expose sensitive filesystem paths to any allowed network host depending on permissions and mount options. NFS exports should be limited to specific trusted hosts and should avoid broad wildcard access.


## Initial Security Considerations

File sharing and RPC services can expose sensitive information, authentication surfaces, shared directories, and internal system details. In real environments, SMB, NFS, and RPC services should be restricted to trusted networks and monitored closely. Unnecessary file shares or weak access controls can increase the risk of data exposure, lateral movement, and unauthorized access.

## MITRE ATT&CK Mapping

| Tactic           | Technique                      | Relevance                                                                          |
| ---------------- | ------------------------------ | ---------------------------------------------------------------------------------- |
| Discovery        | Network Service Discovery      | The analyst is identifying exposed SMB, NFS, and RPC services.                     |
| Discovery        | Network Share Discovery        | File sharing services may expose shared directories or network resources.          |
| Collection       | Data from Network Shared Drive | Improperly secured network shares may expose sensitive files in real environments. |
| Lateral Movement | Remote Services                | SMB and related services may be used for lateral movement if weakly secured.       |

## SOC Detection Opportunities

Potential detection opportunities include:

* Connections to SMB, NetBIOS, NFS, or RPC services from unusual hosts
* Repeated attempts to enumerate shares or exports
* Access to administrative or hidden shares
* Unexpected NFS mount attempts
* SMB session setup attempts from non-domain systems
* Authentication failures against file-sharing services
* Access to file shares outside normal business hours

## Defensive Recommendations

Recommendations will be updated after enumeration is completed. Initial defensive considerations include:

* Disable unnecessary SMB, NFS, NetBIOS, and RPC services
* Restrict file-sharing services to trusted network segments
* Require strong authentication and access controls
* Remove anonymous or guest access where possible
* Review share permissions regularly
* Monitor access to sensitive shares
* Alert on suspicious share enumeration behavior
* Keep Samba, NFS, and related services patched
* Limit RPC exposure to systems that require it

## Status

In progress.
