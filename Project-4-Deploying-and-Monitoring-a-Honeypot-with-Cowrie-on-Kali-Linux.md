# Project 4: Deploying and Monitoring a Honeypot with Cowrie on Kali Linux

## Introduction
In this project, I learned how to deploy and monitor a honeypot using Cowrie, a popular SSH and Telnet honeypot. Honeypots are valuable tools for security research, as they can capture and analyze malicious activities. By the end of this project, I was able to set up Cowrie, monitor intrusions, and analyze the collected data.

## Pre-requisites
- Basic understanding of SSH and Telnet protocols.
- Familiarity with using the command line interface (CLI).
- Basic knowledge of network security concepts.

## Lab Set-up and Tools

### Diagram
Here is a simple network diagram for this lab setup:
+------------------+ +------------------+
| Attacker | | Honeypot |
| Kali Machine |<----->| (Cowrie) |
| (192.168.1.100) | | (192.168.1.101) |
+------------------+ +------------------+


### Tools
- **Kali Linux**: A Debian-derived Linux distribution designed for digital forensics and penetration testing.
- **Cowrie**: A medium-interaction SSH and Telnet honeypot.
- **Docker**: For setting up the Cowrie honeypot in a containerized environment.

### Installation
#### Docker
1. Install Docker on your Kali Linux machine:
   ```sh
   sudo apt-get update
   sudo apt-get install docker.io
   sudo systemctl start docker
   sudo systemctl enable docker
   ```
2. Pull te Crowrie Docker Image
   ```
   sudo docker pull cowrie/cowrie
   ```
3. Run the Cowrie container:
   ```
   sudo docker run -d -p 2222:2222 -p 2223:2223 --name cowrie cowrie/cowrie
   ```
4. Cowrie should now be running and listening on ports 2222 (SSH) and 2223 (Telnet).

## Tasks

Following are the tasks and steps I performed in order to complete this Hand-On Project.
### Task 1: Verify Honeypot Deployment
Step 1: Open a terminal in Kali Linux machine.
Step 2: Check if the Cowrie container is running:
  ```sh
  sudo docker ps
  ```
Output: The Cowrie container should be listed as running.

### Task 2: Simulate an Attack
Step 1: From Kali Linux machine, attempt to connect to the honeypot using SSH:
  ```sh
  ssh root@localhost -p 2222
  ```
Step 2: Enter any password when prompted.
Output: The honeypot should log the attempt and display a fake shell environment.

### Task 3: Monitor Honeypot Logs
Step 1: Access the logs of the Cowrie container:
  ```sh
  sudo docker logs cowrie
  ```
Output: The logs should show details of the SSH connection attempt, including the username, password, and commands entered.

### Task 4: Analyze Captured Data
Step 1: Open another terminal in Kali Linux machine.
Step 2: Access the Cowrie logs directory within the container:
  ```sh
  sudo docker exec -it cowrie /bin/bash
  cd /srv/cowrie/var/log/cowrie
  ```
Step 3: Analyze the cowrie.json file for detailed logs of captured sessions:
  ```sh
  cat cowrie.json | jq '.'
  ```
Output: Detailed JSON log entries of the captured sessions, including connection attempts and command execution.

### Task 5: Forwarding Logs to a Splunk SIEM
Step 1: Set up Splunk on network (refered to the Splunk documentation for installation steps).
Step 2: Configure Cowrie to forward logs to Splunk by editing the cowrie.cfg configuration file
  ```
  sudo docker exec -it cowrie /bin/bash
  nano /srv/cowrie/cowrie.cfg
  ```
Step 3: Add the following configuration to forward logs to your Splunk instance:
  ```
  [output_splunk]
  enabled = true
  token = <SPLUNK_HEC_TOKEN>
  http_event_collector = https://<SPLUNK_IP>:8088/services/collector/event
  sourcetype = cowrie
  ```
Step 4: Save the file and restart the Cowrie container to apply the changes:
  ```
  sudo docker restart cowrie
  ```
Output: Cowrie logs should now be forwarded to your Splunk instance.

### Task 6: Creating Custom Alerts in Splunk
Step 1: Open the Splunk web interface and log in.
Step 2: Navigate to Search & Reporting and search for Cowrie logs using the following query:
```spl
sourcetype="cowrie"
```
Step 3: Set up custom alerts based on specific events captured by Cowrie (e.g., repeated login attempts):
  - Click on Save As > Alert.
  - Define alert conditions (e.g., more than 5 failed login attempts within 10 minutes).
  - Configure alert actions (e.g., send an email or trigger a webhook).
Output: Splunk should generate alerts based on the defined conditions, providing real-time notifications of suspicious activities.

### Task 7: Visualizing Honeypot Data in Splunk
Step 1: Use Splunk's data visualization tools to create dashboards that visualize the data collected by Cowrie.
Step 2: Navigate to Dashboards and create a new dashboard.
Step 3: Add visualizations (e.g., charts, tables) to display attack patterns and trends over time:
  - Example visualizations: Number of login attempts, top source IPs, common commands executed.
Output: Visualized data showing attack patterns, frequencies, and other relevant metrics, aiding in security research and analysis.

## Additional Resources
Cowrie Documentation
Kali Linux Documentation
Docker Documentation
Splunk Documentation
Splunk Tutorials

This project helped me understand how to deploy and monitor a honeypot using Cowrie on Kali Linux, forward the logs to Splunk, and analyze the data to gain insights into malicious activities.





