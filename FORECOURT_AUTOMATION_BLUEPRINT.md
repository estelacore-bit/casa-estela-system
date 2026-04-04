# Forecourt Automation Blueprint (FCC + Central BOS)

## 1) Vision
Build a two-tier forecourt automation platform:
- **FCC (Forecourt Controller Computer):** Real-time on-site device control and protocol adapter layer.
- **BOS (Back Office System):** Centralized web application for operations, supervision, reporting, and compliance across one or more sites.

> Note: BOS is centrally hosted (not local), while FCC runs on-site for deterministic device control and resilient operation.

## 2) Core Architecture

### FCC (On-Site Edge Controller)
Responsibilities:
- Device communication with Dispensers, ATG, EPS, and metering/skid systems.
- Local command execution and state collection.
- Safety-first control (authorize, stop, lockout, emergency handling).
- Store-and-forward when WAN is down.
- Publish normalized events to BOS via secure APIs/message broker.

Recommended FCC modules:
1. **Protocol Drivers** (vendor-specific per device).
2. **Device Manager** (health, heartbeat, reconnect strategy).
3. **Command Engine** (authorize, preset, stop, suspend, resume).
4. **Event Buffer** (offline queue + retry).
5. **Site Rules Engine** (local interlocks and safety constraints).
6. **Secure Gateway** (mutual TLS, signed payloads).

### BOS (Central Application)
Responsibilities:
- Day-to-day operations: DSM assignment, MOP changes, shift end/day end, close of day.
- Inventory and reconciliation visibility.
- Alerts, logs, reports, and role-based management.
- Multi-site governance from HQ.

Recommended BOS backend services:
1. **Identity & Access (RBAC)**
2. **Operations Service** (shift/day workflows)
3. **Inventory Service** (tank stock, water levels, alarms)
4. **Pricing Service** (product rates + audit trail)
5. **Sales & Reconciliation Service**
6. **Device Telemetry Service**
7. **Alerts & Notification Service**
8. **Reporting Service**
9. **Audit/Compliance Service**

## 3) Roles and Access
Suggested login roles:
- **Admin:** Full control, master settings, users, policies.
- **Supervisor:** Site operations, approvals, corrections, monitoring.
- **DSM:** Daily sales management, dispenser assignment, shift closure tasks.
- **Accountant:** Financial reports, reconciliation, tax and settlement outputs.

Key controls:
- Role-based menu and field-level permissions.
- Maker-checker approval for sensitive actions (rate change, voids, corrections).
- Full audit trail with user, timestamp, old/new value.

## 4) Dashboard Design (Home Page)
Primary widgets:
1. **Tank Inventory**
   - Product-wise current volume, ullage, water dip, safe threshold.
2. **Dispenser Status Matrix**
   - Assigned, Not Assigned, Offline, Authorized, Idle.
3. **Product-wise Sales Table + Graph**
   - Hourly/daily trend, nozzle-wise and product-wise split.
4. **Current Rates Widget**
   - Product-wise rate, last updated datetime, updated by user.
5. **Communication Status Widget**
   - FCC↔BOS link, TCP device connectivity, local vs HO server status.
6. **Site Information Panel**
   - Site Name, Site Code, SAP Code, TCFA Code,
   - BOS version, version release date, last update date,
   - current date/time, switch user, logoff.

Vertical tabs (left navigation):
- Dashboard
- Reports
- MOP
- IoT Devices
- Alert
- Log
- Setting

## 5) Key Workflows
1. **DSM Assignment**
   - Assign DSM to dispenser/nozzle/shift with validity window.
2. **Shift End**
   - Meter readings, sales summary, cash/card/UPI breakup, variance check.
3. **Day End / Close of Day**
   - Consolidation, bank drop declaration, approvals, lock period.
4. **MOP Change**
   - Controlled updates with audit and optional approval.
5. **Rate Change**
   - Scheduled or immediate, dual authorization, propagation status.
6. **Tank Inventory Reconciliation**
   - ATG reading vs book stock with threshold-based alerts.

## 6) Data and Integration Model
- **Event-first model:** FCC emits telemetry, transactions, alarms, and command responses.
- **Idempotent APIs:** Required to handle retries from store-and-forward.
- **Time synchronization:** NTP sync and UTC storage.
- **Master data:** Sites, products, nozzles, tanks, devices, users, roles.

Recommended interfaces:
- BOS APIs over HTTPS (REST/gRPC)
- Message queue for telemetry (MQTT/Kafka-like broker)
- WebSocket for live dashboard updates
- Reporting exports (PDF/CSV/XLSX)

## 7) Reliability & Security Baseline
- Mutual TLS between FCC and BOS.
- Device command signing and replay protection.
- Local FCC cache for WAN outage continuity.
- Automated retries with exponential backoff.
- Encrypted secrets at rest and in transit.
- Central audit logs + tamper-evidence.

## 8) Suggested Enhancements (What Else to Incorporate)
1. **Predictive maintenance** from dispenser/ATG anomalies.
2. **Auto anomaly detection** (sudden shrinkage, unusual idle/sales patterns).
3. **Smart alerting** with severity, escalation, SLA timers.
4. **Mobile supervisor app** for approvals and live alerts.
5. **Geo-fenced remote operations** for secure remote commands.
6. **BI-ready data warehouse** for long-term trend analytics.
7. **Rule-based fraud checks** (suspicious manual interventions).
8. **Offline shift packet signing** to avoid tampering in low-connectivity periods.
9. **Config versioning** with rollback (rates, mappings, permissions).
10. **Disaster recovery drills** and site-level failover readiness dashboard.

## 9) Suggested Phase Plan
- **Phase 1:** Core FCC connectivity + BOS dashboard + role-based login.
- **Phase 2:** Shift/day workflows, reconciliation, reports, alerting.
- **Phase 3:** Advanced analytics, predictive maintenance, HQ multi-site optimization.

## 10) Suggested MVP Scope (First Deliverable)
- FCC:
  - Dispenser + ATG integration
  - Device health/status publish
  - Basic authorize/stop command path
- BOS:
  - Login (Admin/Supervisor/DSM/Accountant)
  - Dashboard widgets listed above
  - Shift end + day end
  - Rate management + audit
  - Basic reports (sales, inventory, communication)

This blueprint can be directly converted into product requirements, API contracts, and sprint backlog.
