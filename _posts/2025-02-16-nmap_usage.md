---
layout: post
title: "Nmap Usage"
date: 2025-02-16
categories:
  - tools
tags:
  - tools
  - tryhackme
  - hackthebox
---

## Basics:
Nmap 😄 Scan basically used for IP Scanning with different procedure.
- `IP` `CIDR` both scan through the `nmap` scanner 
- `CIDR` means bunch of IP's which is like in company networks or organisation

## Host Dicsovery
It's is mainly useful for the corporate networks.
Host Discovery run in by default. Its run through `-Sn`
- Root machine
	- `ICMP Echo Packet`
	- `TCP SYN -443`
	- `ICPM ACK - 80`
	- `ICPM T.S Request`
- Local Machine
	- `SYN -443`
	- `ACK -80`

> If you scan a specific IP there is no need to discover host so we have to tell `nmap` to not performing any host discovery. so we used `-Pn`

## Target
1. Single IP -> `nmap 192.168.0.1`
2. Subnet Range -> `nmap 192.168.0.1/8`
3. IP Range -> `nmap 192.168.0.1-8`
4. Specific IPs -> `nmap 192.168.0.1 192.168.0.2`
5. Text File -> `nmap -iL host.txt`
6. Domain -> `nmap abc.com`
***If I not specified any port then it automatically takes most common 1000 ports.

## Ports
1. Single Port -> `nmap 192.168.0.1 -P 80`
2. Seq Port -> `nmap 192.168.0.1 -P 20-30`
3. Distributed Only -> `nmap 192.168.0.1 -P 80,22,111`
4. Service Specific -> `nmap 192.168.0.1 -P http ` -80 -8008
5. Protocol Specific -> `nmap 192.168.0.1 -P T:22, U:53`
6. All Ports -> `nmap 192.168.0.1 -p-`  65535
7. additional: `nmap 192.168.0.1 --top-ports 100`

## Scan Techniques
1. TCP Connect Scan -> `-sT`
2. TCP Syn Scan -> `-sS`
3. FIN Scan -> `-sF`
4. Xmas Scan -> `-sX`
5. Null Scan -> `-sN`
6. Ping Scan -> `sP`
7. UDP Scan -> `-sU`
8. ACK Scan -> `-sA`
	1. It is mainly detect firewall is present or not.

## Scan Status
### Open
```console
A (client)  -> SYN  ->  B (target)  [Port 22]
           <- SYN/ACK <-
           -> ACK ->
```
### Close
```c
A  -> SYN  ->  B  [Port 22]
   <- RST/ACK <-
```
### Filtered
```c
A  -> SYN  ->  ???  (Firewall/Filter)  ->  B [Port 22]
         [No Response] or ICMP Error
```
### Open | Filtered
```c
A  -> SYN  ->  ???  (Firewall) ->  B [Port 22]
         [No Response]
```
### Close | Filtered
```c
A  -> SYN  ->  ???  (Firewall/Filter)  -> B [Port 22]
         [No Response or ICMP]
```
### Unfiltered
```c
A  -> SYN  ->  B [Port 22]
   <- ICMP <-
```

## Scan Timings

**T0 – Paranoid**
	- Very slow. One probe at a time with long delays.
	- - Designed to avoid detection by IDS (Intrusion Detection Systems).
	-  **Use case**: Maximum stealth.
```bash 
	nmap -T0 192.168.0.1
```

**T1 – Sneaky**
- Slightly faster than T0 but still slow.
- One probe at a time with moderate delays.
- **Use case**: Avoid detection with some patience.
**T2 – Polite**
- Scans slower to minimize the impact on the target network.
- **Use case**: Avoid overwhelming the target system.
**T3 – Normal (Default)**
- Balanced speed and accuracy.
- **Use case**: General purpose scanning.
**T5 – Insane**
- Very fast with minimal delays.
- Can overwhelm or alert IDS systems.
- **Use case**: Only when scanning stable and responsive targets.

**Host Timeout**  
Terminates the scan if the target does not respond within a specified time.
```c
nmap --host-timeout 500ms 192.168.0.1
```
**Scan Delay**  
Introduces a delay between probes to avoid flooding the target.
```c
nmap --scan-delay 1s 192.168.0.1

```

## Output Types
1. **-oN** - Normal Text Output 
2. **-oX** - XML Format 
3. **-oG** - Grepable Format ❌ 
4. **-oS** - Script Kiddie Format 
		**STATE** -> **5t4t3**

## Nmap Script Engine
### Firewall bypass
```bash
# Firewall evasion using fragmentation
nmap -f 192.168.1.1
# Timing template for IDS evasion
nmap -T2 192.168.1.1
```
### FTP Enumeration
```bash
# Basic FTP scanning
nmap --script ftp-* 192.168.1.1
# Anonymous login check
nmap --script ftp-anon 192.168.1.1 -p 21
```
### DNS Enumeration
```bash
# DNS zone transfer
nmap --script dns-zone-transfer -p 53 ns1.target.com
# Subdomain discovery
nmap --script dns-brute target.com
```

### HTTP Enumeration
```bash
# Basic HTTP headers scan
nmap -p 80 --script http-headers target.com
# Directory discovery
nmap --script http-enum target.com
```

## Misc
```bash
nmap -sV 192.168.0.1     # Service version detection
nmap -O 192.168.0.1      # OS detection
nmap -v 192.168.0.1      # Verbosity
nmap -A 192.168.0.1      # Aggressive scan (combines -sV -O -sC --traceroute)
```

- IPv6 Scanning
	```console
	nmap -6 2001:db8:: # Scan IPv6 target
	```
