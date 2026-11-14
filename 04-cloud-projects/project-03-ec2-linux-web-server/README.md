# Project 03 — EC2 Linux Web Server on AWS

**Project Type:** Hands-on AWS Implementation  
**Status:** Deployment, testing, and documentation completed; AWS cleanup pending  
**Start Date:** 14 November 2026  
**AWS Region:** US East (N. Virginia) — `us-east-1`

## 1. Project Overview

This project demonstrates how to deploy and operate a Linux-based web server using Amazon EC2.

An Amazon Linux 2023 instance was provisioned, secured with an EC2 Security Group, and configured to run Nginx.

A custom static HTML page was deployed and successfully accessed through the instance's public IPv4 address.

The project provides hands-on experience with EC2 provisioning, networking, security, EBS storage, SSH connectivity, and basic web server deployment.

## 2. Project Objectives

- Provision an Amazon EC2 Linux instance.
- Configure networking and Security Group rules.
- Deploy an encrypted EBS root volume.
- Establish secure SSH connectivity.
- Install and configure Nginx.
- Deploy a custom HTML web page.
- Test HTTP connectivity and server responses.
- Document the architecture and implementation.
- Review AWS costs and clean up resources.

## 3. AWS Services and Technologies

| Service / Technology | Purpose |
|---|---|
| Amazon EC2 | Linux virtual server |
| Amazon VPC | Virtual networking |
| VPC Subnet | Network placement |
| Internet Gateway | Internet connectivity |
| Route Table | Network traffic routing |
| Security Group | Instance-level firewall |
| Amazon EBS | Persistent root storage |
| AWS KMS | EBS volume encryption |
| Amazon Linux 2023 | Operating system |
| SSH | Remote server access |
| Nginx | Web server |
| HTML | Static website content |
| curl | HTTP response testing |

## 4. Architecture

The project uses a single Amazon EC2 instance deployed in a public subnet within the default Amazon VPC.

Nginx runs on Amazon Linux 2023 and serves a custom static HTML page to visitors over HTTP.

### Architecture Diagram

```mermaid
flowchart TD
    U[Website Visitor / Browser]
    NET[Internet]
    IGW[Internet Gateway]

    subgraph VPC["Default Amazon
