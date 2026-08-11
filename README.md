# Microsoft-Sentinel-Playbooks-
Cowrie SSH Honeypot Integration with Microsoft Sentinel 
## Part 1- This part of the project covers deploying a Cowrie SSH Honeypot on an Azure Linux VM and successfully ingesting its logs into Microsoft Sentinel (Log Analytics) for security monitoring and analysis 

 # Honeypot Deployment 
<img width="1469" height="770" alt="{A2D05FAD-E090-4D55-85F4-C6BB16E5C812}" src="https://github.com/user-attachments/assets/3b434afe-3221-4dd4-a559-e0f4cec2e27c" />

- Cowrie honeypot successfully deployed inside a Python virtual environment (Python 3.12) and running as a background process, listening for simulated SSH connections on port 2222 

- Verified Cowrie is actively logging SSH session activity such as eventid, src_ip, username, password, and message

## Initial Ingestion Attempt & Problems
- Firstly, there are two types of tables which are Azure Monitor Agent(AMA) and Data Collection Rule(DCR) to configure to ship Cowrie's JSON logs to a custom Log Analytics table (CowrieHoneypot_CL)

<img width="1463" height="485" alt="{DADDF4D9-FB04-4C24-9CCD-97CC1582FADB}" src="https://github.com/user-attachments/assets/6158a6a5-a809-4897-88d1-2ebdbdd49ab2" />

 - Problem: The resulting table contained only default system columns( TimeGenerated, TenantId, Type, _ResourceId). No RawData or parsed data were present, and running getschema against the table confirmed the expected structured fields simply did not exist.
 - Root Cause Analysis: The current supported pipeline - AMA + Data Collection Rules (DCR)- behaves differently: raw JSON log content is not automatically flattened into columns. An explicit Transform step( a KQL-based JSON parsing rule) must be defined within the DCR for structured fields like eventid and src_ip to be extracted and populated correctly.

 <img width="1920" height="1080" alt="{DF201733-3E47-4805-8D91-2E09BD153934}" src="https://github.com/user-attachments/assets/b48d4489-f3e4-4875-82de-edff4128530d" />


