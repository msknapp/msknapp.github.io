
# Networking

## HTTP 1 vs. 2

### HTTP/1.0

- Connections lived as long as each request, they were never reused.
- Compression was not possible.

### HTTP/1.1

- Released in 1997.  Plain text protocol.  Supports multiple requests and responses from a single http connection.
- Supported the keep-alive header so a connection could be reused.
  It assumes by default that an HTTP connection should remain open until told to close.
- It is sequential and synchronous though, so if one response was slow, it was blocking all other requests.
  This is called Head of Line blocking.
- Sometimes multiple TCP connections were created to avoid HoL blocking, but this used excessive resources.
- The header file is always repeated, it is never compressed, and the messages are always plain-text.
  It only compresses the payload bodies.

### HTTP/2

- Is much faster.  Released in 2015
- Supports multiple requests over one connection by multiplexing.
- It uses a binary framing layer format.
- Single connections are made, a connection has multiple streams, and each stream enables message transmission.
  A message is split into frames.
- Frames get tagged with their stream, and interleaved over the wire.  This enables multiple requests and responses to 
  run concurrently without any blocking.
- Uses HPack to compress headers.  HPack uses huffman encoding.
- GRPC is only possible over HTTP/2

## Domain Name Service (DNS)

- Translates domain names (URL host names), into IP addresses.
- The /etc/hosts file can override DNS on the local machine.
- Your internet service provider implements and is responsible for DNS
- Requests go to a DNS resolver (aka recursor), which is a server hosted by your ISP 
  that interacts with other DNS servers to find IP addresses.
- **Root server:** knows what host can resolve any root domain.
- **TLD Server:** top level domain server, knows what host can resolve sub-domains of its own root domain.
- **Authoritative Name Server:** Knows the definitive IP address for the domain.
- Many servers cache the results of DNS queries for the “Time to Live” or TTL period.
- AWS Route53 implements DNS.
- Record Types:
  - **A:** basic IP
  - **CName:** like an alias.

## Network Layers

### Open System Interconnect (OSI) Model

1. Physical Link: cables and tangible connections.  Hubs, modems, repeaters.  Responsible for transmitting 1s and 0s as signals.
2. Data Link: MAC addresses and interface numbers in hardware.
3. Network: DNS, routes by addresses.  Internet Protocol (IP)
4. Transport: Uses transport control protocol (TCP), or Unary datagram protocol (UDP)
5. Session
6. Presentation
7. Application

## Terms

- **CIDR masks:** classless inter-domain routing.  The mask is four bytes, as binary the 1s must match, and the 0s do not need to match.
- **DHCP:** dynamic host configuration protocol.  Automatically assigns IP addresses and subnets to devices that join a network.

## Service Meshes

Istio
