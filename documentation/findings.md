# Findings

## F-01 — FTP credentials transmitted without encryption

**Status:** Confirmed in the controlled lab.

**Target:** Metasploitable 2 (`192.168.0.111`)

**Service:** FTP, TCP port 21; Nmap identified `vsftpd 2.3.4`.

**Evidence:**
- Nmap service scan showed TCP/21 open.
- Wireshark captured the FTP control conversation.
- The Follow TCP Stream view showed the FTP `USER` and `PASS` commands in readable form.
- The lab operator confirmed that the test credentials were visible.

**Security impact:** An observer able to capture traffic on the network path may read credentials because traditional FTP does not encrypt its control connection.

**Severity:** High for this lab demonstration. Production severity depends on exposure, account privileges, segmentation, and credential reuse.

**Recommendation:** Migrate to SFTP or correctly configured FTPS, disable plain FTP, restrict access, and rotate credentials if exposure is suspected.

**Evidence handling:** Redact usernames/passwords in public screenshots. Do not commit unredacted credentials or unrelated packet captures.
