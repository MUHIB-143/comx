# muhibx - Ultra-Fast Async HTTP Client for Python

A high-performance, async-first networking module implemented entirely in Go and C/C++, with zero Python source code. Importable and usable like a native Python module.

[![Python 3.14+](https://img.shields.io/badge/python-3.14%2B-blue)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Status: Production](https://img.shields.io/badge/Status-Production%20Ready-green)](https://github.com)

## ✨ Features

### Core Features
- **7 HTTP Methods**: GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS
- **Connection Pooling**: 512 max idle connections, 256 per host
- **HTTP/2 Support**: Automatic HTTP/2 multiplexing
- **Keep-Alive**: 90-second idle connection timeout
- **Gzip Decompression**: Automatic response decompression

### Security Features
- **Security Modes**: STRICT (0), MONITOR (1), OFF (2)
- **TLS Verification**: Configurable certificate validation
- **MITM Detection**: Man-in-the-Middle attack detection
- **TLS Anomaly Detection**: Certificate expiration/checks
- **DNS Protection**: DNS hijacking detection
- **Proxy Detection**: Unauthorized proxy detection
- **Packet Sniffer Detection**: Detect network sniffers
- **Debugger Detection**: Detect attached debuggers
- **API Capture Detection**: Detect traffic capture tools (mitmproxy, Burp, etc.)
- **Auto Block**: Automatically block suspicious requests
- **IP Blocking**: Block specific IP addresses manually

### Developer Features
- **Developer Signature**: MUHIB-143 embedded on module import
- **Version**: 1.1.5
- **SILENT_MODE**: Compile-time flag to disable init message
- **Logging Levels**: ERROR (0), INFO (1), DEBUG (2)

## 🚀 Installation

### install with pip
```bash
pip install muhibx
```

### Verify Installation
```python
import muhibx

# Check module info
print(muhibx.__developer__)  # MUHIB-143
print(muhibx.__version__)    # 1.1.5
```

## 📖 API Reference

### Basic Usage

```python
import muhibx

# GET Request
response = muhibx.get("https://api.example.com/data")
print(response['status_code'])  # 200
print(response['text'])          # Response body
print(response['headers'])       # Response headers

# POST Request with data
response = muhibx.post("https://api.example.com/submit", "name=John&age=30")
print(response['status_code'])

# PUT Request
response = muhibx.put("https://api.example.com/users/1", '{"name": "Jane"}')

# DELETE Request
response = muhibx.delete("https://api.example.com/users/1")

# PATCH Request
response = muhibx.patch("https://api.example.com/users/1", '{"status": "active"}')

# HEAD Request (no body)
response = muhibx.head("https://api.example.com/data")

# OPTIONS Request
response = muhibx.options("https://api.example.com/data")
```

### Response Format

All methods return a dictionary:

```python
{
    'status_code': 200,           # HTTP status code (int)
    'text': '{"key": "value"}',   # Response body (str)
    'headers': {                   # Response headers (dict)
        'Content-Type': 'application/json',
        'Content-Length': '18',
        'Server': 'nginx/1.0'
    }
}
```

### Security Configuration

```python
import muhibx

# Set security mode
# 0 = STRICT (block suspicious activity)
# 1 = MONITOR (log only)
# 2 = OFF (no security checks)
muhibx.set_security_mode(1)

# Set log level
# 0 = ERROR
# 1 = INFO
# 2 = DEBUG
muhibx.set_log_level(2)

# Enable auto-blocking of suspicious requests
muhibx.enable_auto_block(1)

# Block a specific IP address
muhibx.block_ip("192.168.1.100")

# Get security statistics
stats = muhibx.get_security_stats()
print(stats)

# Detect packet sniffer
if muhibx.detect_sniffer():
    print("Warning: Packet sniffer detected!")

# Detect debugger
if muhibx.detect_debugger():
    print("Warning: Debugger detected!")

# Run full security scan
scan = muhibx.full_security_scan()
print(scan)
```

### Full Feature Examples

#### Example 1: JSON API Consumption
```python
import muhibx
import json

# Fetch JSON data
response = muhibx.get("https://jsonplaceholder.typicode.com/users/1")
data = json.loads(response['text'])

print(f"User: {data['name']}")
print(f"Email: {data['email']}")
print(f"Status: {response['status_code']}")
```

#### Example 2: POST JSON Data
```python
import muhibx
import json

payload = json.dumps({
    "title": "My Post",
    "body": "Content here",
    "userId": 1
})

response = muhibx.post(
    "https://jsonplaceholder.typicode.com/posts",
    payload
)

result = json.loads(response['text'])
print(f"Created post ID: {result['id']}")
```

#### Example 3: Error Handling
```python
import muhibx

try:
    response = muhibx.get("https://invalid-domain-12345.com")
except RuntimeError as e:
    print(f"Network error: {e}")
except Exception as e:
    print(f"Error: {e}")
```

#### Example 4: REST API CRUD Operations
```python
import muhibx
import json

base_url = "https://jsonplaceholder.typicode.com/posts"

# CREATE - POST
new_post = json.dumps({"title": "New", "body": "Content", "userId": 1})
response = muhibx.post(base_url, new_post)
post_id = json.loads(response['text'])['id']
print(f"Created: {post_id}")

# READ - GET
response = muhibx.get(f"{base_url}/{post_id}")
print(f"Read: {response['status_code']}")

# UPDATE - PUT
updated = json.dumps({"id": post_id, "title": "Updated", "body": "New content", "userId": 1})
response = muhibx.put(f"{base_url}/{post_id}", updated)
print(f"Update: {response['status_code']}")

# PATCH - PATCH
patched = json.dumps({"title": "Patched"})
response = muhibx.patch(f"{base_url}/{post_id}", patched)
print(f"Patch: {response['status_code']}")

# DELETE - DELETE
response = muhibx.delete(f"{base_url}/{post_id}")
print(f"Delete: {response['status_code']}")
```

#### Example 5: Check API Availability (HEAD)
```python
import muhibx

# Quick availability check (no body transfer)
response = muhibx.head("https://httpbin.org/get")
if response['status_code'] == 200:
    print("API is available")
    print(f"Server: {response['headers'].get('server', 'Unknown')}")
```

#### Example 6: CORS Preflight (OPTIONS)
```python
import muhibx

response = muhibx.options("https://api.example.com/data")
print(f"Allowed Methods: {response['headers'].get('allow', 'N/A')}")
```

#### Example 7: Security Configuration
```python
import muhibx

# Enable monitoring mode (log suspicious activity)
muhibx.set_security_mode(1)  # MONITOR
muhibx.set_log_level(2)      # DEBUG

# Make request
response = muhibx.get("https://httpbin.org/get")
print(f"Status: {response['status_code']}")

# Return to normal mode
muhibx.set_security_mode(2)  # OFF
muhibx.set_log_level(0)      # ERROR only
```

#### Example 8: Multiple Requests
```python
import muhibx

urls = [
    "https://httpbin.org/get",
    "https://httpbin.org/ip",
    "https://httpbin.org/headers"
]

for url in urls:
    response = muhibx.get(url)
    print(f"{url}: {response['status_code']}")
```

### Technical Details

- **Module**: muhibx.so
- **Type**: ELF 64-bit LSB shared object
- **Python**: 3.14+ extension module
- **Build Mode**: c-shared


## ⚡ Performance

- **Zero subprocess overhead**: Direct C extension call
- **Connection reuse**: Keep-alive pooling
- **HTTP/2 multiplexing**: Parallel requests on single connection
- **Minimal memory**: Efficient type conversions

## 🧪 Testing

```bash
# Quick test
python3 -c "import muhibx; print(muhibx.get('https://httpbin.org/get')['status_code'])"

```

## 📋 Requirements

- Python 3.*+
- Linux x86-64 or Linux ARM64
- Cross compiler for ARM64 builds: `aarch64-linux-gnu-gcc`
- Additional cross-compilers for Windows/macOS/Android targets

### Module Import Error
```python
# If you get import error, ensure you're in the correct directory
import sys
sys.path.insert(0, '/home')
import muhibx
```

### Network Errors
- Check internet connectivity
- Verify URL is accessible
- Check firewall/proxy settings

## 📄 License

MIT License - See [LICENSE](LICENSE) file for details.

---

Built by **MUHIB-143** 
