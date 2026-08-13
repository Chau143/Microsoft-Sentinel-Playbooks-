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

-Data Collection Rule (DCR) configuration for "Windows Event Logs" selected, with XPath filter for EventID 4624 for successful logon and 4625 for failure logon
  
<img width="1910" height="955" alt="{7FDF722D-B05A-49CD-9A3B-B65BC4E1B8CE}" src="https://github.com/user-attachments/assets/abf50cfe-fac1-4acc-82e5-521e7c851047" />

- Verifying Agent Connectivity- Confirmed Azure Monitor Agent connectivity - The Windows VM is successfully reporting to the workspace 

<img width="1909" height="970" alt="{64B8DD6D-3713-4CEB-9BDA-0EBFE53B10EB}" src="https://github.com/user-attachments/assets/2ef453f0-e53c-43de-bb0b-9ecfe183af99" />

- Generating Test Logon Activity- Remote Desktop Connection dialog/ successful and failure RDP login attempt

<img width="1911" height="973" alt="{8C1CFB1A-049B-4956-AF9B-0D3D132E4F69}" src="https://github.com/user-attachments/assets/bad9f49f-d869-43db-850b-d87e10d53501" />

- Successful Logon Query(EventID 4624)- KQL query editor with the 4624 query and populated results table

<img width="1893" height="953" alt="{8E7F7ED9-DAE1-4308-8B1A-8CF3F27C3D38}" src="https://github.com/user-attachments/assets/97bbce40-d9b4-4380-a627-0bcad90f95e6" />

- Failure Logon Query(EventID 4625) - KQL query editor with the 4625 query and populated results table

<img width="1912" height="959" alt="{0B09CF69-AA8B-4619-B1A6-01FAC23E479B}" src="https://github.com/user-attachments/assets/06698a5b-d2a8-4a3b-bff1-ebdb6cedf42c" />

- Added Microsoft Sentinel to Log Analytics Workspace
<img width="1906" height="597" alt="{23A82BDD-ECE0-4A9B-94BA-67FE0279D77C}" src="https://github.com/user-attachments/assets/03a8c63e-8873-4066-b41b-2b28476c2bad" />

- Created Cowrie Sentinel Workbook to visualize where the successful logon are coming from
<img width="1904" height="975" alt="{6D5646E4-DB29-4AA7-9E55-326602198E5C}" src="https://github.com/user-attachments/assets/eb794061-7f9c-4804-9a22-51bcf90d0597" />

<img width="1915" height="963" alt="{E76358F2-41F9-45B0-820F-3F97348CDDF2}" src="https://github.com/user-attachments/assets/6e0b2e05-c2df-433f-b8cf-65e07cdfec44" />

- Created Windows Sentinel Workbook to visualize where the failure and successful logon by Windows remote desktop
_ Failure logons
<img width="1894" height="670" alt="{C187E32F-8237-47BD-95AE-C5713C8831E9}" src="https://github.com/user-attachments/assets/6aaee610-3976-4a02-8f0a-b05c1c7f59ed" />


<img width="931" height="682" alt="{EA87B1B4-332F-4EF0-BE34-07716FE6A71B}" src="https://github.com/user-attachments/assets/3e755c45-a539-4d92-9403-c9484047f283" />

_ Successful logons 
<img width="1913" height="962" alt="{E1BD8925-A6AF-4890-95A6-7E896D199644}" src="https://github.com/user-attachments/assets/ef967b12-b56f-446a-924c-9f59263145c8" />

<img width="1070" height="639" alt="{ECB15A2E-1610-4496-91FF-968BEC34B7CA}" src="https://github.com/user-attachments/assets/e7e9324c-69f0-4be5-978c-a1414477e093" />

#Section3: Automated IP Reputation Enrichment(Logic App Playbook + AbuseIPDB)
- Playbook Creation: Created a new Azure Logic App to serve as an automated playbook for enriching incoming security incidents with AbuseIPDB threat intelligence data
<img width="1916" height="959" alt="{8AEC4D98-2A3C-4E5C-A144-4EB7B180DE58}" src="https://github.com/user-attachments/assets/a9998d95-2a32-442e-9cfe-5f8b704dfd5d" />

<img width="1903" height="973" alt="{AD79F4A9-A98F-4FED-8DB3-501C1A429DE6}" src="https://github.com/user-attachments/assets/267f700f-9c51-41d1-b072-299a52b5915e" />


- Logic App Overview: Logic App Playbook successfully deployed and enable, ready to be triggered by Microsoft Sentinel 

<img width="1904" height="973" alt="{43C6CFEA-FC24-4FC6-AFAE-B064D7A727EE}" src="https://github.com/user-attachments/assets/ce5d6d38-4c9b-4733-94c9-2f8ce52e2ec7" />
