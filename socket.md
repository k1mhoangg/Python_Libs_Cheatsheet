# Python Socket Library Cheatsheet

## Overview
The `socket` module provides low-level networking interfaces for creating client-server applications, supporting various protocols (TCP, UDP) and network operations.

```python
import socket
```

---

## Socket Types & Protocols

| Constant | Description | Usage |
|----------|-------------|-------|
| `socket.AF_INET` | IPv4 address family | Most common for internet |
| `socket.AF_INET6` | IPv6 address family | For IPv6 networks |
| `socket.SOCK_STREAM` | TCP socket (connection-oriented) | Reliable, ordered delivery |
| `socket.SOCK_DGRAM` | UDP socket (connectionless) | Fast, no guarantee |
| `socket.SOCK_RAW` | Raw socket | Low-level packet access |

### Common Combinations
```python
# TCP socket (most common)
tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# UDP socket
udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# IPv6 TCP socket
ipv6_socket = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
```

---

## Core Socket Methods

| Method | Description | Used By | Returns |
|--------|-------------|---------|---------|
| `socket.socket(family, type)` | Create new socket | Both | Socket object |
| `bind(address)` | Bind to address | Server | None |
| `listen(backlog)` | Enable server mode | Server | None |
| `accept()` | Accept connection | Server | (conn, address) |
| `connect(address)` | Connect to server | Client | None |
| `send(data)` | Send data (TCP) | Both | Bytes sent |
| `recv(bufsize)` | Receive data (TCP) | Both | Bytes data |
| `sendto(data, address)` | Send data (UDP) | Both | Bytes sent |
| `recvfrom(bufsize)` | Receive data (UDP) | Both | (data, address) |
| `close()` | Close socket | Both | None |
| `shutdown(how)` | Shutdown connection | Both | None |

---

## Socket Configuration Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `settimeout(value)` | Set timeout in seconds | None |
| `gettimeout()` | Get timeout value | Float or None |
| `setblocking(flag)` | Set blocking/non-blocking mode | None |
| `setsockopt(level, optname, value)` | Set socket option | None |
| `getsockopt(level, optname)` | Get socket option | Integer |
| `fileno()` | Get file descriptor | Integer |
| `getpeername()` | Get remote address | Address tuple |
| `getsockname()` | Get local address | Address tuple |

### Common Socket Options
```python
# Reuse address (prevents "Address already in use" error)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# Set timeout
sock.settimeout(5.0)  # 5 seconds
sock.settimeout(None)  # Blocking mode
sock.settimeout(0)  # Non-blocking mode

# Keep-alive
sock.setsockopt(socket.SOL_SOCKET, socket.SO_KEEPALIVE, 1)

# Buffer sizes
sock.setsockopt(socket.SOL_SOCKET, socket.SO_RCVBUF, 4096)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_SNDBUF, 4096)
```

---

## Module-Level Functions

| Function | Description | Returns |
|----------|-------------|---------|
| `gethostname()` | Get local hostname | String |
| `gethostbyname(hostname)` | Get IP from hostname | String IP |
| `gethostbyaddr(ip)` | Get hostname from IP | (hostname, aliases, IPs) |
| `getaddrinfo(host, port, family, type)` | Get address info | List of tuples |
| `inet_aton(ip)` | Convert IP to bytes | Bytes |
| `inet_ntoa(bytes)` | Convert bytes to IP | String IP |
| `getservbyname(service, protocol)` | Get port for service | Integer |
| `getservbyport(port, protocol)` | Get service for port | String |

### Examples
```python
# Get local hostname and IP
hostname = socket.gethostname()
local_ip = socket.gethostbyname(hostname)
print(f"{hostname}: {local_ip}")

# Resolve domain name
ip = socket.gethostbyname('www.google.com')
print(f"Google IP: {ip}")

# Reverse DNS lookup
try:
    hostname, aliases, ips = socket.gethostbyaddr('8.8.8.8')
    print(f"8.8.8.8 is {hostname}")
except socket.herror:
    print("Lookup failed")

# Get detailed address info
addr_info = socket.getaddrinfo('google.com', 80, socket.AF_INET, socket.SOCK_STREAM)
for info in addr_info:
    print(info)

# Service name to port
http_port = socket.getservbyname('http')  # Returns 80
ssh_port = socket.getservbyname('ssh')    # Returns 22
```

