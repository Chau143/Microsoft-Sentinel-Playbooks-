# Microsoft-Sentinel-Playbooks-
Cowrie SSH Honeypot Integration with Microsoft Sentinel 
## Part 1- This part of the project covers deploying a Cowrie SSH Honeypot on an Azure Linux VM and successfully ingesting its logs into Microsoft Sentinel (Log Analytics) for security monitoring and analysis 

 # Honeypot Deployment 
<img width="1469" height="770" alt="{A2D05FAD-E090-4D55-85F4-C6BB16E5C812}" src="https://github.com/user-attachments/assets/3b434afe-3221-4dd4-a559-e0f4cec2e27c" />

Cowrie honeypot successfully deployed inside a Python virtual environment (Python 3.12) and running as a background process, listening for simulated SSH connections on port 2222 
Verified Cowrie is actively logging SSH session activity such as eventid, src_ip, username, password, and message 
