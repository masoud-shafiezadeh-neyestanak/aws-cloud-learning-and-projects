# Deployment and Testing — EC2 Linux Web Server

## 1. Document Purpose

This document records the deployment procedure and validation steps performed during Project 03 — EC2 Linux Web Server on AWS.

It provides a practical implementation record covering SSH access, operating system verification, Nginx installation, service configuration, and HTTP testing.

## 2. Environment

| Component | Configuration |
|---|---|
| Cloud Provider | Amazon Web Services |
| Region | us-east-1 |
| Compute | Amazon EC2 |
| Instance Type | t3.micro |
| Operating System | Amazon Linux 2023 |
| Architecture | x86_64 |
| Web Server | Nginx 1.30.6 |
| Web Server Port | TCP 80 |
| Document Root | `/usr/share/nginx/html/` |
| Main Web Page | `index.html` |

## 3. SSH Connection

The instance was accessed remotely from Windows PowerShell using SSH and the EC2 private key.

Example command:

```bash
ssh -i "<path-to-private-key.pem>" ec2-user@<EC2-public-IPv4>
```

The private key was stored locally and was not uploaded to the GitHub repository.

## 4. Operating System Verification

The following commands were used to verify the remote session and operating system:

```bash
whoami
hostname
cat /etc/os-release
```

**Observed results:**

- Linux user: `ec2-user`
- Hostname: `ip-172-31-18-114.ec2.internal`
- Operating System: Amazon Linux 2023
- Architecture: x86_64

## 5. Nginx Installation

Nginx was installed using the DNF package manager:

```bash
sudo dnf install -y nginx
```

The installed version was verified:

```bash
nginx -v
```

**Observed version:**

```text
nginx version: nginx/1.30.6
```

## 6. Nginx Service Configuration

The Nginx service was enabled and started:

```bash
sudo systemctl enable --now nginx
```

The service configuration was verified using:

```bash
sudo systemctl is-enabled nginx
sudo systemctl is-active nginx
```

**Observed state:**

- Service enabled: `enabled`
- Service status: `active`

Enabling the service ensures that Nginx is configured to start automatically when the instance boots.

## 7. Website Deployment

A custom static HTML page was created at:

```text
/usr/share/nginx/html/index.html
```

This file replaced the default Nginx landing page.

The deployed page displayed the project title and a successful deployment message.

## 8. HTTP Validation

### Local HTTP Test

The following command was executed on the EC2 instance:

```bash
curl -I http://localhost
```

**Observed response:**

```text
HTTP/1.1 200 OK
Server: nginx/1.30.6
Content-Type: text/html
```

The HTTP `200 OK` status confirmed that Nginx successfully responded to the local HTTP request.

### Browser Test

The website was also accessed through the EC2 instance's automatically assigned public IPv4 address.

**Result:** The custom HTML page was successfully displayed in the browser.

## 9. Validation Summary

| Validation Item | Result |
|---|---|
| SSH connectivity | Successful |
| Operating system verification | Successful |
| Nginx installation | Successful |
| Nginx service startup | Successful |
| Automatic service startup configuration | Enabled |
| Custom HTML deployment | Successful |
| Local HTTP response | HTTP 200 OK |
| External browser access | Successful |

## 10. Security and Operational Notes

- SSH access was restricted to the administrator's public IP using a `/32` source range.
- HTTP access was allowed through TCP port 80.
- The EBS root volume was encrypted.
- IMDSv2 was required for instance metadata access.
- The private SSH key was kept outside the repository.
- The instance used an automatically assigned public IPv4 address.
- The deployment used HTTP and did not include HTTPS configuration.

## 11. Project Completion and Cleanup

The deployment and testing activities were completed successfully.

The EC2 instance and associated billable resources must be reviewed and cleaned up after documentation and evidence collection.

The instance should be terminated when the hands-on activity is finished, followed by verification that no unnecessary billable resources remain.

---

**Project:** Project 03 — EC2 Linux Web Server on AWS  
**Implementation Date:** 14 November 2026  
**Documentation:** Deployment and Testing Record
