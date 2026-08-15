# Phishing Email Investigation

## Investigation Overview

This investigation was performed as part of a cybersecurity training exercise focused on phishing email analysis and threat intelligence.

The investigation started with a provided email sample. I examined the email headers, sender information, authentication results, message content, suspicious URL, related IP addresses, malware indicators, payload hashes, and threat intelligence records.

The main objective was to determine whether the email contained indicators associated with malicious infrastructure or malware.

## Tools Used

- Email Header Analysis
- VirusTotal
- URLhaus
- AbuseIPDB
- Abuse.ch
- VMRay
- CyberChef
- CyberDefenders training environment

## Initial Email Analysis

The email appeared to be a commercial purchase or receipt-related message.

Sender:

`erikajohana.lopez@uptc.edu.co`

Subject:

`COMMERCIAL PURCHASE RECEIPT ONLINE 27 NOV`

The message was addressed to undisclosed recipients.

The email contained a suspicious executable URL:

`http://107.175.247.199/loader/install.exe`

The message also contained an access code:

`8657`

The executable URL was treated as suspicious because it directly referenced an IP address and attempted to deliver an `.exe` file.

## Email Authentication Analysis

The header contained the following authentication results:

| Authentication | Result |
|---|---|
| SPF | SoftFail |
| DKIM | Fail |
| DMARC | None |
| Sender IP | 18.208.22.104 |

These results increased the suspicion around the email. Authentication failures alone do not prove that an email is malicious, so the results were correlated with URL, IP, payload, and malware intelligence.

The sender IP `18.208.22.104` was also investigated with VirusTotal.

## Sender IP Investigation

### IP

`18.208.22.104`

VirusTotal showed:

`0/91` security vendors detected the IP as malicious.

VirusTotal also showed files associated with the IP address.

The IP was identified as belonging to Amazon.com, Inc. under AS14618.

Because the IP itself had a 0/91 detection result, I did not classify the IP as malicious based on this result alone.

## Suspicious URL Investigation

### URL

`http://107.175.247.199/loader/install.exe`

The URL was investigated using VirusTotal and URLhaus.

VirusTotal showed:

`7/91` security vendors flagged the IP `107.175.247.199` as malicious.

The IP was associated with AS36352, HostPapa.

URLhaus classified the URL as:

`Malware download`

The URL status was listed as offline.

The URLhaus record also associated the infrastructure with:

- AsyncRAT
- BitRAT
- CoinMiner

## URLhaus Payload Investigation

URLhaus showed multiple executable payloads retrieved from the same infrastructure.

The payload records included:

| Malware Association | SHA-256 |
|---|---|
| BitRAT | `bf7628695c2d7fa3020034a065397592a1f8850e59f9a448b555bc1c8c639539` |
| AsyncRAT | `5ca468704e7ccb8e1b37c0f7595c54df4e2f4035345b6e442e8bd4e11c58f791` |
| CoinMiner | `453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0` |

The URLhaus evidence showed these payloads as executable files associated with the investigated IP.

## VirusTotal Payload Analysis

The payload SHA-256 values were searched individually in VirusTotal.

The observed detection results included:

### AsyncRAT

SHA-256:

`5ca468704e7ccb8e1b37c0f7595c54df4e2f4035345b6e442e8bd4e11c58f791`

VirusTotal result:

`53/70` security vendors flagged the file as malicious.

### CoinMiner

SHA-256:

`453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0`

VirusTotal result:

`54/70` security vendors flagged the file as malicious.

### BitRAT

The BitRAT payload was also investigated by SHA-256 through VirusTotal. The investigation recorded a high malicious detection rate for the sample.

The VirusTotal results provided strong supporting evidence that the referenced payloads were malicious.

## Malware Family Research

### AsyncRAT

AsyncRAT is a remote access trojan associated with unauthorized remote access and capabilities such as keylogging, credential theft, and other system-control functions.

### BitRAT

BitRAT is a remote access trojan associated with unauthorized access, data theft, remote control, and downloading additional malicious content.

### CoinMiner

CoinMiner malware uses a victim's computing resources to mine cryptocurrency. Such activity may increase CPU usage, reduce system performance, and increase power consumption.

The malware-family names were observed in threat intelligence associated with the investigated infrastructure. This does not by itself prove that the original email attachment was one of these specific families.

## VMRay Investigation

VMRay was used to investigate indicators associated with the payload SHA-256 values.

The investigation included IOC information, payload information, Base64-related artifacts, and URL or module requests.


## Base64 and CyberChef Analysis

Base64-related content associated with the CoinMiner analysis was examined using CyberChef.

After decoding the Base64 content, a PowerShell sleep or delay command was observed:

`Start-Sleep -50`

This indicates PowerShell-related activity involving a delay.

The command itself does not prove that the system was rebooted. A separate reboot or shutdown command would be required to establish such behavior.

## Domain Investigation

The domain `fsfb.org.co` was investigated with VirusTotal.

VirusTotal showed:

`0/91` direct detections.

The domain information also showed at least 10 detected files associated with the domain and historical passive DNS records resolving to multiple IP addresses.

The domain's long registration history does not establish legitimacy. Domain age and reputation were therefore considered together with the email authentication results, URL, IP reputation, and payload intelligence.

## Indicators of Compromise

### Email

`erikajohana.lopez@uptc.edu.co`

### Sender IP

`18.208.22.104`

### Malicious infrastructure IP

`107.175.247.199`

### Suspicious URL

`http://107.175.247.199/loader/install.exe`

### Domain

`fsfb.org.co`

### Payload SHA-256 values

```text
bf7628695c2d7fa3020034a065397592a1f8850e59f9a448b555bc1c8c639539
5ca468704e7ccb8e1b37c0f7595c54df4e2f4035345b6e442e8bd4e11c58f791
453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0


Conclusion
The investigation identified multiple indicators consistent with a phishing email delivering or directing the recipient toward malicious infrastructure.
The strongest findings were the suspicious executable URL, the VirusTotal malicious reputation for 107.175.247.199, the URLhaus classification of the URL as a malware download, and the association of the infrastructure with AsyncRAT, BitRAT, and CoinMiner payloads.
The payload SHA-256 investigations also produced high VirusTotal detection rates, providing additional evidence of malicious activity.
The email authentication results, including SPF SoftFail, DKIM Fail, and the absence of a DMARC policy, added further suspicion.
Based on the combined evidence, the email should be treated as malicious or highly suspicious in a SOC investigation.
