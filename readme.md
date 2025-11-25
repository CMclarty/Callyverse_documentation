# Home Infrastructure Lab

Self-hosted Linux server running containerized applications with 40TB+ of storage in areas, NGINX reverse proxy with full remote access over HTTPS

## Overview

Personal production server running since 2019. Used for self hosting key apps, remote storage and backup as well as a learning excercise - Linux administration, Docker, networking, and infrastructure automation.

## Architecture

- **Host OS**: Ubuntu Server 22.04
- **Containerization**: Docker + Docker Compose
- **Reverse Proxy**: NGINX 
- **SSL/TLS**: Let's Encrypt certificates via Certbot
- **CDN/DDoS Protection**: Cloudflare, with dynamically updated IP address service
- **Domain**: Custom domain with DNS management
- **Access**: SSH key-based authentication, passwordless

## Examples of Services running

- Nextcloud CMS and backup solution
- Jellyfin media server
    - Nvidia drivers to allow on-the-fly transcoding of media for streaming.
- Bitwarden password manager
- Mealio recipe management system

## Example skills 

**Linux Administration**
- User and permission managemen (file permissions and similar access issues)
- System monitoring and logs (`journalctl`, `htop`, 'du', 'grep', log analysis)
- Package management and updates (APT etc)
- Storage configuration and management (mdadm arrays, fstab file, docker)

**Docker & Containerization**
- Writing and managing Docker Compose files
- Container networking and port/storage mapping
- Volume management and persistent storage
- Troubleshooting container issues, basic commandline usage of Docker Compose('Start, stop, up/down, build, remove etc)

**Networking & Security**
- Reverse proxy configuration
- SSL certificate generation and renewal (Certbot, Cloudlfare)
- Firewall rules (UFW/iptables)
- Domain DNS configuration, dynamic DNS updates
- SSH hardening (Keys, removing password, automatic blocking of malicious requests)

**Automation**
- Bash scripts for backups
- Automated system updates
- Service monitoring scripts

### Partial Docker Compose file for Nextcloud server
---
'''version: "2.1"
services:
  nextcloud:
    image: linuxserver/nextcloud
    container_name: nextcloud
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    volumes:
      - .:/config
      - /mnt/theta/nextcloud:/data
      - /mnt/sigma:/mnt/sigma
      - /mnt/theta:/mnt/theta
      - /mnt/:/mnt/
#    ports:
#      - 5555:443
    restart: unless-stopped
 #   environment:
#      - DOCKER_MODS=linuxserver/mods:universal-cron
    networks:
      - callyverse
'''
### Example config file - Fstab snippet for MergerFS array

## Sigma
#### HDDs
UUID="42d9b894-4edc-4644-94e8-dbd33b62cd11" /mnt/mu ext4 defaults 0 0
UUID="e7cf54fd-1d53-444b-9070-90689fc9ce9c" /mnt/rho ext4 defaults 0 0

#### MergerFS
/mnt/mu:/mnt/rho /mnt/sigma fuse.mergerfs use_ino,cache.files=partial,fsname=sigma,dropcacheonclose=true,allow_other,category.create=mfs 0 0

## Challenges

**Challenge**: Unable to get a static IP from my residential address, kept losing access.
**Solution**: Researched and setup DynDNS service to automatically update Cloudlfare records on a regular basis

**Challenge**: Full raid backup of every file was cost-prohibitive
**Solution**: Seperated physical hard drives into two seperate arrays depending on the redundancy requirements

## Future Improvements

- Implement monitoring solution (Prometheus/Grafana stack)
- Add automated testing for configuration changes
- Start exploring Terraform and similar tools
- Investigate and learn CI/CD techniques

