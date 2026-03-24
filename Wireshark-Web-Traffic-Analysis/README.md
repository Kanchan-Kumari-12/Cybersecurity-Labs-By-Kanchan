# Web Traffic Analysis using Wireshark

## Objective
To capture real network traffic and analyze TCP/IP layers and a complete web request.

## Tools Used
- tcpdump
- Wireshark
- curl

## Steps Performed
1. Captured traffic using tcpdump
2. Generated HTTPS request using curl
3. Opened capture in Wireshark
4. Applied IP filter
5. Analyzed DNS, TCP handshake, and TLS handshake

## Observations

### TCP Handshake
- SYN → SYN-ACK → ACK (3 packets)

### Source Port
- Client used ephemeral port (41412)

### TTL Analysis
- Client TTL ≈ 64
- Server TTL 118

### Encryption
- HTTP content not visible
- Reason: HTTPS uses TLS encryption

## Conclusion
This lab helped in understanding packet-level communication, TCP handshake, and the role of encryption in securing web traffic.