---

## TCP Server Example

```python
import socket

def tcp_server(host='127.0.0.1', port=8080):
    # Create socket
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    # Allow reuse of address
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    
    # Bind to address
    server_socket.bind((host, port))
    
    # Listen for connections (backlog of 5)
    server_socket.listen(5)
    print(f"Server listening on {host}:{port}")
    
    try:
        while True:
            # Accept connection
            client_socket, client_address = server_socket.accept()
            print(f"Connection from {client_address}")
            
            try:
                # Receive data
                data = client_socket.recv(1024)
                if data:
                    print(f"Received: {data.decode()}")
                    
                    # Send response
                    response = "Message received!"
                    client_socket.send(response.encode())
            finally:
                # Close client connection
                client_socket.close()
    except KeyboardInterrupt:
        print("\nServer shutting down...")
    finally:
        server_socket.close()

# Run server
tcp_server()
```

---

## TCP Client Example

```python
import socket

def tcp_client(host='127.0.0.1', port=8080, message='Hello Server!'):
    # Create socket
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        # Connect to server
        client_socket.connect((host, port))
        print(f"Connected to {host}:{port}")
        
        # Send data
        client_socket.send(message.encode())
        print(f"Sent: {message}")
        
        # Receive response
        response = client_socket.recv(1024)
        print(f"Received: {response.decode()}")
        
    except ConnectionRefusedError:
        print("Connection refused. Is the server running?")
    except socket.timeout:
        print("Connection timed out")
    finally:
        client_socket.close()

# Run client
tcp_client()
```

---

## UDP Server Example

```python
import socket

def udp_server(host='127.0.0.1', port=8080):
    # Create UDP socket
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    # Bind to address
    server_socket.bind((host, port))
    print(f"UDP Server listening on {host}:{port}")
    
    try:
        while True:
            # Receive data and sender address
            data, client_address = server_socket.recvfrom(1024)
            print(f"Received from {client_address}: {data.decode()}")
            
            # Send response back to client
            response = "ACK: Message received!"
            server_socket.sendto(response.encode(), client_address)
    except KeyboardInterrupt:
        print("\nServer shutting down...")
    finally:
        server_socket.close()

# Run server
udp_server()
```

---

## UDP Client Example

```python
import socket

def udp_client(host='127.0.0.1', port=8080, message='Hello UDP Server!'):
    # Create UDP socket
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    try:
        # Send data (no connection needed)
        client_socket.sendto(message.encode(), (host, port))
        print(f"Sent to {host}:{port}: {message}")
        
        # Set timeout for receiving
        client_socket.settimeout(5.0)
        
        # Receive response
        data, server_address = client_socket.recvfrom(1024)
        print(f"Received from {server_address}: {data.decode()}")
        
    except socket.timeout:
        print("No response from server")
    finally:
        client_socket.close()

# Run client
udp_client()
```

---

## Multi-Client TCP Server (Threading)

```python
import socket
import threading

def handle_client(client_socket, client_address):
    """Handle individual client connection."""
    print(f"[NEW CONNECTION] {client_address} connected")
    
    try:
        while True:
            # Receive data
            data = client_socket.recv(1024)
            if not data:
                break  # Client disconnected
            
            message = data.decode()
            print(f"[{client_address}] {message}")
            
            # Echo back
            client_socket.send(f"Echo: {message}".encode())
    except Exception as e:
        print(f"[ERROR] {client_address}: {e}")
    finally:
        client_socket.close()
        print(f"[DISCONNECTED] {client_address}")

def multi_client_server(host='127.0.0.1', port=8080):
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server_socket.bind((host, port))
    server_socket.listen(5)
    
    print(f"[STARTED] Server listening on {host}:{port}")
    
    try:
        while True:
            client_socket, client_address = server_socket.accept()
            
            # Create new thread for each client
            thread = threading.Thread(
                target=handle_client,
                args=(client_socket, client_address)
            )
            thread.daemon = True
            thread.start()
            
            print(f"[ACTIVE CONNECTIONS] {threading.active_count() - 1}")
    except KeyboardInterrupt:
        print("\n[SHUTDOWN] Server shutting down...")
    finally:
        server_socket.close()

# Run server
multi_client_server()
```

---

## HTTP Client Example

