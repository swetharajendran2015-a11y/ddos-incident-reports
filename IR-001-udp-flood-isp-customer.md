# IR-001 — UDP Flood Attack on ISP Customer

---

## Incident Summary

- **Incident ID:** IR-001
- **Date:** 2024-03-12
- **Severity:** High
- **Attack Type:** UDP Flood (Volumetric — L3)
- **Status:** Resolved
- **Duration:** 47 minutes

---

## Timeline

- 02:14 — Kentik alert fired — abnormal UDP traffic spike detected on customer edge
- 02:16 — Grafana confirmed — inbound traffic jumped from 800 Mbps baseline to 4.2 Gbps
- 02:17 — NOC engineer acknowledged alert and opened Jira ticket INC-2041
- 02:19 — Initial triage completed — traffic identified as UDP flood, destination port random
- 02:22 — Null route applied to targeted destination IP via upstream provider
- 02:25 — Traffic volume on customer edge dropped to normal baseline
- 02:38 — Attack traffic redirected confirmed in Kentik — source IPs still sending but null routed
- 02:58 — Attack subsided — source traffic dropped significantly
- 03:01 — Null route removed — customer IP restored to normal routing
- 03:04 — Service verified via Pingdom — availability confirmed 100%
- 03:06 — Incident closed in Jira — post-incident notes added to Confluence

---

## Alert Details

- **Tool that fired:** Kentik
- **Alert name:** Volumetric DDoS — UDP Flood Threshold Exceeded
- **Threshold crossed:** Inbound UDP traffic exceeded 2 Gbps for more than 60 seconds
- **Affected customer:** Customer A (role-based placeholder)
- **Targeted IP:** x.x.x.x (customer edge IP)
- **Destination ports:** Random high ports — no consistent port pattern

---

## Traffic Analysis

**Grafana observations**

- Sharp vertical spike on inbound traffic graph at 02:14
- Traffic climbed from 800 Mbps to 4.2 Gbps within 90 seconds
- UDP protocol dominated — approximately 94% of inbound traffic during attack
- No corresponding outbound spike — confirming inbound volumetric flood

**Kentik observations**

- Flow data showed traffic originating from multiple source IPs across different ASNs
- Source IPs distributed across 12+ countries — consistent with botnet-sourced flood
- Destination port randomisation observed — no single port targeted
- Peak packet rate: approximately 3.1 million packets per second

**Attack characteristics**

- No TCP handshake — pure UDP flood
- Large packet sizes (1400–1500 bytes) — designed to maximise bandwidth consumption
- No application payload — garbage data in UDP body
- Classic volumetric L3 flood — goal was to saturate the customer's bandwidth pipe

---

## Investigation Steps

1. Acknowledged Kentik alert and opened Jira ticket INC-2041
2. Opened Grafana dashboard — confirmed traffic spike on customer edge interface
3. Checked protocol breakdown in Kentik — UDP confirmed as dominant protocol
4. Checked destination IP — single customer edge IP being targeted
5. Verified source IP distribution in Kentik — multiple ASNs, botnet pattern confirmed
6. Checked Pingdom — customer service showing degraded response times
7. Assessed impact — customer bandwidth pipe at approximately 85% saturation
8. Decided on null route mitigation — customer IP had no active critical sessions
9. Applied null route via upstream provider BGP community
10. Monitored Grafana and Kentik post-mitigation to confirm traffic drop
11. Verified Pingdom recovery — availability restored
12. Monitored for 30 minutes before removing null route
13. Confirmed attack had subsided before restoring normal routing

---

## Mitigation Actions

**Action taken: Null Route (BGP Blackhole)**

- Null route applied to the targeted destination IP via upstream provider
- BGP blackhole community string advertised to upstream — traffic dropped at provider edge
- Customer service impact during null route: targeted IP was unreachable (expected)
- Null route held for 39 minutes until attack traffic subsided
- Normal routing restored after confirming attack had stopped in Kentik flow data

**Why null route was chosen**

- Attack volume (4.2 Gbps) exceeded customer pipe capacity — scrubbing alone would not have been fast enough
- Customer had no active critical sessions on the targeted IP at the time
- Null route is the fastest mitigation for a pure volumetric flood of this scale

---

## Root Cause Analysis

- Targeted customer IP was publicly exposed with no prior rate limiting or upstream filtering
- Botnet-sourced UDP flood — likely opportunistic, not targeted
- No prior DDoS protection policy configured for this customer segment

---

## Resolution

- Null route removed at 03:01 after attack traffic confirmed as subsided
- Customer service fully restored — Pingdom showing 100% availability
- Incident ticket INC-2041 closed at 03:06
- Customer notified of incident details and resolution time

---

## Lessons Learned

- Upstream rate limiting for UDP traffic should be pre-configured for customers in high-risk segments
- Kentik alert threshold of 2 Gbps detected the attack early — response time was good
- Scrubbing centre referral should be evaluated for customers requiring higher availability SLAs during attacks

---

## Ticket Reference

- **Jira:** INC-2041
- **Confluence:** NOC > Incident Reports > 2024

---

> ⚠️ This is a fictional incident report created for portfolio and learning purposes. No real customer or company data is included.

---

## 🤝 Connect

- 📧 swetharajendran4055@gmail.com
- 💼 LinkedIn — https://www.linkedin.com/in/swetha-rajendran-4349a12a1
- 🐙 GitHub — https://github.com/swetharajendran2015-a11y
- 🎥 YouTube — https://www.youtube.com/@alexessecurity
