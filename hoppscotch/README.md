# Hoppscotch

## Running Hoppscotch

1. **Copy the required files**

Copy the following files from the repository:

* `docker-compose.yml`
* `.env.example`

Also copy the **proxy docker compose file** from the `/proxy` directory.

Create the following structure:

```
.
├── docker-compose.yml
├── .env
└── proxy
    └── docker-compose.yml
```

Rename `.env.example` to `.env`.

---

2. **Start the proxy services**

Navigate into the proxy directory:

```bash
cd proxy
```

Start the proxy stack:

```bash
docker compose up -d
```

---

3. **Configure Hoppscotch**

Go back to the root directory:

```bash
cd ..
```

Open the `.env` file and adjust the values to match your environment (domain, ports, secrets, etc.).

---

4. **Start Hoppscotch**

Run the main compose stack:

```bash
docker compose up -d
```

---

✅ **Hoppscotch is now running** and ready to be accessed through your configured reverse proxy.


---

# Run behind Nginx Proxy Manager

This guide shows how to expose **Hoppscotch** services behind **Nginx Proxy Manager** using path routing.

The setup routes:

| Path       | Service                | Port   |
| ---------- | ---------------------- | ------ |
| `/`        | Hoppscotch frontend    | `3000` |
| `/admin`   | Hoppscotch admin       | `3100` |
| `/backend` | Hoppscotch backend API | `3170` |

Example domain used:

```
hopp.assie.local
```

---

## 1. Create Proxy Host

In **Nginx Proxy Manager → Proxy Hosts → Add Proxy Host**

### Domain Names

```
hopp.assie.local
```

### Forward Settings

| Setting             | Value          |
| ------------------- | -------------- |
| Scheme              | `http`         |
| Forward Hostname/IP | `127.0.0.1` |
| Forward Port        | `3000`         |

### Access List

```
Publicly Accessible
```

### Options

Enable:

```
Block Common Exploits
Websockets Support
```

Disable:

```
Cache Assets
```

---

# 2. Add Custom Locations

Open the **Custom Locations** tab.

---

## Admin Route

Location:

```
/admin
```

Forward Settings:

| Setting             | Value          |
| ------------------- | -------------- |
| Scheme              | `http`         |
| Forward Hostname/IP | `127.0.0.1` |
| Forward Port        | `3100`         |

---

## Backend Route

Location:

```
/backend
```

Forward Settings:

| Setting             | Value          |
| ------------------- | -------------- |
| Scheme              | `http`         |
| Forward Hostname/IP | `127.0.0.1` |
| Forward Port        | `3170`         |

---

# 3. Custom Nginx Configuration

Open **Advanced → Custom Nginx Configuration** and add:

```nginx
location ^~ /backend/ {
  proxy_http_version 1.1;

  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;

  # Trailing slash is the magic: strips /backend/
  proxy_pass http://127.0.0.1:3170/;
}

location = /backend {
  return 301 /backend/;
}
```

This configuration:

• Enables **WebSocket support**
• Preserves headers for the backend
• Fixes **trailing slash routing issues**
• Strips `/backend/` before forwarding to the backend service

---

# 4. SSL Configuration

Go to the **SSL tab**.

Enable:

```
Request a new SSL certificate
Force SSL
HTTP/2 Support
```

Use **Let's Encrypt**.

---

# 5. Final Routing

After setup, the services will be accessible as:

```
https://hopp.assie.local/          → Hoppscotch frontend
https://hopp.assie.local/admin    → Hoppscotch admin
https://hopp.assie.local/backend  → Hoppscotch backend API
```

---

# 6. Expected Architecture

```
Internet
    │
    ▼
Nginx Proxy Manager
    │
    ├── /        → 127.0.0.1:3000
    ├── /admin   → 127.0.0.1:3100
    └── /backend → 127.0.0.1:3170
```
