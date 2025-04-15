# DNS for Data Center Engineers

![DNS Overview](./assets/dns-overview.png)

## Overview

The Domain Name System (DNS) is a foundational component of modern network infrastructure. In a data center, DNS supports internal service discovery, external connectivity, and traffic routing. It resolves domain names (e.g., `app01.dc.local`) into IP addresses (e.g., `10.120.0.25`) — enabling seamless machine communication.

Key Features of DNS in Data Centers:
- Hierarchical & distributed
- Supports caching to reduce latency
- Resilient through redundancy
- Used for service discovery in microservices

---

## DNS Query Process (Full Resolution Path)

![DNS Resolution Path](./assets/dns-overview.png)

1. **Client Resolver (Stub)**:
   - Initiates a request to resolve a domain.
   - Sends query to a **recursive resolver**.

2. **Recursive Resolver**:
   - Looks up the domain from cache or forwards the query up the DNS hierarchy.

3. **Root Server**:
   - Responds with the address of the TLD server (e.g., `.com`, `.org`, `.local`).

4. **TLD Server**:
   - Points the resolver to the **authoritative server** for the domain.

5. **Authoritative Name Server**:
   - Responds with the final IP address for the domain.

6. **Response to Client**:
   - Resolver returns the IP to the client. Caching occurs at every layer.

---

## Types of DNS in Data Centers

![Types of DNS](./assets/types-of-dns.png)

### 1. Recursive DNS
- Performs full resolution on behalf of the client.
- Often runs on internal BIND9 or Unbound servers.
- Provides caching, which reduces query times.

**Example Use Case**: Internal resolver for all employee queries, forwarding to Google DNS (8.8.8.8) or upstream ISP DNS.

---

### 2. Caching DNS
- Stores previous query results.
- Reduces traffic to authoritative servers.
- TTL (time-to-live) controls how long results are cached.

**Example**: Edge DNS caching resolver for remote office DNS acceleration.

---

### 3. Authoritative DNS
- Holds zone files and DNS records (A, AAAA, CNAME, PTR, TXT).
- Responds only for domains it manages.
- No recursion.

**Example**: A DNS server authoritative for `dc1.bloomberg.net` zone.

---

### 4. Forwarding DNS
- Forwards all queries to another DNS server instead of resolving them directly.
- Used to centralize outbound DNS queries.

**Example**: All remote office DNS traffic is forwarded to a centralized HQ resolver.

---

## DNS Records Used in Data Centers

| Record Type | Description | Example |
|-------------|-------------|---------|
| A           | IPv4 address | `app01.dc -> 10.10.0.5` |
| AAAA        | IPv6 address | `app01.dc -> fe80::1` |
| PTR         | Reverse lookup | `10.10.0.5 -> app01.dc` |
| CNAME       | Alias | `web -> app01.dc` |
| MX          | Mail exchange | `mail.dc -> smtp1.dc` |
| SRV         | Service locator | `_ldap._tcp.dc -> ldap1.dc` |
| TXT         | Arbitrary text | SPF, DKIM, metadata |

---

## DNS Troubleshooting in a Data Center

![DNS Troubleshooting](./assets/dns-troubleshooting.png)

### Step-by-Step Workflow

1. **Check Local Resolver Config**:
   - File: `/etc/resolv.conf` or `systemd-resolved`
   - Confirm DNS server IPs are reachable

2. **Test Resolution**:
   ```bash
   dig example.com
   nslookup example.com
   host example.com
   systemd-resolve example.com
