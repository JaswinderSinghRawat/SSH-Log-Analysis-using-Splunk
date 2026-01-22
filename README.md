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
3. Run the following validation search to confirm successful ingestion and parsing:
```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json"
| stats count by event_type
```


