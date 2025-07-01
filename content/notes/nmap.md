+++
date = '2025-07-01T14:17:06+05:30'
draft = false
title = 'Nmap'
+++

<style>
h1, h2, h3 {
  color: #000000; /* Pure black */
  font-weight: 700;
}
</style>

# Nmap Cheat Sheet

---

## Basic Scans
```bash
nmap -sn <target>           # Ping scan (check live hosts)
nmap -Pn <target>           # No ping (assume host is up)
````

## Port Scanning

```bash
nmap -sS <target>           # SYN scan (stealthy)
nmap -sT <target>           # TCP connect scan
nmap -sU <target>           # UDP scan
nmap -p- <target>           # Scan all 65535 ports
nmap -F <target>            # Fast scan (100 most common ports)
```

## Service & OS Detection

```bash
nmap -sV <target>           # Detect service versions
nmap -A <target>            # Aggressive scan (OS, version, scripts)
nmap -O <target>            # Detect OS
```

## Output & Logging

```bash
nmap -oN output.txt <target>   # Normal output
nmap -oX output.xml <target>   # XML output
nmap -oG output.gnmap <target> # Grepable output
nmap -oA output <target>       # Save in all formats
```

## Script Scanning (NSE)

```bash
nmap --script=<script> <target>     # Run specific script
nmap --script=vuln <target>         # Run vulnerability scripts
nmap --script=http-* <target>       # Run HTTP-related scripts
nmap --script=default <target>      # Run default scripts
```

## Timing & Performance

```bash
nmap --min-rate 1000 <target>       # Set min scan rate
nmap --max-retries 1 <target>       # Reduce retries
nmap --host-timeout 10m <target>    # Timeout after 10 minutes
nmap -T0 <target>                   # Slowest scan (stealthy)
nmap -T4 <target>                   # Faster scan
nmap -T5 <target>                   # Insane speed
```

## Firewall & IDS Evasion

```bash
nmap -sA <target>           # ACK scan (bypass stateless firewalls)
nmap -sN <target>           # NULL scan (no flags)
nmap -sX <target>           # XMAS scan
nmap -sF <target>           # FIN scan
```

## Evading Detection

```bash
nmap -D RND:10 <target>             # Decoy scan
nmap -f <target>                    # Fragment packets
nmap --data-length 50 <target>     # Append random data
nmap -S <spoofed-IP> <target>      # Spoof source IP
```

## Advanced Scanning

```bash
nmap -sC <target>               # Run default scripts
nmap -sI <zombie-host> <target> # Idle scan
nmap -6 <target>                # IPv6 scan
nmap -A --reason <target>       # Show scan reasoning
```

## Live Host Discovery

```bash
nmap -sn <network>              # Ping scan on subnet
nmap -sL <network>              # List targets only
nmap -PR <target>               # ARP scan (LAN discovery)
```

