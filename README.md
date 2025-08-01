# Inception Project

## Overview

This project is a Dockerized web stack built to run WordPress, MariaDB, and Nginx using custom configuration and setup scripts. The main objective is to provide a reproducible, secure, and modular environment for a WordPress website, with all components isolated in their own containers and orchestrated via Docker Compose and Makefile automation.

---

## Table of Contents

1. [Project Structure](#project-structure)
2. [Quick Start](#quick-start)
3. [Configuration Files & Dockerfiles Explained](#configuration-files--dockerfiles-explained)
4. [How the Stack Works (with Diagrams)](#how-the-stack-works-with-diagrams)
5. [Troubleshooting](#troubleshooting)

---

## Project Structure

```
inception/
├── Makefile
└── srcs/
    └── requirements/
        ├── nginx/
        │   ├── Dockerfile
        │   ├── conf/
        │   │   └── nginx.conf
        │   └── tools/
        │       └── fun.html
        ├── wordpress/
        │   ├── Dockerfile
        │   ├── conf/
        │   │   └── www.conf
        │   └── tools/
        │       └── setup.sh
        └── mariadb/
            ├── Dockerfile
            └── tools/
                └── setup.sh
```

---

## Quick Start

1. **Clone the repo:**
   ```
   git clone https://github.com/B-ki/inception.git
   cd inception
   ```

2. **Build and start the containers:**
   ```
   make
   ```
   This will create the required Docker volumes, build all images, and start the containers in detached mode.

3. **Stop and clean up:**
   ```
   make fclean
   ```
   This removes containers and persistent volumes.

---

## Configuration Files & Dockerfiles Explained

### 1. Makefile

Automates Docker Compose tasks:
- `make` → builds and starts everything.
- `make fclean` → stops and deletes everything.
- `make volumes` → creates persistent storage for WordPress and MariaDB.

### 2. Nginx

- **Dockerfile**: Installs Nginx and sets up HTTPS with a self-signed certificate.
- **nginx.conf**: Custom config to serve static files and proxy PHP requests to the WordPress container.
- **fun.html**: Example static HTML served by Nginx for demonstration.

### 3. WordPress

- **Dockerfile**: Installs PHP, PHP-FPM, and WordPress CLI. Copies custom PHP-FPM pool config and setup script.
- **www.conf**: Configures PHP-FPM (process manager, user, listening port, etc.).
- **setup.sh**: Waits for MariaDB, sets up WordPress (installation, config), and starts PHP-FPM.

### 4. MariaDB

- **Dockerfile**: Installs MariaDB server and copies the setup script.
- **setup.sh**: Initializes the database and user for WordPress, applies security tweaks.

---

## How the Stack Works (with Diagrams)

### Full Request Lifecycle

```text
Browser
  |
  v
Nginx (HTTPS, static files, forwards PHP requests)
  |
  v
FastCGI (over port 9000)
  |
  v
WordPress (PHP-FPM runs WordPress code)
  |
  v
MariaDB (stores/retrieves site data)
```

### Example: Handling a WordPress Page Request

1. **Browser** requests a page.
2. **Nginx** serves static files directly, but for PHP files, it forwards the request to the WordPress container using FastCGI (port 9000).
3. **WordPress PHP-FPM** processes the PHP code, interacts with **MariaDB** as needed, and returns HTML.
4. **Nginx** passes the HTML back to the browser.

---

## Detailed Explanations 

### Nginx Configuration

- Acts as a reverse proxy and static file server.
- Forwards `.php` requests to PHP-FPM (WordPress container).
- Handles HTTPS using a self-signed certificate.

### PHP-FPM and WordPress

- PHP-FPM manages a pool of PHP processes (controlled by `www.conf`).
- Each request is handled by a worker: it reads and executes PHP code, compiles it to bytecode (using Opcache for speed), and generates HTML.
- Uses `setup.sh` to bootstrap WordPress using environment variables for DB credentials.

### MariaDB

- Stores all WordPress data: posts, users, settings.
- Isolated in its own container with persistent storage.

---

## Troubleshooting

- If a service fails to start, check logs with:
  ```
  docker compose logs <service>
  ```
- To rebuild from scratch:
  ```
  make fclean
  make
  ```

---

## Notes

- You can customize PHP, Nginx, and MariaDB by editing the files in `srcs/requirements/`.
- For production, replace the self-signed certificate with a real one and secure all environment variables.

---
