# Home Network Docker Setup

This Docker Compose configuration sets up multiple services, including Pi-hole, Fail2Ban, Portainer, Prometheus, Grafana, Node Exporter, and Blackbox Exporter, all running within a custom network for seamless communication.

## Services

### 1. **Pi-hole (Ad Blocking)**
Pi-hole is a network-wide ad blocker that acts as a DNS sinkhole. This service blocks advertisements and trackers for all devices on your network.

- **Image**: `pihole/pihole:latest`
- **Ports**:
  - `80:80` (Web interface)
  - `443:443` (Secure Web interface)
- **Environment Variables**:
  - `TZ=Europe/London` (Set the timezone)
  - `WEBPASSWORD=password` (Password for the web interface)
  - `PIHOLE_DNS_=8.8.8.8` (DNS server to use)
  - `PIHOLE_INTERFACE=eth0` (Interface for Pi-hole)
  - `VIRTUAL_HOST=pihole.local` (DNS for local access)
- **Volumes**:
  - Maps `./pihole/etc-pihole` and `./pihole/etc-dnsmasq.d` to persistent storage.
- **Networks**: `pihole_network`

### 2. **Fail2Ban**
Fail2Ban is a security tool that scans log files and bans IPs with too many failed login attempts. It helps protect against brute-force attacks.

- **Image**: `crazymax/fail2ban:latest`
- **Environment**:
  - `TZ=Europe/London`
- **Volumes**:
  - Maps `./fail2ban` and `/var/log` for persistent log storage.
- **Networks**: `pihole_network`

### 3. **Portainer**
Portainer provides a web-based UI to manage Docker containers, making it easier to monitor and control the services running in Docker.

- **Image**: `portainer/portainer-ce:latest`
- **Ports**:
  - `9000:9000` (Web UI for Portainer)
- **Volumes**:
  - Maps `/var/run/docker.sock` for Docker socket.
- **Networks**: `pihole_network`

### 4. **Prometheus**
Prometheus is an open-source monitoring and alerting toolkit that collects and stores metrics data.

- **Image**: `prom/prometheus:latest`
- **Ports**:
  - `9090:9090` (Prometheus UI)
- **Volumes**:
  - Maps `prometheus_data` for persistent storage.
  - Maps `./prometheus/prometheus.yml` for Prometheus configuration.
- **Networks**: `pihole_network`

### 5. **Grafana**
Grafana is an open-source platform for monitoring and observability. It integrates with Prometheus to provide dashboards and visualization of metrics.

- **Image**: `grafana/grafana:latest`
- **Environment**:
  - `GF_SECURITY_ADMIN_PASSWORD=password` (Set Grafana admin password)
- **Ports**:
  - `3000:3000` (Grafana dashboard)
- **Volumes**:
  - Maps `grafana_data` for persistent storage.
- **Networks**: `pihole_network`

### 6. **Node Exporter**
Node Exporter collects system metrics such as CPU, memory, disk, and network usage from the host machine.

- **Image**: `prom/node-exporter:latest`
- **Ports**:
  - `9100:9100` (Metrics endpoint)
- **Volumes**:
  - Maps `/proc`, `/sys`, and `/` for host system metrics.
- **Networks**: `pihole_network`

### 7. **Blackbox Exporter**
Blackbox Exporter is used for monitoring endpoints via HTTP, HTTPS, DNS, TCP, ICMP, etc.

- **Image**: `prom/blackbox-exporter:latest`
- **Ports**:
  - `9115:9115` (Blackbox Exporter endpoint)
- **Volumes**:
  - Maps `./blackbox/blackbox.yml` for Blackbox configuration.
- **Networks**: `pihole_network`

## Networks
- **pihole_network**: A custom bridge network for communication between services.

## Volumes
- **prometheus_data**: Persistent storage for Prometheus data.
- **grafana_data**: Persistent storage for Grafana data.
- **pihole_data**: Persistent storage for Pi-hole data.
- **fail2ban_data**: Persistent storage for Fail2Ban data.

## Prerequisites
- Docker and Docker Compose must be installed on your system.
- Ensure that you have sufficient disk space for logs and persistent storage.

## Usage
1. Clone this repository or download the Docker Compose file.
2. Adjust the `WEBPASSWORD` and `GF_SECURITY_ADMIN_PASSWORD` environment variables to set secure passwords for the web interfaces.
3. Run the following command to start all the services:
   ```bash
   docker-compose up -d
   ```
4. Access the services:
   - Pi-hole Web Interface: `http://pihole.local` (or `http://localhost`)
   - Portainer Web UI: `http://localhost:9000`
   - Prometheus UI: `http://localhost:9090`
   - Grafana Dashboard: `http://localhost:3000`

## Stopping and Restarting Services
To stop the services, run:
```bash
docker-compose down
```
To restart the services:
```bash
docker-compose restart
```

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
