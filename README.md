*Disclaimer: Page still being updated*

# Hosting an Authentication Service with Nginx and Authelia using Docker Compose

This guide explains how to set up an authentication service using **Nginx Proxy Manager (NPM)**, **Authelia**, and **Docker Compose**, along with a React web app frontend and a NestJS REST API backend. The setup is designed for small-scale hosting on an AWS EC2 instance.

---

## **Technologies Used**
- **Docker Compose**
- **Nginx Proxy Manager (NPM)** (with MariaDB)
- **Authelia** (with Redis)
- **React Web App** (frontend example)
- **NestJS API** (backend example)
- **SSL Certificates** (generated via NPM)

---

## **Assumptions**
1. You have an AWS EC2 instance running, or an equivalent host machine.
2. A domain name (e.g., `your-domain.com`) is purchased and points to your server's IP address.
3. Docker and Docker Compose are installed.
4. You are familiar with basic Docker Compose, Nginx, and Authelia configurations.

---

## **Step-by-Step Setup**

### **1. Create a Docker Compose File**
The `docker-compose.yml` file will define the services for this setup:
- **Nginx Proxy Manager (NPM):** Manages reverse proxy and SSL certificates.
- **MariaDB:** Acts as the database for NPM.
- **Authelia:** Provides authentication/authorization.
- **Redis:** Stores sessions for Authelia.
- **React App & NestJS API:** Serve as example applications.

You can find the full `docker-compose.yml` file in [this GitHub repository](https://github.com/IngSW24/todos-authelia-poc/blob/main/docker-compose.yml).

#### Key Points:
- The `frontend` and `backend` networks ensure isolated communication between services.
- Authelia and Redis use persistent data storage for configurations and sessions.

---

### **2. Start the Services**
Run the following command to start the services:

```bash
docker-compose up
```

Initially, Authelia will fail because it lacks a configuration file. This is expected and will generate a template file in the specified configuration directory.

---

### **3. Configure Authelia**

Edit the Authelia configuration file located in `./authelia/config/configuration.yml`.
You can find the full `configuration.yml` file in [this GitHub repository](https://github.com/IngSW24/todos-authelia-poc/blob/main/authelia-configuration.yml).

#### Key Configuration Points:

- Replace `your-domain.com` with your actual domain name.
- Update the jwt_secret, session.secret, and storage.encryption_key with secure values.
- Define access control rules to protect specific routes and enforce multi-factor authentication if necessary.

---

### **3. Set Up Proxy Hosts with SSL**

Access the NPM admin panel via: http://<`your-server-ip`>:81

Log in using the default credentials (admin@example.com / changeme) and create an admin account.

Add Proxy Hosts:

- `your-domain.com` : Redirect traffic to `http://my-app:3000`
  - Add [following configuration](https://github.com/IngSW24/todos-authelia-poc/blob/main/your-domain-nginx-snippet.txt) on advance settings tab
- `your-domain.com/api` : Redirect traffic to `http://nestjs:3000`
  - Add [following configuration](https://github.com/IngSW24/todos-authelia-poc/blob/main/auth-your-domain-nginx-snippet.txt) on advance settings tab

Add SSL certificates for your domain using the integrated Let's Encrypt option.

---

### **4. Verify the Setup**

- Visit `auth.your-domain.com` to test the Authelia login page.
- Ensure that routes protected by Authelia prompt for authentication and other routes are accessible as expected.
