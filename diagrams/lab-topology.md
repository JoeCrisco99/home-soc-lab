# Lab Topology

## Network Diagram

```text
                         NAT / Internet
                              |
                              |
                    eth0: 192.168.57.10
                    +--------------------+
                    |                    |
                    |     Kali Linux     |
                    |   Analyst VM       |
                    |                    |
                    +--------------------+
                    eth1: 192.168.56.10
                              |
                              |
                 Host-Only Private Lab Network
                              |
                              |
                    eth1: 192.168.56.20
                    +--------------------+
                    |                    |
                    |  Metasploitable2   |
                    |   Target VM        |
                    |                    |
                    +--------------------+
```

## Description

This lab uses two virtual machines inside VMware Workstation. Kali Linux is configured with two network adapters: one NAT adapter for internet access and one host-only adapter for private lab communication. Metasploitable2 is connected only to the host-only network to keep the intentionally vulnerable target isolated from external networks.

## Systems

| System          | Role                               | IP Address      | Network Type |
| --------------- | ---------------------------------- | --------------- | ------------ |
| Kali Linux      | Analyst / Security Testing VM      | 192.168.57.10 | NAT          |
| Kali Linux      | Analyst / Security Testing VM      | 192.168.56.10 | Host-only    |
| Metasploitable2 | Intentionally Vulnerable Target VM | 192.168.56.20 | Host-only    |
