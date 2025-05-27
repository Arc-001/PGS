# PGS (Pretty Good Socket)

PGS (Pretty Good Socket) is a Python-based secure socket communication framework that implements PGP (Pretty Good Privacy) encryption for end-to-end secure message exchange between clients and servers. It features automatic key management, two-stage authentication, and encrypted communication channels.

## Features

- **PGP Encryption**: RSA 2048-bit PGP encryption for all communications
- **Automatic Key Management**: Generates and manages PGP key pairs automatically
- **Two-Stage Authentication**: Secure handshake with key exchange and credential verification
- **Concurrent Connections**: Multi-threaded server supporting multiple simultaneous clients
- **Message Queuing**: Advanced message box system with blocking and non-blocking operations
- **Command Execution**: Remote command execution capabilities with secure API
- **Extensible Architecture**: Modular design for easy customization and extension

## Project Structure

```
PGS/
├── README.md
├── com/
│   ├── __init__.py
│   ├── SocketData.py           # Message handling and queuing system
│   ├── socket_all.py           # Basic socket implementation (non-encrypted)
│   ├── server_socket.py        # Secure server with PGP encryption
│   ├── client_socket.py        # Secure client with PGP encryption
│   └── encyption/
│       ├── __init__.py
│       ├── key_gen.py          # PGP key generation and crypto utilities
│       ├── test_enc.py         # Encryption testing and examples
│       ├── test                # Sample private key (for testing)
│       └── test.pub            # Sample public key (for testing)
├── connect.py                  # Client connection script with env support
├── test_server.py              # Server testing script
├── test_client.py              # Client testing script
└── exec_commands.py            # Command execution API (referenced)
```

## Installation

### Prerequisites

- Python 3.7+
- PGPy library
- python-dotenv (for environment configuration)

### Install Dependencies

```bash
pip install pgpy python-dotenv
```

## Quick Start

### 1. Starting a Secure Server

```python
from com.server_socket import socket_server

# Create and start server with PGP encryption
server = socket_server(host="0.0.0.0", port=5000, max_listen=5)
# Server automatically generates keys and starts listening for encrypted connections
```

### 2. Connecting a Client

```python
from com.client_socket import socket_client

# Connect to server with automatic PGP handshake
client = socket_client(
    host="localhost", 
    port=5000, 
    passcode="test_passcode"
)
```

### 3. Environment-based Connection

Create a `.env` file:
```env
REMOTE_HOST=localhost
REMOTE_PORT=5000
PASSWORD=your_secure_password
```

Then use the connection script:
```python
python connect.py
```

## Authentication Flow

### Stage 1: Key Exchange
1. Client connects and sends public key with metadata
2. Server responds with its public key
3. Both parties confirm successful key exchange using encrypted messages

### Stage 2: Credential Verification
1. Client sends encrypted credentials (username/password)
2. Server decrypts and verifies against stored credentials
3. Authentication success/failure response sent back encrypted

## Key Management

- **Automatic Generation**: Keys are automatically generated using RSA 2048-bit encryption
- **Persistent Storage**: 
  - Private keys: `com/encryption/keys/rsa`
  - Public keys: `com/encryption/keys/rsa.pub`
- **Key Reuse**: Keys persist between sessions for consistent identity

## Message Security

- **End-to-End Encryption**: All messages after handshake are PGP encrypted
- **Per-Client Encryption**: Each client has unique encryption context
- **Digital Signatures**: Messages are both encrypted and signed
- **Forward Secrecy**: Each session uses fresh encryption context

## API Reference

### Server Methods

```python
# Send encrypted message to specific client
server.send_message(client_addr, "Hello, secure world!")

# Broadcast encrypted message to all connected clients
server.send_all("Broadcast message")

# Get list of authenticated connections
connections = server.get_established_connections()

# Access message box for specific client
msg_box = server.get_msg_box(client_addr)

# Graceful connection closure
server.close_conn(client_addr)
server.close_server()  # Close all connections
```

### Client Methods

```python
# Send encrypted message to server
client.send_message("Hello from client!")

# Get message history
recv_msgs = client.get_all_recv_messages()
sent_msgs = client.get_all_sent_messages()

# Access message box for advanced operations
msg_box = client.get_post_box()

# Blocking message reception
message = msg_box.get_msg_from_now()

# Close connection
client.close_conn()
```

### Message Box Operations

```python
# Non-blocking message retrieval
message = msg_box.get_recv_message()

# Blocking message retrieval (waits for next message)
message = msg_box.get_msg_from_now()

# Message history management
msg_box.clear_recv_messages()
msg_box.set_recv_cursor(position)
```

## Configuration

### Server Configuration

```python
server = socket_server(
    host="0.0.0.0",      # Listen address
    port=5000,           # Listen port
    max_listen=10,       # Max concurrent connections
    data_len=3072        # Max message length
)
```

### Client Configuration

```python
client = socket_client(
    host="localhost",     # Server address
    port=5000,           # Server port
    data_len=3072,       # Max message length
    passcode="password", # Authentication password
    hostname="client1"   # Client identifier
)
```

### Environment Variables

```env
REMOTE_HOST=your.server.com
REMOTE_PORT=5000
PASSWORD=your_secure_password
```

## Testing

### Test Encryption

```bash
cd com/encyption/
python test_enc.py
```

### Test Server

```bash
python test_server.py
```

### Test Client

```bash
python test_client.py
```

## Remote Command Execution

PGS includes a secure command execution framework:

```python
# Server can send commands to authenticated clients
server.send_message(client_addr, "command_name()")

# Client executes and returns encrypted results
# Results are automatically encrypted and sent back
```

## Security Considerations

**Important Security Notes:**

- **Credential Storage**: Current implementation uses hardcoded credentials for demonstration
- **Production Deployment**: Implement proper user database and secure password storage
- **Key Rotation**: Consider implementing regular key rotation for enhanced security
- **Network Security**: Use over secure networks; consider additional transport encryption
- **Access Control**: Implement proper access controls for command execution features

## Development

### Adding New Commands

1. Define command functions in `exec_commands.py`
2. Commands are automatically callable via the API
3. Results are JSON-formatted and encrypted

### Extending Authentication

1. Modify `uuid_pass_dict` in server initialization
2. Implement database integration for user management
3. Add additional authentication factors as needed

## Troubleshooting

### Common Issues

1. **Key Generation Fails**: Ensure write permissions to `com/encryption/keys/`
2. **Connection Refused**: Check firewall settings and port availability
3. **Authentication Failed**: Verify credentials match server configuration
4. **Message Corruption**: Ensure consistent encoding (UTF-8) across client/server

### Debug Mode

Enable debug output by checking console messages during handshake process.

## License

This project is open source. Please ensure compliance with local encryption laws and regulations when using PGP encryption.

## Contributing

Contributions welcome! Please ensure:
- All cryptographic implementations follow security best practices
- Maintain backward compatibility for key formats
- Include tests for new features
- Document security implications of changes

## Roadmap

- [ ] Database integration for user management
- [ ] Certificate-based authentication
- [ ] Automated key rotation mechanisms
- [ ] Comprehensive logging and audit trails
- [ ] Configuration file support
- [ ] Docker containerization
- [ ] Web interface for management

## Authors

- arc (original author)

---
