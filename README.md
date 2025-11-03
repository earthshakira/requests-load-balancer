# requests-load-balancer

A Python library for load balancing HTTP requests across multiple hosts with automatic health checking and failover capabilities.

## Features

- **Round-robin load balancing**: Distributes requests evenly across multiple hosts
- **Automatic health checking**: Monitors host health based on HTTP status codes
- **Automatic failover**: Switches to healthy hosts when current host becomes unhealthy
- **Flexible error codes**: Customize which status codes indicate an unhealthy host
- **Simple API**: Drop-in replacement for common `requests` methods

## Installation

```bash
pip install requests-load-balancer
```

## Quick Start

```python
from requests_load_balancer import LoadBalancer

# Create a load balancer with multiple hosts
lb = LoadBalancer([
    'http://host1.example.com',
    'http://host2.example.com',
    'http://host3.example.com'
])

# Make requests - they will be automatically balanced
response = lb.get('/api/users')
print(response.json())

# POST request
response = lb.post('/api/users', json={'name': 'John Doe'})
```

## Usage

### Basic Load Balancing

```python
from requests_load_balancer import LoadBalancer

# Initialize with multiple hosts
lb = LoadBalancer([
    'http://api1.example.com',
    'http://api2.example.com',
])

# All standard HTTP methods are supported
response = lb.get('/endpoint')
response = lb.post('/endpoint', json={'key': 'value'})
response = lb.put('/endpoint', json={'key': 'value'})
response = lb.delete('/endpoint')
response = lb.patch('/endpoint', json={'key': 'value'})
```

### Custom Error Codes

By default, the following status codes mark a host as unhealthy: `500, 502, 503, 504`

You can customize this behavior:

```python
from requests_load_balancer import LoadBalancer

# Consider 404 and 429 as unhealthy as well
lb = LoadBalancer(
    hosts=['http://host1.com', 'http://host2.com'],
    error_codes={404, 429, 500, 502, 503, 504}
)
```

### Health Management

```python
# Get lists of healthy and unhealthy hosts
healthy = lb.get_healthy_hosts()
unhealthy = lb.get_unhealthy_hosts()

print(f"Healthy hosts: {healthy}")
print(f"Unhealthy hosts: {unhealthy}")

# Reset all hosts to healthy
lb.reset_health()
```

### Error Handling

When all hosts become unhealthy, a `RuntimeError` is raised:

```python
from requests_load_balancer import LoadBalancer

lb = LoadBalancer(['http://host1.com', 'http://host2.com'])

try:
    response = lb.get('/endpoint')
except RuntimeError as e:
    print(f"All hosts are unhealthy: {e}")
    # Optionally reset health and retry
    lb.reset_health()
```

## How It Works

1. **Round-Robin Distribution**: The load balancer cycles through hosts in order, distributing requests evenly.

2. **Health Checking**: When a request returns a status code in the `error_codes` set or raises a connection exception, that host is marked as unhealthy.

3. **Automatic Failover**: Unhealthy hosts are automatically skipped. The load balancer will try the next healthy host.

4. **Recovery**: Use `reset_health()` to mark all hosts as healthy again (useful for periodic health resets).

## Requirements

- Python >= 3.7
- requests >= 2.25.0

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Repository

https://github.com/GoCredit/requests-load-balancer