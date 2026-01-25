# ntopng Docker Compose Template

This project provides a simple Docker Compose setup for running **ntopng**, a real-time network traffic monitoring and analysis tool.

It is intended as a lightweight, self-hosted template that can be reused across environments.

The container runs using **host networking** so ntopng can properly capture traffic from local interfaces (for example `eth0`), just like the original `docker run` command.

---

## 📦 What’s Included

* ntopng latest Docker image
* License file mounted read-only
* Host network mode for full traffic visibility
* Auto-restart unless stopped

---

## 📁 Requirements

* Docker
* Docker Compose (v2 recommended)
* An ntopng license file named:

```
ntopng.license
```

placed in the same directory as `docker-compose.yml`

---

## 🚀 How to Run

From the project directory:

```bash
docker compose up -d
```

---

## 🌐 Access ntopng

Open in your browser:

```
http://localhost:3000
```

---

## 🛑 Stop the Service

```bash
docker compose down
```

---

## ⚠️ Notes

* This setup uses `network_mode: host` which disables Docker port mapping (this is expected)
* ntopng listens directly on port `3000` on the host
* Interface monitored by default is `eth0` (can be changed in `docker-compose.yml`)