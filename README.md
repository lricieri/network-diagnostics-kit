# Network Diagnostics Kit (Level 1 Support)

A command line tool that automates the first checks a support analyst runs
when a user reports "I can't reach this site or server": DNS resolution,
ICMP ping, TCP port reachability and HTTP response. It then explains what
the pattern of results usually means and generates a ticket note ready to
paste into a help desk tool.

## Why I built it

Level 1 support follows the same routine over and over: reproduce the
problem, narrow down which layer is failing, document what was done and
escalate with useful information. This project turns that routine into a
script, and reflects what I practiced in my technical support internship
(remote B2B support, structured testing and reporting).

## What it checks

| Check | What it tells you |
|---|---|
| DNS resolution | Whether the name resolves, and how fast |
| ICMP ping | Whether the host answers, with packet loss detection |
| TCP ports | Whether services accept connections (default: 80 and 443) |
| HTTP/HTTPS request | Whether the web service answers, with status code and latency |

The diagnosis step reads the combination of results, not each one alone.
For example:

- DNS fails: the problem is name resolution or local connectivity, so there
  is no point testing ports.
- Ping fails but ports answer: ICMP is probably blocked on purpose, not an
  outage.
- Port refused vs. port timed out: the first means the service is down, the
  second usually means a firewall is dropping traffic.
- Port 443 open but HTTPS fails: possible certificate or clock problem.

## Usage

```bash
# Full check with default ports (80, 443)
python netdiag.py check example.com

# Custom ports
python netdiag.py check example.com --ports 80 443 3389

# Add a note ready to paste into a ticket
python netdiag.py check example.com --ticket

# Machine readable output
python netdiag.py check example.com --json
```

No external dependencies, only the Python 3 standard library.

## Example output

```
Network diagnostics for: example.com
--------------------------------------------------------
[ OK ] DNS resolution: example.com resolved to 93.184.216.34 in 24 ms
[ OK ] ICMP ping: host replied, no packet loss
[ OK ] TCP port 80: connection accepted in 12 ms
[ OK ] TCP port 443: connection accepted in 11 ms
[ OK ] HTTPS request: HTTP 200 in 62 ms

Suggested next steps:
  1. All checks passed. The problem is likely on the application or user
     side (browser cache, credentials, local proxy). Collect screenshots
     and the exact error message.

TICKET NOTE (2026-10-27 22:02)
Problem: user reports difficulty reaching example.com.
Actions performed:
  - DNS resolution: OK (...)
  ...
Result: No failure reproduced from the support side.
Next step: ...
```

## Ethical use

The port checks only open a normal TCP connection to the ports you list.
Use the tool on hosts you own or are authorized to troubleshoot.

## Next steps

- [ ] Add traceroute to locate where the path breaks
- [ ] Check certificate expiration date directly
- [ ] Save reports to a log file with timestamps
- [ ] Add a check for the local gateway and the configured DNS servers

## Tech stack

- Python 3 (standard library: `socket`, `subprocess`, `urllib`, `argparse`)
"# network-diagnostics-kit" 
