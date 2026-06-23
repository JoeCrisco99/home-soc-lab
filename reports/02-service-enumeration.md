# Service Enumeration

## Objective

The objective of this phase is to perform deeper service enumeration against selected open ports discovered during the baseline Nmap scan. This report focuses on identifying service behavior, exposed applications, visible banners, default pages, and potential security concerns from a defensive and SOC-focused perspective.

## Scope

| Item          | Details                                                 |
| ------------- | ------------------------------------------------------- |
| Analyst VM    | Kali Linux                                              |
| Target VM     | Metasploitable2                                         |
| Target IP     | 192.168.56.20                                         |
| Network       | Private VMware host-only lab                            |
| Authorization | Private lab systems owned and controlled by the analyst |

## Target Services for This Phase

This phase begins with web-facing services because they are commonly exposed in real environments and often provide useful information for vulnerability assessment and SOC detection planning.

| Port     | Service       | Purpose                                            |
| -------- | ------------- | -------------------------------------------------- |
| 80/tcp   | HTTP          | Web service enumeration                            |
| 8180/tcp | HTTP / Tomcat | Web application and application server enumeration |

## Methodology

The enumeration process will include safe, non-destructive checks such as:

* Confirming service availability
* Reviewing HTTP response headers
* Identifying default pages or exposed web applications
* Recording visible service information
* Saving command output as evidence
* Documenting security observations
* Identifying SOC detection opportunities
* Writing remediation recommendations

## Evidence Files

Evidence for this phase will be saved in the evidence/raw directory.

| Evidence File                      | Description                              |
| ---------------------------------- | ---------------------------------------- |
| evidence/raw/http-headers-80.txt   | HTTP header response from port 80        |
| evidence/raw/http-headers-8180.txt | HTTP header response from port 8180      |
| evidence/raw/http-index-80.txt     | Initial HTTP page content from port 80   |
| evidence/raw/http-index-8180.txt   | Initial HTTP page content from port 8180 |

## Findings

### Finding 1: Web Server Version Disclosure on Port 80

Port 80 returned HTTP headers identifying the service as Apache/2.2.8 on Ubuntu with DAV/2 enabled. The response also disclosed PHP/5.2.4. Exposed server and framework version information can help an attacker identify known vulnerabilities, outdated software, and target-specific attack paths.

**Evidence:**

* evidence/raw/http-headers-80.txt
* evidence/raw/http-index-80.txt

**Observed Details:**

* Apache/2.2.8 (Ubuntu)
* DAV/2
* PHP/5.2.4-2ubuntu5.10
* Default Metasploitable2 landing page

**Risk Observation:**

The web service exposes outdated software banners and confirms that WebDAV functionality is enabled. In a production environment, this would increase the risk of targeted vulnerability research and exploitation attempts.

### Finding 2: Exposed Web Applications on Port 80

The default web page on port 80 links to several applications and directories, including TWiki, phpMyAdmin, Mutillidae, DVWA, and WebDAV.

**Evidence:**

* evidence/raw/http-index-80.txt
* evidence/raw/http-discovered-links.txt

**Observed Paths:**

* /twiki/
* /phpMyAdmin/
* /mutillidae/
* /dvwa/
* /dav/

**Risk Observation:**

Exposed application paths can reveal administrative tools, intentionally vulnerable applications, and web-accessible services. In a real environment, exposed phpMyAdmin or WebDAV paths would require strong access controls, patching, monitoring, and network restrictions.

### Finding 3: WebDAV Directory Allows High-Risk HTTP Methods

The /dav/ path on port 80 advertised WebDAV support and allowed multiple HTTP methods, including DELETE, TRACE, PROPFIND, PROPPATCH, COPY, MOVE, LOCK, and UNLOCK.

**Evidence:**

* evidence/raw/http-options.txt

**Observed Allowed Methods:**

* OPTIONS
* GET
* HEAD
* POST
* DELETE
* TRACE
* PROPFIND
* PROPPATCH
* COPY
* MOVE
* LOCK
* UNLOCK

**Risk Observation:**

The presence of WebDAV methods may allow file management operations if authentication and authorization controls are weak or misconfigured. The TRACE method is also commonly disabled in hardened environments because it can expose unnecessary diagnostic behavior.

### Finding 4: Tomcat Default Page and Administrative Paths Exposed on Port 8180

Port 8180 returned a default Apache Tomcat/5.5 page through Apache-Coyote/1.1. The page exposed links to Tomcat status, administration, manager, documentation, release notes, JSP examples, servlet examples, and WebDAV capabilities.

**Evidence:**

* evidence/raw/http-headers-8180.txt
* evidence/raw/http-index-8180.txt
* evidence/raw/http-discovered-links.txt
* evidence/raw/http-options.txt

**Observed Paths:**

* manager/status
* admin
* manager/html
* RELEASE-NOTES.txt
* tomcat-docs/
* jsp-examples/
* servlets-examples/
* webdav/

**Risk Observation:**

Default Tomcat pages and administrative paths should not be exposed broadly. In a production environment, Tomcat manager and administration interfaces should be restricted by authentication, role-based access control, network segmentation, and monitoring.


## Initial Security Considerations

Exposed web services can reveal useful information to attackers, including server versions, default pages, application paths, administrative interfaces, and misconfigurations. From a SOC perspective, web enumeration activity may appear as repeated HTTP requests, unusual user agents, access to uncommon paths, or requests against administrative endpoints.

## MITRE ATT&CK Mapping

| Tactic    | Technique                 | Relevance                                                                                      |
| --------- | ------------------------- | ---------------------------------------------------------------------------------------------- |
| Discovery | Network Service Discovery | The analyst is identifying available network services on the target system.                    |
| Discovery | Software Discovery        | HTTP headers and web responses may reveal software names, versions, or application frameworks. |

## SOC Detection Opportunities

Potential detection opportunities include:

* Repeated HTTP requests from a single source to multiple web paths
* Requests for default administrative pages
* Requests using unusual or security-tool user agents
* Access attempts to application server paths such as Tomcat manager pages
* Web requests followed by authentication attempts

## Defensive Recommendations

Recommendations will be updated after enumeration is completed. Initial defensive considerations include:

* Remove or disable unnecessary web services
* Restrict administrative web interfaces
* Avoid exposing default application pages
* Keep web servers and application servers patched
* Monitor web access logs for enumeration patterns
* Limit access to sensitive web paths by network segment or authentication control

## Status

In progress.
