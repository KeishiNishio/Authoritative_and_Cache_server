# Cache DNS Server and Authoritative Server Setup

---

## Overview

This repository provides configuration files for building a DNS server using NSD (Name Server Daemon) and Unbound, leveraging Docker to create a custom DNS server environment. NSD serves as the authoritative DNS server, while Unbound functions as the cache DNS server.

## Technology Stack

- Docker
- Docker Compose
- NSD (Authoritative DNS Server)
- Unbound (Cache DNS Server)

## System Configuration

- **Authoritative DNS Server (NSD)**: Handles name resolution for the domain. Contains the zone file for `example.com`, managing information related to this domain.
- **Cache DNS Server (Unbound)**: Temporarily stores DNS query results for efficient name resolution. The cache server also forwards queries to external DNS servers.

### **System Architecture Diagram**
![System Architecture Diagram](https://github.com/KeishiNishio/Authoritative_and_Cache_server/blob/main/systemimage.png)

### **Reason for Choosing NSD**

Traditionally, BIND has been widely used for DNS implementations. However, due to frequent vulnerabilities and security challenges in BIND, NSD was chosen as a superior alternative for its enhanced security, specialization in read-only functions, and simpler configuration compared to BIND.

Reference:
[NSD vs BIND Comparison](https://qiita.com/ohhara_shiojiri/items/497aaba989151fa84b3d)

## File Structure

A brief overview of the key files and directories:

- `docker-compose.yml`
  - Docker Compose file containing Docker configurations for NSD and Unbound DNS services, defining build settings, port mapping, and network configurations for each service.
- `authoritative/`
  - Files related to the authoritative DNS server.
    - `Dockerfile`: Docker file directing the installation of NSD and configuration files.
    - `nsd.conf`: Configuration definitions for NSD.
    - `zonefile.zone`: DNS zone information.
- `cache/`
  - Files related to the cache DNS server.
    - `Dockerfile`: Docker file for installing and configuring the Unbound DNS cache server.
    - `unbound.conf`: Configuration definitions for Unbound.
- `README.md`
- `component.txt`: Describes the file composition.

## Behavior

1. DNS queries from clients are first sent to the cache server (Unbound).
2. The cache server forwards queries to the authoritative server (NSD) if no answer is in the cache.
3. The authoritative server responds to queries related to `example.com`, and the results are stored by the cache server.

## Verification Steps

To verify the system's functionality:

1. Launch the servers using Docker and Docker Compose.
2. Use the `dig` command to send queries to the cache server for `example.com`.
3. Resend the same query and observe reduced response times (effect of caching).

## Setup Instructions
    
1. **Build Docker Images**: Use the provided `Dockerfile` to build Docker images.
    
    ```bash
    docker-compose build --no-cache
    ```
    
2. **Launch Docker Containers**: Start the Docker containers with the command below.
    
    ```bash
    docker-compose up -d
    ```

### Verifying Functionality

Execute the following commands to receive corresponding IP addresses:

```bash
dig @localhost example.com
dig @localhost google.com
```

## Other Useful Commands

1. Delete all Docker cache system-wide:

    ```bash
    docker system prune --all --force
    ```

2. Check if containers are part of the configured network:

    ```bash
    docker network inspect nginx-network
    ```

3. Check the status of containers:

    ```bash
    docker ps -a
    ```

4. Stop all existing containers:

    ```bash
    docker stop $(docker ps -aq) || true
    ```

5. Remove all existing containers:

    ```bash
    docker rm -f $(docker ps -aq) || true
    ```



## Development Environment

This project is developed in the following environment:

- **Operating System**: macOS
- **Docker**:
  - Version: 24.0.5
- **Docker Compose**:
  - Version: 2.23.3