```python
import socket

def simple_http_client(host, port=80, path='/'):
    # Create socket
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        # Connect
        sock.connect((host, port))
        
        # Create HTTP GET request
        request = f"GET {path} HTTP/1.1\r\n"
        request += f"Host: {host}\r\n"
        request += "Connection: close\r\n"
        request += "\r\n"
        
        # Send request
        sock.send(request.encode())
        
        # Receive response
        response = b""
        while True:
            chunk = sock.recv(4096)
            if not chunk:
                break
            response += chunk
        
        # Decode and print
        print(response.decode('utf-8', errors='ignore'))
        
    finally:
        sock.close()

# Usage
simple_http_client('example.com')
```

---

## Port Scanner Example

```python
import socket
from datetime import datetime

def scan_port(host, port, timeout=1):
    """Check if a port is open."""
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(timeout)
        result = sock.connect_ex((host, port))
        sock.close()
        return result == 0
    except socket.error:
        return False

def port_scanner(host, start_port=1, end_port=1024):
    """Scan range of ports."""
    print(f"Scanning {host} from port {start_port} to {end_port}")
    print(f"Started at: {datetime.now()}\n")
    
    open_ports = []
    
    for port in range(start_port, end_port + 1):
        if scan_port(host, port):
            print(f"Port {port}: OPEN")
            open_ports.append(port)
    
    print(f"\nScan completed at: {datetime.now()}")
    print(f"Open ports: {open_ports}")
    return open_ports

# Usage
port_scanner('127.0.0.1', 1, 100)
```

---

## Chat Application Example

### Chat Server
```python
import socket
import threading

clients = []
clients_lock = threading.Lock()

def broadcast(message, sender_socket=None):
    """Send message to all clients except sender."""
    with clients_lock:
        for client in clients:
            if client != sender_socket:
                try:
                    client.send(message)
                except:
                    clients.remove(client)

def handle_chat_client(client_socket, address):
    """Handle individual chat client."""
    print(f"[+] {address} joined the chat")
    
    try:
        # Get username
        client_socket.send("Enter your username: ".encode())
        username = client_socket.recv(1024).decode().strip()
        
        welcome = f"[SERVER] {username} joined the chat!"
        broadcast(welcome.encode())
        
        while True:
            message = client_socket.recv(1024)
            if not message:
                break
            
            full_message = f"[{username}] {message.decode()}"
            print(full_message)
            broadcast(full_message.encode(), client_socket)
    except:
        pass
    finally:
        with clients_lock:
            if client_socket in clients:
                clients.remove(client_socket)
        client_socket.close()
        
        leave_msg = f"[SERVER] {username} left the chat"
        broadcast(leave_msg.encode())
        print(f"[-] {address} disconnected")

def chat_server(host='127.0.0.1', port=9999):
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind((host, port))
    server.listen()
    
    print(f"[*] Chat server running on {host}:{port}")
    
    try:
        while True:
            client_socket, address = server.accept()
            
            with clients_lock:
                clients.append(client_socket)
            
            thread = threading.Thread(
                target=handle_chat_client,
                args=(client_socket, address)
            )
            thread.daemon = True
            thread.start()
    except KeyboardInterrupt:
        print("\n[!] Server shutting down...")
    finally:
        server.close()

# Run server
chat_server()
```

### Chat Client
```python
import socket
import threading

def receive_messages(sock):
    """Receive and print messages from server."""
    while True:
        try:
            message = sock.recv(1024).decode()
            if message:
                print(message)
            else:
                break
        except:
            break

def chat_client(host='127.0.0.1', port=9999):
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        client.connect((host, port))
        print(f"Connected to chat server at {host}:{port}")
        
        # Start receiving thread
        receive_thread = threading.Thread(target=receive_messages, args=(client,))
        receive_thread.daemon = True
        receive_thread.start()
        
        # Send messages
        while True:
            message = input()
            if message.lower() == '/quit':
                break
            client.send(message.encode())
    except KeyboardInterrupt:
        print("\nDisconnecting...")
    finally:
        client.close()

# Run client
chat_client()
```

---

## File Transfer Example

