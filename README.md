# Event ID 115 — Possible SQL Injection Payload Detected


## Short Overview of the Investigation 🔬 

This investigation examines a high-severity LetsDefend alert triggered by a URL containing a potential SQL injection payload. The investigation focused on determining whether the traffic was malicious, whether the attack succeeded, and whether Tier 2 escalation was necessary.

*Investigation verdict*: True Positive - Malicious SQL injection attempt was unsuccessful.


## Alert Summary and Timeline ⌛ 

### Alert Summary
| Field | Details | 
| ----- | ------- |
| Platform | LetsDefend |
| Event ID | 115 |
| Rule | SOC165 - Possible SQL Injection Payload Detected |
| Severity | High |
| Event Date | February 25, 2022 | 
| Event Time | 11:34 AM |
| Hostname | WebServer1001 |
| Source IP | 167.99.169.17 |
| Destination IP | 172.16.17.18 |
| HTTP Method | GET |
| Endpoint | /search/ |
| Requested URL | https://172.16.17.18/search/?q=%22%20OR%201%20%3D%201%20--%20- | 
| Device Action | Allowed |
| Attack Type | SQL Injection | 

### Timeline
- *11:30–11:34 AM*: Multiple requests containing SQL injection payloads were sent to the web server.
- *11:34 AM*: The alert was generated for a request containing the pattern OR 1 = 1.
- *Log investigation*: Related requests were reviewed, including attempts using tautologies and SQL syntax manipulation.
- *Response analysis*: The suspicious requests consistently returned HTTP 500 errors with identical response sizes.
- *Endpoint investigation*: No evidence of successful exploitation or host compromise was identified.
- *Final assessment*: The attack was unsuccessful, and Tier 2 escalation was not required.


## Investigation Steps 🕵️‍♂️ 

### 1. Alert Triage
I reviewed the alert metadata, source and destination IP addresses, HTTP method, requested URL, and device action.

The URL contained an encoded SQL injection payload. After decoding, the relevant expression was: " OR 1 = 1 -- -

The condition 1 = 1 is always true, while the SQL comment syntax may be used to alter the interpretation of a database query.

### 2. Log Management and Traffic Analysis
I filtered the logs for requests from 167.99.169.17 to 172.16.17.18.

Several requests targeted the /search/ endpoint using different SQL injection probes, including:

- ' OR '1
- ' OR 'x'='x
- 1' ORDER BY 3--+
- " OR 1 = 1 -- -

The sequence suggested deliberate testing of the application's handling of SQL input rather than ordinary browsing.


### 3. Threat Intelligence Investigation
I investigated the source IP using threat intelligence resources. The address was associated with DigitalOcean infrastructure and had a negative reputation in abuse-reporting sources.

The reputation supported the assessment, but the SQL injection payloads and related logs were the more direct evidence of malicious activity.

### 4. HTTP Response Analysis
I compared the responses to suspicious requests with normal traffic.

- Normal requests returned HTTP 200 responses with a response size of approximately 3,547 bytes.
- SQL injection attempts returned HTTP 500 errors with response sizes of approximately 948 bytes.

The consistent error responses suggested that the application was failing to process the injected queries rather than returning evidence of successful exploitation.

### 5. Endpoint Investigation
I reviewed the available endpoint, terminal, and browser activity for signs of compromise. No suspicious commands or evidence of successful exploitation were identified in the documented investigation.

Taken together, the HTTP errors, consistent response sizes, and lack of endpoint compromise evidence supported the conclusion that the attack had failed.

### 6. Attack Success and Tier 2 Escalation
I initially marked the attack as successful and selected Tier 2 escalation. Both answers were incorrect.

The correct assessment was that the SQL injection attempts were unsuccessful, and the playbook did not require Tier 2 escalation. The alert was still a true positive because the malicious activity genuinely occurred.


## My Findings, Conclusions, and Response 🙋‍♀️ 

### Findings
- An external IP sent multiple SQL injection payloads to the web server.
- The requests targeted the /search/ endpoint.
- The firewall allowed the traffic.
- The suspicious requests consistently returned HTTP 500 responses with identical response sizes.
- No evidence of successful exploitation or host compromise was identified.
- Tier 2 escalation was not required by the playbook.

### Conclusion
This was a True Positive unsuccessful SQL injection attempt. The attacker attempted to manipulate database queries, but the available evidence did not indicate successful exploitation. The alert could be closed after documenting the findings and appropriate follow-up actions.

### My Response
1. I blocked the source IP based on the organization's security policy.
2. I monitored web logs for repeated SQL injection attempts and related suspicious requests.
3. I reviewed application error handling to avoid exposing sensitive technical information.
4. I recommended parameterized queries and prepared statements to prevent SQL injection.
5. I considered tuning WAF rules to detect and block malicious SQL payloads.
6. I documented the unsuccessful attempt and close the alert according to the SOC playbook.


## Screenshots 📷 

<img width="584" height="289" alt="image" src="https://github.com/user-attachments/assets/af6c23b2-5e41-4b48-91d2-a1a8ddcc8845" />

<img width="593" height="302" alt="image" src="https://github.com/user-attachments/assets/34052536-9957-4c45-a962-7a994a03b32f" />

<img width="581" height="292" alt="image" src="https://github.com/user-attachments/assets/4cfa129e-3f1c-4ce2-bf22-f4a8deb56f4e" />

<img width="573" height="242" alt="image" src="https://github.com/user-attachments/assets/60a18795-0f26-4013-9d57-f8d35871bee9" />


## Lessons Learned ✍️ 

- *A true positive can still be an unsuccessful attack*: The alert correctly identified malicious activity, even though exploitation failed.
- *Do not confuse an attempt with a compromise*: A malicious payload does not automatically mean the attacker gained access to data or systems.
- *Correlate response codes and response sizes*: Consistent HTTP 500 responses supported the conclusion that the injection attempts were unsuccessful, especially when combined with endpoint findings.
- *Escalate based on evidence and playbook criteria*: A high-severity alert does not automatically mean Tier 2 escalation is required.
- *Learn from mistakes*: I achieved a 60% playbook success rate and incorrectly marked the attack as successful and requiring Tier 2 escalation. This investigation reinforced the importance of examining server responses and endpoint evidence before making a final assessment.

