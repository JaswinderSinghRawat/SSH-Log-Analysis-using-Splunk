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

## Step-by-Step Guide
### 📥Step 1: Log Ingestion in Splunk

In this step, I ingested SSH log data into Splunk to begin the analysis process.

1. I logged in to my Splunk instance (Enterprise or Free edition).
2. Navigated to **Apps → Search & Reporting**.
3. Selected **Add Data** and chose the **Upload** option.
4. Uploaded the provided `ssh_log.json` file.
5. Set the **sourcetype** to `_json` to enable automatic field extraction.
6. Created and selected a new index (e.g., `ssh_logs`).
7. Reviewed the configuration and started searching the ingested data.

   _In this video, I demonstrate how I ingested SSH logs into Splunk._

   [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/gjINdGCkGZg/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgALCBYoCDAgAEAEYZiBmKGYwDw==&rs=AOn4CLB75dWk4w-PL6PDwWzjcTIbtHSDJA)](https://youtu.be/gjINdGCkGZg?si=6HMm2EaTdn_N6Zn4)


### 🧩STEP 2 : Parse & Field Validation

In this step, I parsed the SSH logs to ensure that all important fields were extracted correctly.  
This was necessary to perform accurate analysis and detection in the later steps.

- Verified fields such as `event_type`, `auth_success`, `auth_attempts`, `id.orig_h`, and `id.resp_h`.
- Confirmed that Splunk automatically extracted fields using the `_json` sourcetype.
- Validated the parsing by running sample SPL queries.

I used the following SPL query to confirm that logs were successfully indexed and the `event_type` field was populated correctly.
```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json"
| stats count by event_type
```
   _In this video, I demonstrate how I parsed SSH logs and verified field extraction in Splunk._
   
   [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/D3btjX37AG4/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKaBYoCDAgAEAEYTyBfKGUwDw==&rs=AOn4CLAi_sUlrjP3VurmdiMdi7zrvqerXQ)](https://youtu.be/D3btjX37AG4?si=QGsYQ9zu9OJ9fYjN)
 
 Note This : `id.orig_h` *(source IP address)* , `id.resp_h` *(destination host)*
 
### 📊Step 3: Analyze Failed SSH Login Attempts

In this step, I analyzed failed SSH login attempts to identify potential brute-force or password-guessing activity.
This analysis helps in understanding which source IPs are repeatedly attempting to access the system.

I used the following SPL query to identify failed SSH login attempts by source IP:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Failed SSH Login"
| stats count by id.orig_h
```

  [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/dRrHTqSDAEM/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKiBYoCDAgAEAEYTiBeKGUwDw==&rs=AOn4CLBXp9xSIXBnhuStVYsFNy8ZoAOcnA)](https://www.youtube.com/watch?v=dRrHTqSDAEM&list=PLHM71OGMoBDM_wg7MvMeaIa0sG5KZEw0c&index=3)
 
### 🚨Step 4: Detect Multiple Failed Authentication Attempts (Brute Force)

In this step, I analyzed SSH logs to detect multiple failed authentication attempts, which may indicate a brute-force attack.
This helps in identifying source IPs that repeatedly try to authenticate within a short time period.

I used the following SPL query to identify multiple failed authentication attempts:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Multiple Failed Authentication Attempts"
| stats count by id.orig_h, id.resp_h
```
   _In this video, I demonstrate how I detected brute-force attempts and configured an alert in Splunk._
   
  [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/s8q5610XU8A/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKcBYoCDAgAEAEYTyBfKGUwDw==&rs=AOn4CLAlSVDPssEJX0eZ-95Ah37iLS4Q5Q)](https://www.youtube.com/watch?v=s8q5610XU8A&list=PLHM71OGMoBDM_wg7MvMeaIa0sG5KZEw0c&index=4)
  
Detect repeated failures (e.g., more than 5 attempts).

Configure a Splunk alert:
- Trigger when any source IP attempts more than 5 login attempts within a 10-minute window. see in next step

### 🔔Step 5: Creating Alert for Brute Force Attempts

In this step, I created a Splunk alert to detect possible SSH brute-force attacks.

- The alert monitors multiple failed authentication attempts in SSH logs.
- It groups events by source IP address.
- The alert is triggered when a single IP makes more than 5 failed login attempts within 10 minutes.

This alert helps in automatically identifying suspicious SSH activity without manual monitoring.

  _The complete alert creation and configuration process is demonstrated in the project video._
  
   [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/e3I6902dTBc/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKkBYoCDAgAEAEYUCBbKGUwDw==&rs=AOn4CLDe1D3TLCOSsK5T-Ng42KM8BvcLHw)](https://www.youtube.com/watch?v=e3I6902dTBc&list=PLHM71OGMoBDM_wg7MvMeaIa0sG5KZEw0c&index=5)

### ✅Step 6: Track Successful SSH Logins

In this step, I analyzed successful SSH login events to understand normal access patterns and identify suspicious behavior.
This helps in checking whether any successful login occurred after multiple failed attempts, which may indicate a compromised account.

I used the following SPL query to analyze successful SSH login events:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Successful SSH Login"
| stats count by id.orig_h, id.resp_h
```

- Compare successful logins against prior failed attempts to identify potentially compromised accounts.
- Create a dashboard panel displaying the top source IPs for successful SSH logins.

   _In this video, I demonstrate how I tracked successful SSH logins and visualized them using a Splunk dashboard._

   [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/r9PXSoIFwUw/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKSBYoCDAgAEAEYTyBaKGUwDw==&rs=AOn4CLBL2HHfTE0H1JtKy7MgVj6D2PwgPw)](https://www.youtube.com/watch?v=r9PXSoIFwUw&list=PLHM71OGMoBDM_wg7MvMeaIa0sG5KZEw0c&index=6)

### 🕵️Step 7: Spot Suspicious Connections Without Authentication

In this step, I analyzed SSH connections that were established without successful authentication.
Such activity may indicate SSH probing, scanning attempts, or incomplete connection sessions.

I used the following SPL query to identify unauthenticated SSH connections:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Connection Without Authentication"
| stats count by id.orig_h
```

To monitor these events over time, I created a timechart visualization:

```spl
source="ssh_logs.json" host="Acer" index="ssh_logs" sourcetype="_json" event_type="Connection Without Authentication"
| timechart count by id.orig_h
```

- Identify repeated unauthenticated connection attempts, which may indicate SSH probing or port scanning activity.
  
  _In this video, I demonstrate how I identified and visualized unauthenticated SSH connections in Splunk._
  
  [![SSH Log Analysis using Splunk Demo](https://i.ytimg.com/vi/6Pwe0aqgIvM/hqdefault.jpg?sqp=-oaymwFACKgBEF5IWvKriqkDMwgBFQAAiEIYAdgBAeIBCggYEAIYBjgBQAHwAQH4Af4JgAKUBYoCDAgAEAEYTyBfKGUwDw==&rs=AOn4CLCINh6L3WoBU8du4ylz4vyPYSxtxg)](https://www.youtube.com/watch?v=6Pwe0aqgIvM&list=PLHM71OGMoBDM_wg7MvMeaIa0sG5KZEw0c&index=7)
   
## Conclusion

By the end of this project, I was able to:

- Build dashboards to monitor SSH activity.
- Identify brute-force login attempts and other suspicious access patterns.
- Configure Splunk alerts for high-risk behavior.
- Parse, search, visualize, and alert on SSH logs using Splunk.

This project demonstrates practical SOC Analyst–level log analysis skills and strengthens my cybersecurity portfolio.

