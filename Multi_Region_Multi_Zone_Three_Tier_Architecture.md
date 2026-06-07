# Multi-Region Multi-Zone Three-Tier Architecture (Azure)

## Overview

This architecture demonstrates a highly available and disaster-resilient application deployed across two Azure regions:

- Central India (Primary Region)
- South India (Secondary/DR Region)

Each region contains:
- Zone 1
- Zone 2
- Web Tier
- Application Tier
- Database Tier

---

# 1. Users

**Purpose:** End users accessing the application.

**Use Case:**
- Web users
- Mobile users
- API consumers

Traffic starts from users and enters Azure through DNS and security layers.

---

# 2. Azure DNS

**Purpose:** Name resolution.

**Use Case:**
- Converts application URL into an IP address.
- Can redirect users to another region during disaster recovery.

Example:
www.company.com -> Application Gateway public IP

---

# 3. Front Door + WAF

## Azure Front Door

**Purpose:**
Global entry point for the application.

**Use Case:**
- Global load balancing
- Health probes
- Regional failover
- Lowest latency routing

If Central India becomes unavailable, traffic can automatically move to South India.

## WAF (Web Application Firewall)

**Purpose:**
Protect web applications from attacks.

**Use Case:**
- SQL Injection protection
- XSS protection
- OWASP Top 10 protection

---

# 4. DDoS Protection

**Purpose:**
Protects public-facing services from Distributed Denial of Service attacks.

**Use Case:**
- Prevents traffic flooding attacks.
- Maintains application availability.

---

# 5. Application Gateway

**Purpose:**
Regional Layer-7 Load Balancer.

**Use Case:**
- SSL termination
- URL-based routing
- Host-based routing
- Web Application Firewall integration

Examples:
- /api -> App Servers
- /images -> Different backend pool

---

# 6. Virtual Network (VNet)

Central India:
10.0.0.0/16

South India:
10.10.0.0/16

**Purpose:**
Provides network isolation.

**Use Case:**
- Secure communication between tiers.
- Controls traffic using NSGs and route tables.

---

# 7. Availability Zones

Each region contains:

- Zone 1
- Zone 2

**Purpose:**
Protect workloads from datacenter failure.

**Use Case:**
If Zone-1 fails, Zone-2 continues serving traffic.

This provides High Availability (HA).

---

# 8. Web Tier

Subnets:
- 10.0.1.0/24
- 10.0.11.0/24

**Purpose:**
Hosts web servers.

**Typical Workloads:**
- NGINX
- Apache
- IIS
- React UI

**Use Case:**
Receives user requests and forwards them to the application layer.

---

# 9. Internal Azure Load Balancer (Web -> App)

**Purpose:**
Distributes traffic from web servers to application servers.

**Benefits:**
- High availability
- Scalability
- Session distribution

---

# 10. Application Tier

Subnets:
- 10.0.2.0/24
- 10.0.21.0/24

**Purpose:**
Contains business logic.

**Typical Workloads:**
- Node.js
- Java
- .NET
- Python

**Responsibilities:**
- Authentication
- Authorization
- API processing
- Business rules

---

# 11. Internal Azure Load Balancer (App -> DB)

**Purpose:**
Distributes requests toward database services.

**Benefits:**
- Fault tolerance
- High availability

---

# 12. Database Tier

Subnets:
- 10.0.3.0/24
- 10.0.31.0/24

**Purpose:**
Stores application data.

**Example Services:**
- Azure SQL Database
- SQL Managed Instance
- SQL Server on VM

**Use Case:**
Stores customer data, transactions, logs and business information.

---

# 13. VNet Peering

**Purpose:**
Private connectivity between regions.

**Use Case:**
- Replication
- Backup traffic
- Monitoring traffic
- Management traffic

Benefits:
- Low latency
- Microsoft backbone network
- No public internet usage

---

# 14. Management Resource Group

**Purpose:**
Centralized management resources.

**Typical Components:**
- Bastion
- Automation Accounts
- Update Management
- Management VMs

---

# 15. Governance Policies

**Purpose:**
Enforce standards.

**Examples:**
- Mandatory tagging
- Allowed regions
- Allowed VM sizes
- Security baselines

Implemented using Azure Policy.

---

# 16. Monitoring Dashboard

**Purpose:**
Observe health and performance.

**Tools:**
- Azure Monitor
- Log Analytics
- Application Insights

Metrics:
- CPU
- Memory
- Response time
- Availability

---

# 17. Key Vault

**Purpose:**
Secure storage of secrets.

**Stores:**
- Passwords
- Certificates
- Connection strings
- Encryption keys

Benefits:
- Centralized secret management
- Improved security

---

# End-to-End Request Flow

1. User accesses application URL.
2. Azure DNS resolves the request.
3. Front Door receives traffic.
4. WAF inspects the request.
5. Traffic reaches Application Gateway.
6. Application Gateway routes traffic to Web Tier.
7. Web Tier forwards requests to App Tier through Internal Load Balancer.
8. App Tier processes business logic.
9. App Tier communicates with Database Tier.
10. Response returns to the user.

---

# Failure Scenarios

## Zone Failure

Zone-1 fails:
- Zone-2 continues serving traffic.
- No application downtime.

## Region Failure

Central India fails:
- Front Door redirects traffic to South India.
- Business services continue operating.

---

# Interview Questions

1. Why use Front Door instead of Application Gateway?
2. What is the difference between Availability Zones and Regions?
3. Why separate Web, App and DB tiers?
4. Why use Internal Load Balancers?
5. What is VNet Peering?
6. How is disaster recovery achieved?
7. Why store secrets in Key Vault?
8. What is the role of Azure Policy?
