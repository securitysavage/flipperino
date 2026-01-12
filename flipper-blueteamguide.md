# Flipper Zero + Momentum Firmware + WiFi Dev Board Guide

> **⚠️ Legal & Ethical Notice**: This guide is intended for educational purposes, security research, and authorized penetration testing only. Always ensure you have explicit permission before testing any systems you don't own. Unauthorized access to computer systems and networks is illegal under the Computer Fraud and Abuse Act (CFAA) and similar laws worldwide.

## Table of Contents

- [Introduction](#introduction)
- [Setup & Prerequisites](#setup--prerequisites)
- [WiFi Capabilities](#wifi-capabilities)
- [Sub-GHz Radio Fun](#sub-ghz-radio-fun)
- [RFID/NFC Projects](#rfidnfc-projects)
- [Infrared Control](#infrared-control)
- [BadUSB & Scripting](#badusb--scripting)
- [GPIO & Hardware Hacking](#gpio--hardware-hacking)
- [Blue Team Applications](#blue-team-applications)
- [Legal Considerations](#legal-considerations)
- [Resources](#resources)

---

## Introduction

The Flipper Zero is a versatile multi-tool for hardware hacking, security research, and RF experimentation. When combined with:

- **Momentum Firmware**: Community firmware with enhanced pentesting features
- **WiFi Dev Board**: Adds ESP32-based WiFi capabilities

You get a portable security research platform that fits in your pocket. Think of it as a 21st-century tricorder for the cybersecurity professional.

---

## Setup & Prerequisites

### Hardware Required

- Flipper Zero device
- WiFi Dev Board (ESP32-based module)
- microSD card (recommended: 16GB or larger)

### Software Setup

1. **Install Momentum Firmware**
   - Download from: <https://momentum-fw.dev/>
   - Flash using qFlipper or web updater
   - Verify installation in Settings → About

2. **Install WiFi Dev Board Firmware**
   - Flash Marauder or Black Magic firmware to ESP32
   - Connect dev board to Flipper's GPIO pins
   - Test connection via Apps → GPIO → WiFi Marauder

3. **SD Card Setup**
   - Format as FAT32
   - Create folder structure for databases and captures
   - Load community databases (sub-GHz, NFC, IR)

---

## WiFi Capabilities

### Deauth Frame Analysis

**Use Case**: Monitor your network for deauthentication attacks

```text
Apps → GPIO → WiFi Marauder → Sniff → Deauth Detector
```

**What it does**: Detects and logs deauth frames, useful for identifying WiFi denial-of-service attempts

**Blue Team Application**:

- Monitor corporate WiFi for attack attempts
- Validate wireless IDS/IPS effectiveness
- Security awareness demonstrations

### Evil Portal Testing

**Use Case**: Test user susceptibility to captive portal phishing

⚠️ **Only on networks you own/have authorization to test**

```text
Apps → GPIO → WiFi Marauder → Evil Portal
```

**What it does**: Creates fake captive portals to test user awareness

**Best Practices**:

- Use only in controlled lab environments
- Include security awareness training component
- Document all tests with proper authorization

### Beacon Spam (WiFi Pollution)

**Use Case**: Conference pranks, testing WiFi scanners

```text
Apps → GPIO → WiFi Marauder → Beacon Spam
```

**Fun Ideas** (all legal):

- "NSA Surveillance Van 1-50"
- "Pretty Fly for a WiFi"
- "404 Network Unavailable"
- "Get Off My LAN"

**Note**: This is harmless but can annoy people. Use responsibly at conferences/events where it's expected.

### Packet Capture & Analysis

**Use Case**: Capture WiFi traffic for analysis

```text
Apps → GPIO → WiFi Marauder → Sniff → PCAP
```

**Applications**:

- Protocol analysis
- Troubleshooting WiFi issues
- Security research on your own networks

---

## Sub-GHz Radio Fun

### Signal Capture & Replay

**Frequency Range**: 300-928 MHz (region dependent)

**Legal Uses**:

- Analyze your own garage door opener
- Test your car's key fob security
- Experiment with weather station protocols
- Research wireless doorbell systems

```text
Sub-GHz → Read → [Select frequency]
Sub-GHz → Saved → [Replay captured signal]
```

**Popular Frequencies to Explore**:

- 315 MHz: North American garage doors, car key fobs
- 433.92 MHz: European devices, wireless sensors
- 868 MHz: European ISM band devices
- 915 MHz: North American ISM band

**Blue Team Use**:

- Assess physical security vulnerabilities
- Test for signal replay vulnerabilities
- Demonstrate wireless attack vectors

### Signal Analysis

Use the spectrum analyzer to:

- Identify unknown signals in your environment
- Check for interference sources
- Map out RF landscape around facilities

---

## RFID/NFC Projects

### Card Reading & Analysis

**Supported Formats**:

- 125 kHz: EM4100, HID Prox, Indala
- 13.56 MHz: MIFARE, NTAG, ISO14443

```text
NFC/RFID → Read → [Select protocol]
```

**Ethical Applications**:

- Clone your own access badges (backup)
- Analyze security of your personal cards
- Test badge security systems (with authorization)
- Research card protocols

### iButton (Dallas Key) Reading

**Use Case**: Read and emulate iButton devices

```text
iButton → Read
iButton → Saved → Emulate
```

**Common Applications**:

- Backup building access keys you own
- Test legacy access control systems (authorized only)

---

## Infrared Control

### Universal Remote Functionality

**Use Case**: Control IR devices, build custom remotes

```text
Infrared → Universal Remotes → [Select device type]
Infrared → Learn New Remote → [Capture signals]
```

**Fun Projects**:

- Build custom media center remote
- Create scene-based home automation
- Conference room shenanigans (ethical ones!)
- Museum exhibits that allow interaction

**Popular IR Databases**:

- Load IRDB from GitHub
- Community-contributed remote files
- Custom signal libraries

---

## BadUSB & Scripting

### Authorized Penetration Testing

**Use Case**: Automated security testing with permission

```text
BadUSB → [Select script] → Run
```

**Example Scripts** (for your own systems):

- Network information gathering
- Security audit automation
- Incident response data collection
- System inventory tools

### DuckyScript Compatibility

Write your own scripts in DuckyScript format:

```text
REM Security Assessment Script
REM Author: [Your Name]
REM Target: [Authorized system]
DELAY 1000
GUI r
DELAY 500
STRING cmd
ENTER
DELAY 500
STRING ipconfig /all > C:\temp\network_info.txt
ENTER
```

**Blue Team Applications**:

- Rapid incident response data collection
- Automated security configuration checks
- User awareness testing (with HR approval)

---

## GPIO & Hardware Hacking

### UART Debugging

**Use Case**: Access serial consoles on embedded devices

```text
GPIO → UART Terminal
```

**Common Baud Rates**: 9600, 115200, 38400

**What You Can Do**:

- Debug IoT devices
- Access router serial consoles
- Explore embedded Linux systems
- Firmware analysis

### SPI/I2C Communication

**Use Case**: Talk to embedded chips and sensors

**Projects**:

- Read EEPROM chips
- Interface with sensors
- Debug hardware designs
- Reverse engineer devices

### Logic Analysis

Use Flipper as a basic logic analyzer:

- Capture digital signals
- Analyze communication protocols
- Debug hardware circuits

---

## Blue Team Applications

### Wireless Security Auditing

- **Deauth Detection**: Monitor for WiFi attacks
- **Rogue AP Hunting**: Identify unauthorized access points
- **Signal Strength Mapping**: RF site surveys

### Physical Security Testing

- **Badge Cloning**: Test RFID security (authorized only)
- **Key Fob Analysis**: Assess wireless key security
- **Access Control Review**: Evaluate physical security measures

### Security Awareness

- **Demonstration Tool**: Show real attacks safely
- **Training Aid**: Hands-on security education
- **Red Team Support**: Authorized physical penetration tests

### Incident Response

- **Quick Data Collection**: BadUSB scripts for IR
- **Network Analysis**: Rapid WiFi assessment
- **Evidence Gathering**: Document security findings

---

## Legal Considerations

### Know Your Laws

**United States**:

- **Computer Fraud and Abuse Act (CFAA)**: Unauthorized access is illegal
- **Wiretap Act**: Intercepting communications requires consent
- **FCC Regulations**: Transmission power limits, licensed spectrum

**International**:

- Check local laws regarding RF transmission
- Understand data protection regulations (GDPR, etc.)
- Research computer misuse laws in your jurisdiction

### Always Get Permission

✅ **Legal & Ethical**:

- Testing your own devices
- Authorized penetration testing with written consent
- Educational use in controlled lab environments
- Security research on owned equipment

❌ **Illegal & Unethical**:

- Accessing networks without permission
- Cloning cards you don't own
- Interfering with public infrastructure
- Unauthorized signal jamming
- Stealing credentials or data

### Best Practices

1. **Document Everything**: Keep authorization letters
2. **Scope Clearly**: Define exactly what you're testing
3. **Time-Box Tests**: Set specific testing windows
4. **Report Findings**: Provide professional documentation
5. **Follow Disclosure**: Responsible disclosure for vulnerabilities

---

## Resources

### Official Links

- **Flipper Zero**: <https://flipperzero.one/>
- **Momentum Firmware**: <https://momentum-fw.dev/>
- **Official Documentation**: <https://docs.flipperzero.one/>

### Community Resources

- **GitHub - Flipper Zero**: <https://github.com/flipperdevices>
- **Sub-GHz Database**: <https://github.com/UberGuidoZ/Flipper>
- **IR Database**: <https://github.com/Lucaslhm/Flipper-IRDB>
- **Discord Community**: Active support and sharing

### Learning Materials

- **RF Fundamentals**: Study radio frequency basics
- **Protocol Analysis**: Learn common wireless protocols
- **Penetration Testing**: OSCP, CEH, or similar certifications
- **Hardware Hacking**: Joe Grand's resources, Hardware Hacking Village

### Legal/Ethical Frameworks

- **NIST Cybersecurity Framework**
- **PTES (Penetration Testing Execution Standard)**
- **OWASP Testing Guide**

---

## Contributing

Found a cool project or technique? Contributions welcome! Please ensure all submissions:

- Include ethical use disclaimers
- Provide educational value
- Follow legal guidelines
- Include proper attribution

---

## Disclaimer

This guide is provided for educational and authorized security research purposes only. The author(s) assume no liability for misuse of this information. You are responsible for complying with all applicable laws and regulations in your jurisdiction. When in doubt, consult with legal counsel before conducting any security testing.

**Remember**: Just because you *can* do something doesn't mean you *should*. With great power comes great responsibility—and potentially great legal liability.

---

## License

This guide is released under MIT License. Feel free to share, modify, and distribute with attribution.
