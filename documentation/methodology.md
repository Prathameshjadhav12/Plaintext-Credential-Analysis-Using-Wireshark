# Methodology

## Scope

A controlled VirtualBox lab with Kali Linux as the assessment/capture machine and Metasploitable 2 as the intentionally vulnerable FTP server.

## Addressing

- Kali Linux: `192.168.0.110`
- Metasploitable 2: `192.168.0.111`

Confirm both VMs are connected to the same isolated Host-only network before testing. Do not bridge Metasploitable 2 to a public or household network.

## Steps

1. Test reachability from Kali with `ping -c 4 192.168.0.111`.
2. Identify the FTP service with `sudo nmap -sV -p 21 192.168.0.111`.
3. Start Wireshark on Kali and capture on the interface connected to the lab network.
4. Apply `ftp || tcp.port == 21` as a display filter.
5. Connect to the lab FTP service using `ftp 192.168.0.111`.
6. Authenticate only with the authorized lab account.
7. Inspect the corresponding conversation using **Follow → TCP Stream**.
8. Record the finding and redact credentials before sharing screenshots.

## Tools

- Nmap: service discovery and version detection.
- Wireshark: packet capture and protocol inspection.
- FTP client: generating the authorized test session.

## Limitations

This lab demonstrates plaintext FTP behavior in a controlled environment. It does not demonstrate interception of another person's traffic or prove that a third party accessed the credentials.