### File Server
```python
import socket
import os

def file_server(host='127.0.0.1', port=5000, directory='shared_files'):
    os.makedirs(directory, exist_ok=True)
    
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind((host, port))
    server.listen(1)
    
    print(f"File server running on {host}:{port}")
    
    try:
        while True:
            client, address = server.accept()
            print(f"Connection from {address}")
            
            try:
                # Receive filename
                filename = client.recv(1024).decode()
                filepath = os.path.join(directory, filename)
                
                # Check if file exists
                if os.path.exists(filepath):
                    client.send(b"OK")
                    
                    # Send file size
                    filesize = os.path.getsize(filepath)
                    client.send(str(filesize).encode())
                    
                    # Send file
                    with open(filepath, 'rb') as f:
                        while True:
                            data = f.read(4096)
                            if not data:
                                break
                            client.send(data)
                    
                    print(f"Sent: {filename} ({filesize} bytes)")
                else:
                    client.send(b"NOT_FOUND")
            except Exception as e:
                print(f"Error: {e}")
            finally:
                client.close()
    except KeyboardInterrupt:
        print("\nServer shutting down...")
    finally:
        server.close()

# Run server
file_server()
```

### File Client
```python
import socket
import os

def download_file(host, port, filename, save_as=None):
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        client.connect((host, port))
        
        # Request file
        client.send(filename.encode())
        
        # Check if file exists
        response = client.recv(2).decode()
        if response != "OK":
            print(f"File not found: {filename}")
            return
        
        # Receive file size
        filesize = int(client.recv(1024).decode())
        print(f"Downloading {filename} ({filesize} bytes)...")
        
        # Receive file
        save_name = save_as or filename
        received = 0
        
        with open(save_name, 'wb') as f:
            while received < filesize:
                data = client.recv(4096)
                if not data:
                    break
                f.write(data)
                received += len(data)
                
                # Progress
                progress = (received / filesize) * 100
                print(f"\rProgress: {progress:.1f}%", end='')
        
        print(f"\nDownload complete: {save_name}")
    finally:
        client.close()

# Usage
download_file('127.0.0.1', 5000, 'example.txt')
```

---

## Exception Handling

| Exception | Description | When It Occurs |
|-----------|-------------|----------------|
| `socket.error` | General socket error | Various socket operations |
| `socket.timeout` | Operation timed out | After settimeout() expires |
| `socket.gaierror` | Address-related error | DNS lookup failures |
| `socket.herror` | Host-related error | gethostbyname/addr failures |
| `ConnectionRefusedError` | Connection refused | Server not listening |
| `ConnectionResetError` | Connection reset | Connection lost |
| `BrokenPipeError` | Broken pipe | Write to closed socket |

### Exception Handling Example
```python
import socket

def safe_socket_operation(host, port):
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(5)
    
    try:
        sock.connect((host, port))
        sock.send(b"Hello")
        data = sock.recv(1024)
        print(f"Received: {data.decode()}")
        
    except socket.timeout:
        print("Connection timed out")
    except ConnectionRefusedError:
        print("Connection refused - server not running?")
    except socket.gaierror as e:
        print(f"Address-related error: {e}")
    except socket.error as e:
        print(f"Socket error: {e}")
    except Exception as e:
        print(f"Unexpected error: {e}")
    finally:
        sock.close()
```

---

## Best Practices

### ✅ Do's
1. **Always close sockets** - Use `try/finally` or context managers
2. **Set timeouts** - Prevent indefinite blocking
3. **Use SO_REUSEADDR** - Avoid "Address already in use" errors
4. **Handle exceptions** - Network operations can fail
5. **Use threading/async** - For multiple concurrent connections
6. **Validate data** - Check received data before processing
7. **Use proper encoding** - `encode()`/`decode()` for strings
8. **Check return values** - `send()` may not send all data

```python
# Good: Context manager (Python 3+)
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
    sock.connect(('localhost', 8080))
    sock.send(b"data")
# Automatically closed

# Good: Manual cleanup
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
    sock.connect(('localhost', 8080))
finally:
    sock.close()
```

### ❌ Don'ts
1. **Don't ignore errors** - Always handle exceptions
2. **Don't forget to close** - Causes resource leaks
3. **Don't assume send() sends all** - Use `sendall()` instead
4. **Don't use blocking sockets in UI** - Use threads/async
5. **Don't hardcode buffer sizes** - May truncate data
6. **Don't trust user input** - Validate before using
7. **Don't mix blocking modes** - Be consistent

