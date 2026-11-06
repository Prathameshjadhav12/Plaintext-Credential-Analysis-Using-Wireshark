# Plaintext Credential Analysis: FTP Traffic Capture

A controlled cybersecurity lab demonstrating how traditional FTP transmits login credentials without encryption, using **Nmap** for service discovery and **Wireshark** for packet analysis.

> **Scope and safety:** This assessment was performed against the intentionally vulnerable Metasploitable 2 virtual machine in a lab environment. Use only systems and credentials you own or are explicitly authorized to test. Do not expose Metasploitable 2 to the public internet.

## Objectives

- Identify an FTP service and version with Nmap.
- Generate an FTP login from Kali Linux to Metasploitable 2.
- Capture the authorized lab traffic with Wireshark.
- Inspect the FTP control conversation using **Follow → TCP Stream**.
- Explain the security impact and recommend encrypted alternatives.

## Lab environment

| Component | Role | Address / details |
|---|---|---|
| Kali Linux | Assessment and packet-capture machine | `192.168.0.110` |
| Metasploitable 2 | Authorized FTP test server | `192.168.0.111` |
| FTP service | Service under assessment | TCP `21`, `vsftpd 2.3.4` |
| Tools | Discovery and analysis | Nmap, Wireshark, FTP client |
| Virtualization | Lab platform | Oracle VirtualBox |

**Network note:** The recorded addresses are in the `192.168.0.x` range. Verify that both VMs are attached to the same VirtualBox **Host-only** network and are not bridged onto a home or public network. Keep Metasploitable 2 isolated.

## Methodology

### 1. Verify connectivity

From Kali:

```bash
ping -c 4 192.168.0.111
```

Observed result: 15 packets transmitted, 15 received, 0% packet loss (as recorded during the lab).

### 2. Discover FTP with Nmap

```bash
sudo nmap -sV -p 21 192.168.0.111
```

Observed result:

```text
21/tcp open  ftp  vsftpd 2.3.4
```

This establishes that TCP port 21 was open and the service was identified as FTP running vsftpd 2.3.4. Service discovery alone does not demonstrate credential exposure.

### 3. Capture the authorized FTP session

On Kali, start Wireshark on the interface carrying traffic to Metasploitable 2. Use this display filter:

```text
ftp || tcp.port == 21
```

In a separate Kali terminal, connect to the lab server:

```bash
ftp 192.168.0.111
```

Use only the Metasploitable 2 lab account supplied for this exercise. After a successful login, issue a harmless command such as `ls`, then exit with `quit`.

### 4. Inspect the FTP control stream

In Wireshark, select a packet from the FTP TCP conversation and choose:

**Right-click → Follow → TCP Stream**

The observed client-to-server stream contained the FTP login commands:

```text
USER <lab-username>
PASS <lab-password>
```

The lab operator confirmed that the test username and password were readable in the captured stream. Do not publish real credentials; redact them in screenshots and reports.

## Finding F-01: FTP credentials transmitted without encryption

| Field | Assessment |
|---|---|
| Finding ID | F-01 |
| Title | FTP credentials transmitted in plaintext |
| Affected service | FTP over TCP/21 |
| Observed service | vsftpd 2.3.4 on Metasploitable 2 |
| Evidence | Wireshark packet capture and Follow TCP Stream |
| Risk | A party able to capture traffic on the communication path may read FTP credentials |
| Severity | High in this lab's context; actual severity depends on network exposure, account privileges, and credential reuse |

### Impact

Traditional FTP does not encrypt its control channel. Login commands can therefore be readable to an observer who can capture the relevant network traffic. Exposed credentials may enable unauthorized access to the affected account and any resources available to it.

### Recommendation

- Replace FTP with **SFTP** (over SSH) or properly configured **FTPS**.
- Disable plain FTP after migration.
- Restrict administrative and file-transfer access to trusted networks.
- Use unique, strong credentials and rotate any credentials that may have been exposed.
- Monitor for unexpected FTP connections and authentication attempts.

## Evidence checklist

Add your own screenshots to `screenshots/`:

- `01-ping-connectivity.png` — Kali successfully reaching Metasploitable 2.
- `02-nmap-ftp-discovery.png` — Nmap identifying TCP/21 and vsftpd.
- `03-wireshark-ftp-capture.png` — captured FTP packets.
- `04-plaintext-credentials-redacted.png` — Follow TCP Stream with credentials safely redacted.

The first two screenshots were captured during the lab. Add the Wireshark screenshots yourself. **Do not commit screenshots containing unredacted passwords, personal data, or unrelated traffic.**

## Limitations

- This was a controlled lab demonstration, not an assessment of a production network.
- The capture demonstrates the behavior of the tested FTP session; it does not establish that any third party intercepted the traffic.
- Nmap service/version detection can be imperfect and should be corroborated where necessary.
- Severity is context-dependent; the rating above is a lab assessment, not a universal CVSS score.

## Reproduction summary

1. Start both VMs on the same isolated VirtualBox Host-only network.
2. Confirm the IP addresses and connectivity.
3. Run the Nmap service scan.
4. Capture the FTP session in Wireshark.
5. Inspect the TCP stream and redact credentials before sharing evidence.

## Author

Prathamesh Jadhav

This repository documents an educational cybersecurity lab using intentionally vulnerable training infrastructure.
