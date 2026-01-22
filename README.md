# SSH Log Analysis using Splunk - Project


## Objective
The goal of this project is to analyze SSH authentication logs to detect unauthorized access attempts, abnormal login behavior, and potential SSH brute-force attacks.
- **Successful SSH logins** (who connected, from where)
- **Failed login attempts** (possible brute-force or password spraying)
- **Multiple failed authentication attempts** (brute-force)
- **Connections without authentication** (potential scanning or incomplete sessions)

By completing this project, I developed practical skills in ingesting SSH logs into Splunk, analyzing them using SPL, building dashboards, and configuring security alerts.


## Lab Setup & Pre-requisite
* Download SSH Logs File > [ssh\_logs.json](https://github.com/JaswinderSinghRawat/SSH-Log-Analysis-using-Splunk/blob/61454b49e12bd6e8ef386619a9f23d4b29af91ed/ssh_logs.json)


## Log Ingestion Steps
1. Log in to your Splunk instance (Enterprise or Free edition).
2. Navigate to **Apps → Search & Reporting**.
3. Click **Add Data** and select **Upload**.
4. Upload the provided `ssh_log.json` file.
5. Set the **sourcetype** to `_json` to enable automatic field extraction.
6. Create and select a new index (e.g., `ssh_logs`).
7. Review the configuration and start searching the ingested data.

## Step-by-Step Guide

### STEP 1 : Ingest and Parse SSH Logs

1. Upload the `ssh_log.json` file into Splunk.
2. Verify that the following fields are extracted correctly:
   - `event_type`  
     *(Successful SSH Login, Failed SSH Login, Multiple Failed Authentication Attempts, Connection Without Authentication)*
   - `auth_success` *(true / false / null)*
   - `auth_attempts`
   - `id.orig_h` *(source IP address)*
   - `id.resp_h` *(destination host)*
3. **Validation Query:**
I used the following SPL query to confirm that logs were successfully indexed and the `event_type` field was populated correctly:
```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json"
| stats count by event_type
```
### Step 2: Analyze Failed SSH Login Attempts

Identify all failed SSH login attempts:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Failed SSH Login"
| stats count by id.orig_h
```

- Highlight the top 10 source IP addresses generating failed login attempts.
- Create a bar chart visualization showing failed login attempts per source IP.

### Step 3: Detect Multiple Failed Authentication Attempts (Brute Force)

Search for multiple failed authentication attempts in SSH logs:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Multiple Failed Authentication Attempts"
| stats count by id.orig_h, id.resp_h
```

Detect repeated failures (e.g., more than 5 attempts).

Configure a Splunk alert:
- Trigger when any source IP attempts more than 5 login attempts within a 10-minute window.

---

### Step 4: Track Successful SSH Logins

Search for successful SSH login events:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Successful SSH Login"
| stats count by id.orig_h, id.resp_h
```

- Compare successful logins against prior failed attempts to identify potentially compromised accounts.
- Create a dashboard panel displaying the top source IPs for successful SSH logins.

### Step 5: Spot Suspicious Connections Without Authentication

Search for unauthenticated SSH connections:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Connection Without Authentication"
| stats count by id.orig_h
```

Create a timechart visualization to monitor unauthenticated SSH connections over time:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Connection Without Authentication"
| timechart count by id.orig_h
```

- Identify repeated unauthenticated connection attempts, which may indicate SSH probing or port scanning activity.

---

## Conclusion

By the end of this project, I was able to:

- Build dashboards to monitor SSH activity.
- Identify brute-force login attempts and other suspicious access patterns.
- Configure Splunk alerts for high-risk behavior.
- Parse, search, visualize, and alert on SSH logs using Splunk.

This project demonstrates practical SOC Analyst–level log analysis skills and strengthens my cybersecurity portfolio.

