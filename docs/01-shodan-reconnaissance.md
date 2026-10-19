# Cloud Security OSINT Assessment — Shodan Reconnaissance

> Educational, passive reconnaissance exercise based on *Lab 2: Finding Cloud Vulnerabilities using OSINT Tools*. No direct interaction with third-party services was performed.

## Project scope

**Objective:** Understand what public search-engine indexing can reveal about cloud services and infrastructure, and distinguish potential exposure from confirmed vulnerability.

**Tool:** Shodan (web interface)  
**Observation date:** 2026-10-16  
**Method:** Run the nine lab queries, examine indexed result listings and banners, record aggregate counts and technical observations. No direct target connections, login attempts, or downloads.

**Ethics:** Screenshots and raw third-party identifiers from the academic lab are not published in this repository. Public findings below are aggregated and anonymized. The original course instructions require screenshots to remain confidential.

## Results

| Search | Topic | Shodan matches | Observations | Verification status |
|---|---|---:|---|---|
| Initial | Azure directory listings | 264 | `Index of /` titles; inspected results also showed TLS certificate information despite `ssl:false` | No missing TLS or exposed sensitive file confirmed |
| (a) | Azure Swagger interfaces | 1,687 | Swagger-related titles; IIS, Kestrel and Uvicorn appeared in visible headers | Public API docs are not necessarily a vulnerability |
| (b) | Azure `cloudapp.net` | 634,288 | HTTP 200 responses; ports 8081, 8443, 8083 and 8085 prominent | Internet-facing service inventory only; no country filter |
| (c) | Weave Scope | 1 | Title and favicon search matched one Azure-associated service with HTTP 200 | No authentication or function testing |
| (d) | Docker Registry | 239 | `Docker-Distribution-Api-Version: registry/2.0` in visible banners; ports 443 and 5000 | No evidence of unauthenticated image read/write |
| (e) | Jenkins-related search | 1,592 | Visible results included Netdata, Linux Dash and Hoverfly, some with honeypot tags | Possible false positives; Jenkins not confirmed in inspected results |
| (f) | MikroTik RouterOS 6.45.9 | 6,447 | WinBox banners showed 6.45.9 version strings | Version match does not prove a CVE is exploitable |
| (g) | FTP successful login banners | 28,369 | Responses included `220`, `230 Login successful.` and some `vsFTPd 3.0.2` identifiers | Login context and file permissions unknown |
| (h) | Windows 7 RDP | 3,174 | RDP on 3389; one visible result showed Windows 7/Server 2008 R2 NTLM identification and a Windows 7 Professional login screenshot | No unauthorized RDP access or vulnerability confirmed |

Counts are Shodan search matches at the time of observation, **not verified unique hosts or confirmed vulnerabilities**. Different queries may overlap.

## Reflection questions

### 1. What information can Shodan reveal?

Shodan can expose indexed **public IP addresses, geolocation, hosting organizations, open ports, applications, software versions, HTTP response headers, TLS certificate metadata, operating-system identifiers, and service screenshots**. Examples from this exercise include Azure-associated services, MikroTik RouterOS 6.45.9 banners, Docker Registry API version headers, FTP responses, and Windows RDP metadata. These findings support attack-surface inventory but do not prove a flaw.

### 2. Why are Weave Scope, Docker Registry and Jenkins infrastructure/supply-chain risks?

Weave Scope can provide visibility into multiple containers and services. An improperly protected Docker Registry could reveal proprietary images or, if unauthorized write access existed, undermine deployment artifact integrity. A privileged Jenkins instance may interact with source code, secrets and production deployment workflows. Therefore, misconfiguration of these components can have a broader impact than exposure of one web page. **Our results do not confirm such access**, and the inspected Jenkins-search results included apparent false positives.

### 3. Compare a non-TLS Azure service and a Jenkins dashboard.

A service genuinely lacking TLS can expose traffic confidentiality and integrity; directory indexing might additionally reveal unintended files. A Jenkins interface permitting unauthorized privileged operations could expose deployment credentials, alter CI/CD processes and affect multiple services, so it could pose greater immediate risk. **This is a conditional comparison:** TLS details were visible in the examined Azure results, while the inspected Jenkins-search results did not establish Jenkins exposure. The course question labels the non-TLS search as dork (a), although `ssl:false` appears in the initial directory-listing query.

### 4. Why search for particular OS or firmware versions?

Version fingerprints let defenders correlate externally visible assets with **vendor security advisories, CVEs and threat intelligence**, and prioritize verification or patching. Shodan displayed MikroTik RouterOS 6.45.9 version strings and RDP identification consistent with Windows 7 / Windows Server 2008 R2. Exploitability still depends on accurate identification, specific build/patch state, configuration, prerequisites and mitigations.

### 5. How should Shodan evidence appear in a penetration-testing report?

Record the query, time, authorized asset identifier, indexed observations, potential impact, limits of the evidence, and defensive recommendations. **Before calling an issue a confirmed vulnerability**, secure authorization and scope, verify asset/service attribution, confirm relevant conditions through approved and minimally intrusive testing, assess mitigating controls, preserve reproducible evidence and assign a justified severity. This educational exercise reports **observations only**, not confirmed vulnerabilities.

## Defensive recommendations

- Maintain an inventory of publicly reachable cloud services and management interfaces.
- Restrict internal dashboards, registries, RDP and router management to trusted networks or authorized access gateways.
- Require strong authentication and least privilege for CI/CD systems and software registries.
- Enforce TLS where appropriate and disable unnecessary directory indexing.
- Review vendor advisories, unsupported software and patch status.
- Treat Shodan fingerprints as leads that require authorized validation, especially when honeypot tags or inconsistent filters appear.

## Evidence policy

No unredacted Shodan screenshots, third-party IP addresses, user names, hostnames, credentials, or discovered files are included. Raw screenshots are reserved for the confidential academic submission, in line with the lab's instructions. Portfolio screenshots should be recreated with synthetic or fully sanitized information if publication is permitted.

## Next stage

Task 2 of the course examines **Shodan-indexed database service listings**. It expressly prohibits connecting to or interacting with third-party MongoDB and Elasticsearch instances.
