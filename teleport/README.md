# Teleport Docker Compose Setup

A Docker Compose configuration for running Teleport - a modern security gateway for remotely accessing Linux servers, Kubernetes clusters, databases, and web applications.

## What is Teleport?

Teleport provides secure access to infrastructure with:
- SSH access to servers
- Kubernetes cluster access
- Database access (PostgreSQL, MySQL, MongoDB, etc.)
- Web application access
- Built-in audit logging and session recording
- Certificate-based authentication

## Service Details

### Teleport
- **Image**: public.ecr.aws/gravitational/teleport-distroless:17.7.1
- **Ports**:
  - `3025`: Auth service (API and SSH)
  - `3080`: Web UI
- **Web Access**: http://localhost:3080

## Prerequisites

- Docker and Docker Compose installed
- Teleport configuration file at `./teleport/config/teleport.yaml`
- Port availability: 3025, 3080

## Getting Started

1. **Create the directory structure**:
   ```bash
   mkdir -p teleport/config teleport/data
   ```

2. **Create a basic configuration** (`teleport/config/teleport.yaml`):
   ```yaml
   version: v3
   teleport:
     data_dir: /var/lib/teleport
     nodename: localhost
   
   auth_service:
     enabled: true
     listen_addr: 0.0.0.0:3025
   
   proxy_service:
     enabled: true
     web_listen_addr: 0.0.0.0:3080
     public_addr: localhost:3080
   
   ssh_service:
     enabled: true
   ```

3. **Start Teleport**:
   ```bash
   docker-compose up -d
   ```

4. **Create an admin user**:
   ```bash
   docker exec -it teleport tctl users add admin --roles=editor,access --logins=root
   ```
   This will output a signup link - open it in your browser to set your password.

5. **Access the Web UI**:
   Open http://localhost:3080 and log in with your admin credentials.

## Common Commands

**Create a new user**:
```bash
docker exec -it teleport tctl users add <username> --roles=editor,access
```

**View cluster status**:
```bash
docker exec -it teleport tctl status
```

**Reset admin password**:
```bash
docker exec -it teleport tctl users reset admin
```

**Stop Teleport**:
```bash
docker-compose down
```

## Using Teleport

### Connecting to Servers
1. Add servers to Teleport by installing the Teleport agent on them
2. Access servers via the web UI or using `tsh` CLI client
3. All sessions are recorded and auditable

### CLI Access
Install the `tsh` client on your machine and connect:
```bash
tsh login --proxy=localhost:3080 --user=admin
tsh ls  # List available servers
tsh ssh root@hostname  # SSH into a server
```

## Data Persistence

- **Config**: `./teleport/config` - Configuration files
- **Data**: `./teleport/data` - Certificates, audit logs, and session recordings

## Troubleshooting

- **Cannot access web UI**: Ensure port 3080 is available and Teleport has fully started (check logs)
- **"No nodes available"**: This is normal for a fresh install - add nodes by installing Teleport agents
- **Certificate errors**: Ensure `public_addr` in config matches how you access Teleport
- **View logs**: `docker-compose logs -f teleport`

## Notes

- This is a minimal single-instance setup for development/testing
- For production, use a proper domain with HTTPS and external authentication
- Consider using Teleport Cloud for managed hosting
- Documentation: https://goteleport.com/docs/