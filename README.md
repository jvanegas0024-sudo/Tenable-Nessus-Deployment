# Tenable Nessus Vulnerability Scanner Deployment & Uncredentialed Scan Lab

- Transparency Note: Raw lab notes (Tenable_Installation_Instructions.txt) were structured and formatted into Markdown with AI assistance.

## Objective
This project documents the deployment of Tenable Nessus on an Ubuntu Server virtual machine, followed by executing an uncredentialed network scan against a media server target and remediating identified vulnerabilities.

## Skills Applied
- Virtual Machine Provisioning
- Linux Server Administration (Ubuntu, SSH, APT package management)
- Vulnerability Scanner Installation & Service Management
- Application Configuration via CLI
- Vulnerability Assessment & Remediation

## Tools Used
- [VMware Workstation](https://www.vmware.com/products/workstation-pro.html)
- [Ubuntu Server ISO](https://ubuntu.com/download/server)
- [Tenable Nessus Essentials](https://www.tenable.com/tenable-nessus-for-education)
- Secure Copy Protocol (SCP)
- OpenSSH / Terminal

---

## Part 1: Nessus Deployment on Ubuntu

### 1. Virtual Machine Provisioning
- Open VMware and click **Create a New Virtual Machine**.
- Select **Typical configuration** in the wizard and click Next.
- Choose the **Guest OS** by browsing to the Ubuntu Server image and click Next.
- Name the virtual machine `Ubuntu-Tenable`.
- Set the disk capacity to **60GB** (40GB+ recommended) and store the virtual disk as a single file, then click Next.
- Click **Customize Hardware** and allocate **8GB of RAM** and **4 processors**, then click Finish.

<img src="images/vmwaremainmenu.png" width="600" alt="VMware Configuration">
<img src="images/vmwizard.png" width="600" alt="VMware Configuration">
<img src="images/selectos.png" width="600" alt="VMware Configuration">
<img src="images/vmconfiguration.png" width="600" alt="VMware Configuration">

### 2. Ubuntu Server Configuration
- Proceed through the Ubuntu installer using default options until reaching the Profile Configuration.
- Create a username, server name, and password.
- Skip Ubuntu Pro configuration for now.
- Select the option to install the **OpenSSH server**.
- Once the installation completes, select **Reboot Now**.
- After rebooting, log in and update the system packages by running `sudo apt update && sudo apt upgrade -y`.

<img src="images/ubuntutypeinstallation.png" width="600" alt="Ubuntu Setup">
<img src="images/ubuntustorage.png" width="600" alt="Ubuntu Setup">
<img src="images/profileconf.png" width="600" alt="Ubuntu Setup">
<img src="images/updateupgrade.png" width="600" alt="Ubuntu Setup">

### 3. Tenable Nessus Installation
- Transfer the `Nessus.deb` file to the VM under the `/home/user/tmp` path using the SCP command.
- Navigate to the directory (`cd /tmp`) and unpack the installation file by running `sudo dpkg -i Nessus-*.deb`.
- Install any missing dependencies by running `sudo apt -f install -y`.
- Start the Nessus service by running `sudo systemctl start nessusd.service`.
- Verify the service is running with `sudo systemctl status nessusd.service`.
- Verify the web interface is accessible by navigating to `https://<VM's IP>:8834`.

### 4. Nessus Activation & Account Setup via CLI
- Register the Nessus instance using your activation code by running `sudo /opt/nessus/sbin/nessuscli fetch --register <Your-activation-code>`.
- Create an administrative account by running `sudo /opt/nessus/sbin/nessuscli adduser <nameoftheuser>` and follow the command prompts.
- Restart the Nessus service to apply the configuration by running `sudo systemctl restart nessusd.service` (or `bin/systemctl restart nessusd.service`).
- Go back to the web UI and hit refresh.
- Log in with the newly created account, a new host discovery scan will automatically start.

<img src="images/nessuswelcomepage.png" width="600" alt="Nessus Web UI">
<img src="images/hostdiscovery.png" width="600" alt="Nessus Web UI">

---

## Part 2: Nessus Essentials Lab: Uncredentialed Scan & SMB Signing Remediation

### Overview
This section covers executing an uncredentialed network scan using Nessus Essentials against a media server target (`192.168.8.139`), analyzing discovered baseline risks, and remediating the SMB signing vulnerability.

### Lab Procedure & Notes

1. **Perform Uncredentialed Scan**
   * Click **New Scan** on the top right corner of the home page and select **Basic Network Scan**.
   * Under name, named it `uncredentialed_scan_media_sever`, gave it a short description, saved it to the **My Scans** folder, and provided the target address of the media server (`192.168.8.139`).
   * After filling out the information, click **Save** and click the **Launch** button that's on the far right.

2. **Generate Report**
   * Create and export the scan report.

<img src="images/vulnfound.png" width="600" alt="Vulnerability">

3. **Vulnerability Analysis & Remediation**
   * **Research the solution for remediation:** The only vulnerability found was that signing is not required on the remote SMB server.
   * **Remediation Steps:** From the Ubuntu server, I connected to the media server via SSH and changed the `smbglb.conf` files to require signing by adding `server signing = required` under the global section.

<img src="images/vulnerability.png" width="600" alt="Vulnerability">
<img src="images/remediation.png" width="600" alt="Remediation">
