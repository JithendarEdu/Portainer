## Nginx Proxy Manager - Setup & Usage Guide

This document explains how to deploy and access Nginx Proxy Manager using the provided `docker-compose.yml` file.

### 1. Prerequisites
- Docker and Docker Compose installed on your system.

### 2. How to Run
1. Open a terminal in this directory (`02. Proxymanager`).
2. Start the service:
	 ```pwsh
	 docker-compose up -d
	 ```
3. The container will start in the background.

### 3. Accessing the Web UI
- Open your browser and go to: [http://localhost:81](http://localhost:81)

### 4. Default Credentials
On first launch, use the default login:
- **Email:** `admin@example.com`
- **Password:** `changeme`

You will be prompted to change these after first login.

### 5. Changing Username & Password
After logging in:
1. Go to the top-right user menu > 'Profile'.
2. Update your email and password as desired.

### 6. Data Persistence
- All configuration and SSL certificates are stored in Docker volumes:
	- `nginx-proxy-manager-data` (app data)
	- `letsencrypt-certificates` (SSL certs)

### 7. Stopping the Service
To stop Nginx Proxy Manager:
```pwsh
docker-compose down
```

### 8. Additional Notes
- The service is bound to `127.0.0.1` (localhost) for ports 80, 81, and 443.
- To access from another device, adjust the `ports` section in `docker-compose.yml`.

---
For more details, see the official documentation: https://nginxproxymanager.com/
