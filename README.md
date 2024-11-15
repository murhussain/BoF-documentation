# Buffer Overflow Attack Guide
[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)

A comprehensive guide to understanding and exploiting buffer overflow vulnerabilities in Windows applications. This repository contains step-by-step tutorials and scripts for learning buffer overflow attacks in a controlled environment.

## ⚠️ Disclaimer

This guide is for educational purposes only. The techniques demonstrated should only be used in authorized test environments. Unauthorized testing or exploitation may be illegal.

## 📚 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Environment Setup](#environment-setup)
3. [Attack Phases](#attack-phases)
4. [File Structure](#file-structure)
5. [Quick Start](#quick-start)
6. [Detailed Guides](#detailed-guides)

## Prerequisites

- Windows VM (target machine)
  - Immunity Debugger
  - Python installed
  - vulnserver running
- Kali Linux VM (attacker machine)
  - Python3
  - Metasploit Framework
  - netcat

## Environment Setup

1. **Windows Target Setup**:
   ```bash
   # Start vulnserver
   # Default port: 9999
   
   # Start Immunity Debugger as Administrator
   # Attach to vulnserver.exe process
   ```

2. **Kali Linux Setup**:
   ```bash
   # Verify connection
   nc -nv TARGET_IP 9999
   
   # Should see vulnserver banner
   ```

## Attack Phases

1. **Initial Spiking** (`Part-1`)
   - Test commands for vulnerabilities
   - Identify potential overflow points
   - Initial crash testing

2. **Fuzzing & Offset** (`Part-2`)
   - Send incrementing buffers
   - Crash point identification
   - Exact offset calculation

3. **Bad Characters & Return Address** (`Part-3`)
   - Bad character identification
   - Finding JMP ESP instruction
   - Return address verification

4. **Shellcode & Exploitation** (`Part-4`)
   - Shellcode generation
   - NOP sled implementation
   - Final exploit execution

## File Structure

```
buffer-overflow-guide/
│
├── guides/
│   ├── Part-1-Initial-Setup-and-Spiking.md
│   ├── Part-2-Fuzzing-and-Finding-Offset.md
│   ├── Part-3-Bad-Characters-and-Return-Address.md
│   └── Part-4-Shellcode-and-Final-Exploit.md
└── README.md
```

## Quick Start

1. Clone the repository:
   ```bash
   git clone https://github.com/murhussain/BoF-documentation
   cd buffer-overflow-guide
   ```

2. Set up your environment:
   ```bash
   # Update scripts with your target IP
   sed -i 's/172.29.98.109/YOUR_TARGET_IP/' scripts/*.py
   ```

3. Follow the phases:
   ```bash
   # 1. Initial spiking
   python3 scripts/trun_test.py
   
   # 2. Fuzzing
   python3 scripts/fuzzer.py
   
   # 3. Pattern creation
   msf-pattern_create -l 2400
   
   # 4. Final exploit
   python3 scripts/exploit.py
   ```

## Detailed Guides

Each phase has a detailed markdown guide in the `./` directory:

1. [Initial Setup and Spiking](./Part-1.md)
   - Environment preparation
   - Basic vulnerability testing
   - Initial crash confirmation

2. [Fuzzing and Finding Offset](./Part-2.md)
   - Buffer size determination
   - Offset calculation
   - EIP control verification

3. [Bad Characters and Return Address](./Part-3.md)
   - Character set testing
   - JMP ESP location
   - Return address verification

4. [Shellcode and Final Exploit](./Part-4.md)
   - Payload generation
   - Shell code implementation
   - Reverse shell execution

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
