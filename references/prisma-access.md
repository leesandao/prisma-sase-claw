# Prisma Access Configuration API

Base path: `https://api.sase.paloaltonetworks.com/sse/config/v1/`

Prisma Access is the cloud-delivered security service. All configuration changes go through a candidate/running config model — changes are staged in a "candidate" config and must be explicitly pushed to become active.

## Table of Contents

1. [Security Policy Rules](#security-policy-rules)
2. [Addresses and Address Groups](#addresses-and-address-groups)
3. [Services and Service Groups](#services-and-service-groups)
4. [Threat Prevention Profiles](#threat-prevention-profiles)
5. [Remote Networks](#remote-networks)
6. [Service Connections](#service-connections)
7. [GlobalProtect / Mobile Users](#globalprotect--mobile-users)
8. [IKE and IPSec Configuration](#ike-and-ipsec-configuration)
9. [Certificate Management](#certificate-management)
10. [Authentication Infrastructure](#authentication-infrastructure)
11. [User and Group Management](#user-and-group-management)
12. [Decryption Rules](#decryption-rules)
13. [Traffic Steering and QoS](#traffic-steering-and-qos)
14. [Configuration Push](#configuration-push)

---

## Security Policy Rules

Security rules control traffic flow through Prisma Access.

### List security rules
```bash
curl -s -X GET "https://api.sase.paloaltonetworks.com/sse/config/v1/security-rules?folder=All" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Create a security rule
```bash
curl -s -X POST "https://api.sase.paloaltonetworks.com/sse/config/v1/security-rules?folder=All" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "allow-web-traffic",
    "from": ["trust"],
    "to": ["untrust"],
    "source": ["any"],
    "destination": ["any"],
    "application": ["web-browsing", "ssl"],
    "service": ["application-default"],
    "action": "allow",
    "log_end": true
  }'
```

### Update a security rule
```bash
curl -s -X PUT "https://api.sase.paloaltonetworks.com/sse/config/v1/security-rules/{rule_id}?folder=All" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{ ... }'
```

### Delete a security rule
```bash
curl -s -X DELETE "https://api.sase.paloaltonetworks.com/sse/config/v1/security-rules/{rule_id}?folder=All" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Other rule types

The same CRUD pattern applies to:
- `traffic-steering-rules` — traffic routing decisions
- `application-override-rules` — override app identification
- `decryption-rules` — SSL/TLS decryption policies
- `authentication-rules` — authentication enforcement
- `qos-policy-rules` — quality of service policies

All use the same base path and `?folder=` query parameter.

---

## Addresses and Address Groups

### List addresses
```bash
curl -s -X GET "https://api.sase.paloaltonetworks.com/sse/config/v1/addresses?folder=All" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Create an address object
```bash
curl -s -X POST "https://api.sase.paloaltonetworks.com/sse/config/v1/addresses?folder=All" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "webserver-subnet",
    "ip_netmask": "10.0.1.0/24",
    "description": "Web server subnet"
  }'
```

Address types: `ip_netmask`, `ip_range`, `ip_wildcard`, `fqdn`.

### Address Groups
```bash
# List
GET /sse/config/v1/address-groups?folder=All

# Create (static group)
POST /sse/config/v1/address-groups?folder=All
{
  "name": "web-servers",
  "static": ["webserver-1", "webserver-2"],
  "description": "All web servers"
}

# Create (dynamic group)
POST /sse/config/v1/address-groups?folder=All
{
  "name": "tagged-servers",
  "dynamic": {"filter": "'web' and 'production'"}
}
```

---

## Services and Service Groups

### List services
```bash
curl -s -X GET "https://api.sase.paloaltonetworks.com/sse/config/v1/services?folder=All" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Create a service
```bash
curl -s -X POST "https://api.sase.paloaltonetworks.com/sse/config/v1/services?folder=All" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "custom-app-port",
    "protocol": {
      "tcp": {"port": "8443"}
    }
  }'
```

### Service Groups
```bash
POST /sse/config/v1/service-groups?folder=All
{
  "name": "web-services",
  "members": ["service-http", "service-https", "custom-app-port"]
}
```

---

## Threat Prevention Profiles

### Anti-Spyware Profiles
```bash
GET  /sse/config/v1/anti-spyware-profiles?folder=All
POST /sse/config/v1/anti-spyware-profiles?folder=All
```

### Vulnerability Protection Profiles
```bash
GET  /sse/config/v1/vulnerability-protection-profiles?folder=All
POST /sse/config/v1/vulnerability-protection-profiles?folder=All
```

### WildFire Antivirus Profiles
```bash
GET  /sse/config/v1/wildfire-anti-virus-profiles?folder=All
POST /sse/config/v1/wildfire-anti-virus-profiles?folder=All
```

### Other security profiles
- `file-blocking-profiles` — file blocking rules
- `dns-security-profiles` — DNS-layer protection
- `url-access-profiles` — URL filtering

---

## Remote Networks

Remote networks connect branch sites to Prisma Access via IPSec tunnels.

### List remote networks
```bash
curl -s -X GET "https://api.sase.paloaltonetworks.com/sse/config/v1/remote-networks?folder=All" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Create a remote network
```bash
curl -s -X POST "https://api.sase.paloaltonetworks.com/sse/config/v1/remote-networks?folder=All" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "branch-office-nyc",
    "region": "us-east-1",
    "spn_name": "us-east-nyc",
    "ipsec_tunnel": "branch-nyc-tunnel",
    "subnets": ["10.10.0.0/16"]
  }'
```

---

## Service Connections

Service connections link data centers or HQ to Prisma Access.

```bash
GET  /sse/config/v1/service-connections?folder=All
POST /sse/config/v1/service-connections?folder=All
```

---

## GlobalProtect / Mobile Users

### Locations and regions
```bash
GET /sse/config/v1/locations?folder=All
GET /sse/config/v1/regions?folder=All
```

---

## IKE and IPSec Configuration

### IKE Gateways
```bash
GET  /sse/config/v1/ike-gateways?folder=All
POST /sse/config/v1/ike-gateways?folder=All
```

### IPSec Tunnels
```bash
GET  /sse/config/v1/ipsec-tunnels?folder=All
POST /sse/config/v1/ipsec-tunnels?folder=All
```

### Crypto Profiles
```bash
GET  /sse/config/v1/ike-crypto-profiles?folder=All
POST /sse/config/v1/ike-crypto-profiles?folder=All
GET  /sse/config/v1/ipsec-crypto-profiles?folder=All
POST /sse/config/v1/ipsec-crypto-profiles?folder=All
```

---

## Certificate Management

```bash
GET  /sse/config/v1/certificates?folder=All
POST /sse/config/v1/certificates?folder=All
GET  /sse/config/v1/certificate-profiles?folder=All
GET  /sse/config/v1/trusted-certificate-authorities?folder=All
```

---

## Authentication Infrastructure

### LDAP Server Profiles
```bash
GET  /sse/config/v1/ldap-server-profiles?folder=All
POST /sse/config/v1/ldap-server-profiles?folder=All
```

### SAML Server Profiles
```bash
GET  /sse/config/v1/saml-server-profiles?folder=All
POST /sse/config/v1/saml-server-profiles?folder=All
```

### Other authentication backends
- `kerberos-server-profiles`
- `radius-server-profiles`
- `tacacs-server-profiles`
- `mfa-servers`
- `authentication-profiles`
- `authentication-sequences`

---

## User and Group Management

```bash
GET  /sse/config/v1/local-users?folder=All
POST /sse/config/v1/local-users?folder=All
GET  /sse/config/v1/local-user-groups?folder=All
GET  /sse/config/v1/dynamic-user-groups?folder=All
```

---

## Decryption Rules

```bash
GET  /sse/config/v1/decryption-rules?folder=All
POST /sse/config/v1/decryption-rules?folder=All
PUT  /sse/config/v1/decryption-rules/{id}?folder=All
```

---

## Traffic Steering and QoS

```bash
GET  /sse/config/v1/traffic-steering-rules?folder=All
POST /sse/config/v1/traffic-steering-rules?folder=All
GET  /sse/config/v1/qos-policy-rules?folder=All
POST /sse/config/v1/qos-policy-rules?folder=All
```

---

## Configuration Push

After making changes, push the candidate config to make it active.

**Important:** A push creates a **two-level job chain** (Father Job → Child Job). The Father Job only commits the candidate config; the Child Job performs the actual push to cloud. You must monitor both to confirm success. See SKILL.md "Config Push Job Monitoring: Father/Child Job Pattern" for the full procedure.

### Push candidate configuration

```bash
curl -s -X POST "https://api.sase.paloaltonetworks.com/sse/config/v1/config-versions/candidate:push" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"folders": ["Remote Networks"]}'
# Response: {"success": true, "job_id": "1234", "message": "CommitAndPush job enqueued..."}
```

### Monitor push result (Father + Child Jobs)

```bash
# Step 1: Wait for Father Job to finish
curl -s "https://api.sase.paloaltonetworks.com/sse/config/v1/jobs/${FATHER_JOB_ID}" \
  -H "Authorization: Bearer ${TOKEN}"
# Wait until status_str == "FIN"

# Step 2: Find Child Job by parent_id
curl -s "https://api.sase.paloaltonetworks.com/sse/config/v1/jobs?limit=10" \
  -H "Authorization: Bearer ${TOKEN}"
# Filter: job["parent_id"] == FATHER_JOB_ID

# Step 3: Poll Child Job every 2 minutes until terminal status
curl -s "https://api.sase.paloaltonetworks.com/sse/config/v1/jobs/${CHILD_JOB_ID}" \
  -H "Authorization: Bearer ${TOKEN}"
# PUSHSUC/FIN+OK = success, PUSHFAIL/FIN+FAIL = failure
# Parse "details" field (JSON string) for per-region results and errors
```

### Check running configuration

```bash
curl -s -X GET "https://api.sase.paloaltonetworks.com/sse/config/v1/config-versions/running" \
  -H "Authorization: Bearer ${TOKEN}"
```

### Other utility endpoints
- `tags` — tag management for objects
- `schedules` — schedule definitions
- `external-dynamic-lists` — external IP/URL lists
