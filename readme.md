# Homelab Unit Files

This repository contains unit files for deploying applications on my homelab.

## Prerequisites

### System Requirements

- **openSUSE MicroOS** (recommended) or any systemd-based Linux distribution
- **Podman 4.4+** with Quadlet support
- **systemd 250+**
- **Root access** for system-wide services

### Verify Quadlet Support

```bash
# Check Podman version (needs 4.4+)
podman version

# Check systemd version (needs 250+)
systemctl --version

# Verify Quadlet is available
man podman-systemd.unit
```

## Applications

### Pi-hole
**DNS sinkhole with web interface for network-wide ad blocking**

- **Ports**: 53/tcp, 53/udp (DNS), 8080/tcp (Web UI)
- **Volumes**: Configuration and DNS settings persistence
- **Capabilities**: NET_ADMIN for network management
- **Web Interface**: `http://your-server:8080/admin`

### Uptime Kuma
**Self-hosted monitoring tool with status page**

- **Ports**: 3001/tcp (Web UI)
- **Volumes**: Application data and configuration
- **Web Interface**: `http://your-server:3001`

## Manual Installation

### 1. Copy Unit Files

```bash
sudo cp app-name/*.container /etc/containers/systemd/
sudo cp app-name/*.volume /etc/containers/systemd/
sudo cp app-name/*.network /etc/containers/systemd/  # if applicable
```

### 2. Reload and Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now app-name.service
```

### 3. Check Status

```bash
sudo systemctl status app-name.service
```

## Troubleshooting

### Common Issues

#### 1. Service Fails to Start

```bash
# Check service status
sudo systemctl status app-name.service

# View detailed logs
sudo journalctl -u app-name.service -f

# Check container logs
podman logs container-name
```

#### 2. Port Already in Use

```bash
# Check what's using the port
sudo ss -tulpn | grep :53

# Stop conflicting services
sudo systemctl stop systemd-resolved  # if using port 53
```

#### 3. Volume Permission Issues

```bash
# Check volume location
podman volume inspect volume-name
```

#### 4. Image Pull Failures

```bash
# Manually pull image to check connectivity
podman pull docker.io/image:tag

# Check registry configuration
cat /etc/containers/registries.conf
```

### Debugging Commands

```bash
# List all containers
podman ps -a

# List all volumes
podman volume ls

# Check systemd unit files
systemctl list-units --type=service | grep container

# Show generated service file
systemctl cat app-name.service

# Check Quadlet conversion
sudo /usr/libexec/podman/quadlet -dryrun
```

### Log Locations

- **Service logs**: `journalctl -u app-name.service`
- **Container logs**: `podman logs container-name`
- **Quadlet logs**: `journalctl -u quadlet.service`
