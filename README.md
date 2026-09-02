# Home SOC Lab

## Overview

This project documents a private cybersecurity home lab used to practice vulnerability assessment, network enumeration, risk analysis, SOC-style detection thinking, evidence collection, and remediation reporting.

The lab is built around a controlled VMware environment using Kali Linux as the analyst workstation and Metasploitable2 as an intentionally vulnerable target system.

## Lab Environment

* Kali Linux: security testing and analysis workstation
* Metasploitable2: intentionally vulnerable target VM
* VMware Workstation: local virtualization platform
* Network: isolated host-only lab network

## Current Lab Network

The addresses shown below are sanitized lab examples used for public documentation.

* Kali NAT / Internet Adapter: `192.168.57.10`
* Kali Host-Only / Lab Adapter: `192.168.56.10`
* Metasploitable2 Lab IP: `192.168.56.20`

## Project Goals

* Practice safe network discovery and service enumeration
* Document findings in a professional report format
* Identify risks and possible business impact
* Map activity to MITRE ATT&CK where appropriate
* Write SOC-style detection notes
* Write remediation recommendations
* Demonstrate secure evidence-handling practices
* Build a clean cybersecurity portfolio project

## Project Reports

| Report | Description |
| --- | --- |
| [Lab Topology](diagrams/lab-topology.md) | Documents the VMware lab network layout and system roles |
| [Report 01: Reconnaissance and Enumeration](reports/01-reconnaissance-and-enumeration.md) | Baseline connectivity testing, Nmap scan results, open ports, and initial risk themes |
| [Report 02: Service Enumeration](reports/02-service-enumeration.md) | Web service enumeration for HTTP, WebDAV, Tomcat, exposed applications, and administrative paths |
| [Report 03: Remote Access and Database Enumeration](reports/03-remote-access-and-database-enumeration.md) | Enumeration of FTP, SSH, Telnet, MySQL, PostgreSQL, and VNC services |
| [Report 04: File Sharing and RPC Enumeration](reports/04-file-sharing-and-rpc-enumeration.md) | Enumeration of RPC, SMB, NetBIOS, NFS exports, anonymous SMB share listing, and file-sharing exposure |

## Evidence

Raw command output and supporting evidence are stored in:

* `evidence/raw/`

Evidence files are included to support report findings and demonstrate a repeatable assessment workflow.

## Public Release Sanitization

Before making this repository public, the project was reviewed for environment-specific information that should not be exposed in a public portfolio.

The sanitization process included:

* Replacing environment-specific private IP addresses with sanitized documentation addresses
* Reviewing network configuration and routing evidence for consistency after sanitization
* Reviewing raw evidence for credentials, tokens, keys, and other sensitive information
* Identifying binary evidence that required separate handling rather than text-based replacement
* Rewriting Git history to remove previously committed sensitive network information
* Verifying the rewritten repository history after sanitization

This process demonstrates an important aspect of security work: collected evidence can itself contain sensitive infrastructure information and must be reviewed before publication.

## Scope

This project only includes systems intentionally deployed inside my private VMware lab.

## Out of Scope

* Public IP addresses
* Third-party systems
* School or work networks
* Real organizations
* Unauthorized testing
* Credential theft
* Malware deployment
* Destructive testing

## Safety and Authorization Statement

All activity documented in this repository was performed against virtual machines that I own and control inside a private lab environment. Metasploitable2 is intentionally vulnerable and was used only for authorized cybersecurity training, documentation, vulnerability assessment practice, and SOC-style analysis.

## Skills Demonstrated

* Linux command-line usage
* Git and GitHub workflow
* Network connectivity validation
* Nmap scanning and service enumeration
* Web service enumeration
* Remote access service review
* Database service exposure review
* Evidence collection and handling
* Risk documentation
* SOC detection thinking
* Defensive remediation writing
* Secure public-release practices

## Status

* Phase 1: Lab setup and baseline reconnaissance completed.
* Phase 2: Web service enumeration completed.
* Phase 3: Remote access and database service enumeration completed.
* Phase 4: File sharing and RPC enumeration completed.
