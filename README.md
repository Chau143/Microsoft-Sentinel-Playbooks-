# Microsoft-Sentinel-Playbooks-
Cowrie SSH Honeypot Integration with Microsoft Sentinel 
## Part 1- This part of the project covers deploying a Cowrie SSH Honeypot on an Azure Linux VM and successfully ingesting its logs into Microsoft Sentinel (Log Analytics) for security monitoring and analysis 

 # Section1: Honeypot Deployment 
<img width="1469" height="770" alt="{A2D05FAD-E090-4D55-85F4-C6BB16E5C812}" src="https://github.com/user-attachments/assets/3b434afe-3221-4dd4-a559-e0f4cec2e27c" />

- Cowrie honeypot successfully deployed inside a Python virtual environment (Python 3.12) and running as a background process, listening for simulated SSH connections on port 2222 

- Verified Cowrie is actively logging SSH session activity such as eventid, src_ip, username, password, and message

## Initial Ingestion Attempt & Problems
- Firstly, there are two types of tables which are Azure Monitor Agent(AMA) and Data Collection Rule(DCR) to configure to ship Cowrie's JSON logs to a custom Log Analytics table (CowrieHoneypot_CL)

<img width="1463" height="485" alt="{DADDF4D9-FB04-4C24-9CCD-97CC1582FADB}" src="https://github.com/user-attachments/assets/6158a6a5-a809-4897-88d1-2ebdbdd49ab2" />

 - Problem: The resulting table contained only default system columns( TimeGenerated, TenantId, Type, _ResourceId). No RawData or parsed data were present, and running getschema against the table confirmed the expected structured fields simply did not exist.
 - Root Cause Analysis: The current supported pipeline - AMA + Data Collection Rules (DCR)- behaves differently: raw JSON log content is not automatically flattened into columns. An explicit Transform step( a KQL-based JSON parsing rule) must be defined within the DCR for structured fields like eventid and src_ip to be extracted and populated correctly.

<img width="1910" height="941" alt="{39235C93-ADCA-4C48-BF6A-E036EC58E710}" src="https://github.com/user-attachments/assets/b2e837cf-84ca-4d6c-8ddd-aa5b0e5acdbd" />

- Problem Solved: The transformation was validated successfully and saved. The Azure Monitor Agent was restarted to force it to pull the updated DCR configuration, and fresh SSH test sessions were generated against the honeypot to produce new log entries for validation
- Verification & Final Result: KQL query was used to confirm successful ingestion and field parsing: 
  
<img width="1908" height="943" alt="{B42ED1FB-E06B-4A1C-8D4D-F0C68DDB2C7A}" src="https://github.com/user-attachments/assets/17cd42c3-14b2-4ead-ac31-592df811fe9e" />

<img width="1072" height="327" alt="{2A7ADBFB-A3C3-4BF3-8832-B2C0DB1FEF95}" src="https://github.com/user-attachments/assets/9475699d-f989-4471-8519-637d4c767d5e" />

## Analytics Rule & Email Notification
- An Analytics Rule was configured in Microsoft Sentinel to automatically detect suspicious activity captured by the Cowrie honeypot, based on the parsed CowrieHoneypot_CL table
<img width="1886" height="969" alt="{0388E479-9DCE-4D83-8323-2B5121BE1CC7}" src="https://github.com/user-attachments/assets/99dd52fb-bfc7-4629-b351-5aa221e2127e" />

<img width="1179" height="2080" alt="IMG-7438" src="https://github.com/user-attachments/assets/733f0bf5-a56d-4e8f-a3b1-efc99e2e1469" />

<img width="1172" height="2046" alt="IMG-7439" src="https://github.com/user-attachments/assets/22d18e36-7198-46af-8f26-3715413b3a19" />

# Section2: Windows VM Deployment & Security Event Monitoring 
- Windows VM successfully deployed and running, with a public IP assigned for remote desktop connection 
<img width="1906" height="982" alt="{53A43D04-4BBC-4D82-A467-05CBEFDE4086}" src="https://github.com/user-attachments/assets/036b1517-0751-4935-857d-e8b41782201b" />

<img width="1907" height="1080" alt="image" src="https://github.com/user-attachments/assets/cae50fce-be69-4b09-82a4-739a5738d62f" />

- Data Collection Rule (DCR) for Windows Event Logs (Specifically for successful and failed logins with EventID 4624 & 4625)
- 
<img width="1910" height="955" alt="{7FDF722D-B05A-49CD-9A3B-B65BC4E1B8CE}" src="https://github.com/user-attachments/assets/abf50cfe-fac1-4acc-82e5-521e7c851047" />


