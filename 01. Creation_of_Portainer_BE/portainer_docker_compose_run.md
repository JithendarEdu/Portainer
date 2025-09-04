# Deploying Portainer Business Edition with Docker Compose

This guide provides steps to deploy **Portainer Business Edition (BE)** using Docker Compose, binding to IP `127.0.0.1` and defaulting to UTC timezone. Portainer BE requires a valid license key (obtainable from https://www.portainer.io/take-5 for a free 3-node license or a commercial license).

## Prerequisites
- **Docker**: Ensure Docker and Docker Compose are installed on your Linux VM. Install Docker if needed:
  ```bash
  sudo apt-get update && sudo apt-get install -y docker.io docker-compose
  ```
  Avoid snap-based Docker installations on Ubuntu due to potential compatibility issues.
- **License Key**: Obtain a Portainer BE license key from https://www.portainer.io.
- **Network**: Ensure ports `8000`, `9443`, and `9000` are open on `127.0.0.1`.
- **Root Access**: You need `sudo` privileges on the VM.

## Deployment Steps

1. **Create Directory and Compose File**
   - Create a directory for the Portainer configuration:
     ```bash
     mkdir ~/portainer
     cd ~/portainer
     ```
   - Create and edit the `docker-compose.yml` file:
     ```bash
     nano docker-compose.yml
     ```
   - Paste the following configuration:
     ```yaml
     version: '3.9'
     services:
       portainer:
         image: portainer/portainer-ee:lts
         container_name: portainer
         restart: always
         ports:
           - "127.0.0.1:8000:8000" # Agent port
           - "127.0.0.1:9443:9443" # HTTPS UI port
           - "127.0.0.1:9000:9000" # HTTP port (optional, for legacy)
         volumes:
           - /var/run/docker.sock:/var/run/docker.sock
           - portainer_data:/data
     volumes:
       portainer_data:
     ```
   - Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

2. **Deploy Portainer**
   - Run the following to pull the image and start the container in detached mode:
     ```bash
     docker compose up -d
     ```
   - This pulls the `portainer/portainer-ee:lts` image, creates the container, and starts it.

3. **Access Portainer**
   - Open a browser and navigate to `https://127.0.0.1:9443`.
   - Accept the self-signed SSL certificate warning (or configure a valid SSL certificate for production).
   - On first login, create an admin user and enter your Portainer BE license key.

4. **Verify Deployment**
   - Check if the container is running:
     ```bash
     docker ps
     ```
     Look for a container named `portainer`.
   - Verify the timezone (should be UTC):
     ```bash
     docker exec portainer date
     ```
     Example output: `Wed Sep 3 11:01:00 UTC 2025` (adjusts for IST offset of +5:30).
   - Check logs for errors:
     ```bash
     docker logs portainer
     ```
   - Confirm timestamps in Portainer’s UI or logs reflect UTC.

## Additional Notes
- **Timezone**: The container defaults to UTC since no timezone is specified. Timestamps will be in UTC (e.g., 11:01 AM UTC is 4:31 PM IST).
- **Firewall**: Ensure ports are open:
  ```bash
  sudo ufw allow proto tcp from any to 127.0.0.1 port 8000,9443,9000
  ```
- **SELinux**: If SELinux is enabled, add `privileged: true` to the `portainer` service in `docker-compose.yml`:
  ```yaml
    privileged: true
  ```
  Alternatively, adjust SELinux policies or disable SELinux.
- **Security**: For production, use a reverse proxy (e.g., Traefik or Nginx) with a valid SSL certificate. See https://docs.portainer.io for setup guides.
- **Upgrading**: To update Portainer BE:
  ```bash
  docker compose pull
  docker compose up -d
  ```

## Troubleshooting
- **UI Not Accessible**: Verify the IP (`127.0.0.1`), ports, and container status (`docker ps`). Check logs: `docker logs portainer`.
- **License Issues**: Ensure your license key is valid and not used elsewhere.
- **Timezone Issues**: Confirm UTC is expected. If you need the VM’s timezone (e.g., IST), add:
  ```yaml
  volumes:
    - /etc/localtime:/etc/localtime:ro
  ```
  to the `portainer` service in `docker-compose.yml`.

For further details, refer to Portainer’s documentation: https://docs.portainer.io.