```python
# Bad: May not send all data
sock.send(large_data)

# Good: Ensures all data is sent
sock.sendall(large_data)

# Bad: May lose data
data = sock.recv(1024)  # What if data > 1024?

# Good: Loop until all received
chunks = []
while True:
    chunk = sock.recv(4096)
    if not chunk:
        break
    chunks.append(chunk)
data = b''.join(chunks)
```

---

## Common Patterns

### Pattern 1: Send All Data
```python
def send_all(sock, data):
    """Ensure all data is sent."""
    data = data.encode() if isinstance(data, str) else data
    sock.sendall(data)
```

### Pattern 2: Receive Exact Amount
```python
def recv_exact(sock, size):
    """Receive exactly size bytes."""
    data = b''
    while len(data) < size:
        chunk = sock.recv(size - len(data))
        if not chunk:
            raise ConnectionError("Connection closed")
        data += chunk
    return data
```

### Pattern 3: Send/Receive with Length Prefix
```python
import struct

def send_message(sock, message):
    """Send message with 4-byte length prefix."""
    data = message.encode() if isinstance(message, str) else message
    length = struct.pack('!I', len(data))  # Network byte order
    sock.sendall(length + data)

def recv_message(sock):
    """Receive message with length prefix."""
    length_data = recv_exact(sock, 4)
    length = struct.unpack('!I', length_data)[0]
    return recv_exact(sock, length)
```

### Pattern 4: Context Manager
```python
from contextlib import contextmanager

@contextmanager
def create_connection(host, port, timeout=None):
    """Context manager for socket connections."""
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    if timeout:
        sock.settimeout(timeout)
    try:
        sock.connect((host, port))
        yield sock
    finally:
        sock.close()

# Usage
with create_connection('localhost', 8080, timeout=5) as sock:
    sock.send(b"Hello")
    data = sock.recv(1024)
```

---

## Performance Tips

### 1. Buffer Sizes
```python
# Adjust buffer sizes for better performance
sock.setsockopt(socket.SOL_SOCKET, socket.SO_RCVBUF, 65536)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_SNDBUF, 65536)
```

### 2. TCP_NODELAY (Disable Nagle's Algorithm)
```python
# For real-time applications (reduces latency)
sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)
```

### 3. Non-Blocking Sockets with Select
```python
import select

# Non-blocking server
server.setblocking(0)
sockets = [server]

while True:
    readable, _, _ = select.select(sockets, [], [], 1)
    for sock in readable:
        if sock is server:
            client, addr = server.accept()
            sockets.append(client)
        else:
            data = sock.recv(1024)
            if data:
                sock.send(data)  # Echo
            else:
                sockets.remove(sock)
                sock.close()
```

---

## Security Considerations

### ⚠️ Important Security Notes

1. **Input Validation**
```python
# Always validate and sanitize input
def safe_filename(filename):
    # Remove path traversal attempts
    return os.path.basename(filename)
```

2. **Limit Data Size**
```python
# Prevent DoS by limiting receive size
MAX_SIZE = 1024 * 1024  # 1 MB
data = sock.recv(min(requested_size, MAX_SIZE))
```

3. **Use SSL/TLS for Sensitive Data**
```python
import ssl

# Wrap socket with SSL
context = ssl.create_default_context()
secure_sock = context.wrap_socket(sock, server_hostname=hostname)
```

4. **Set Timeouts**
```python
# Prevent resource exhaustion
sock.settimeout(30)  # 30 second timeout
```

---

## Quick Reference

```python
# Create socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Server
sock.bind(('0.0.0.0', 8080))
sock.listen(5)
client, addr = sock.accept()

# Client
sock.connect(('localhost', 8080))

# Send/Receive (TCP)
sock.sendall(b'data')
data = sock.recv(4096)

# Send/Receive (UDP)
sock.sendto(b'data', ('localhost', 8080))
data, addr = sock.recvfrom(4096)

# Configuration
sock.settimeout(5)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# Cleanup
sock.close()

# Context manager (auto-close)
with socket.socket() as sock:
    pass
```

---

## Useful Resources

- **Official Docs**: https://docs.python.org/3/library/socket.html
- **HOWTO**: https://docs.python.org/3/howto/sockets.html
- For production: Consider using higher-level libraries like `asyncio`, `twisted`, or `socketio`
- For HTTP: Use `requests` or `httpx` instead of raw sockets
