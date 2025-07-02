# SOC Automation Project (Wazuh, TheHive and Shuffle)

This project documents the full design, deployment, and automation of a cloud-based Security Operations Centre (SOC) using open-source tools:

- **Wazuh**: Security Information and Event Management (SIEM)
- **TheHive**: Case management and investigation platform
- **Shuffle**: Orchestration and automation (SOAR)

The guide is based on hands-on implementation with cloud and local VMs. It includes all setup steps, diagrams, configurations, and integration examples.

---

## Part 1: Overview and Goals

### Project Overview

This project focuses on the design, deployment, and automation of a cloud-based Security Operations Centre (SOC) using open-source tools: **Wazuh** for Security Information and Event Management (SIEM), **TheHive** for case management, and **Shuffle** for orchestration and automation (SOAR). The primary objective is to simulate and streamline Tier 1 SOC analyst workflows in a lab environment.

The project is structured in modular sections, each dedicated to a specific component or function. Please note that I have implemented several modifications and added extra features beyond the original setup by @MyDFIR. Any future changes or enhancements will be documented at the beginning of the relevant module for accuracy and transparency.

This project was inspired and guided by the work of **Steven (@MyDFIR on YouTube)**, whose free educational content has been invaluable in building real-world cybersecurity skills.

### Objectives

***Primary Objectives:****

-   **Establish a Functional SOC Environment**: Deploy and configure a complete SOC infrastructure using Wazuh (SIEM), Shuffle (SOAR), and TheHive (Case Management) on cloud-based virtual machines
-   **Implement Multi-Platform Integration**: Successfully integrate three distinct security platforms through APIs and custom configurations to create a unified security ecosystem
-   **Automate Incident Response Workflows**: Develop automated playbooks that can detect, analyse, and respond to security incidents with minimal manual intervention
-   **Demonstrate SOC Tier 1 Competencies**: Showcase proficiency in tools and processes essential for entry-level SOC analyst roles

***Secondary Objectives:***

-   **Cloud Infrastructure Management**: Gain hands-on experience with cloud service deployment and network security configuration using DigitalOcean
-   **Security Tool Orchestration**: Master the configuration and management of enterprise-grade security tools in a distributed environment
-   **Documentation and Knowledge Transfer**: Create comprehensive documentation that can serve as a reference for similar implementations

### Acknowledgments

This project was inspired and guided by Steven from @MyDFIR on YouTube. His comprehensive tutorials and free educational content provided the foundational knowledge necessary to complete this implementation. Special thanks to the cybersecurity community for their continuous knowledge sharing and support in building practical security skills.

### Skills Learned

***Technical Skills:***

-   **SIEM Configuration and Management**: Deployed and configured Wazuh for log collection, analysis, and threat detection across multiple systems
-   **SOAR Platform Implementation**: Set up Shuffle workflows for automated incident response and security orchestration
-   **Case Management System Integration**: Configured TheHive for incident tracking, investigation management, and collaborative security operations
-   **Cloud Infrastructure Deployment**: Provisioned and managed virtual machines on DigitalOcean with appropriate resource allocation and security configurations
-   **Network Security Configuration**: Implemented cloud-based firewall rules and access controls for secure multi-VM communication
-   **API Integration and Management**: Established secure API connections between disparate security platforms for seamless data flow
-   **Linux System Administration**: Managed Ubuntu-based systems, including service configuration, user management, and system monitoring

***Analytical and Operational Skills:***

-   **Security Incident Workflow Design**: Developed end-to-end processes for incident detection, analysis, and response
-   **Multi-Platform Security Operations**: Coordinated security activities across integrated SIEM, SOAR, and case management platforms
-   **Threat Detection and Response**: Implemented detection rules and automated response mechanisms for common security threats
-   **Documentation and Standard Operating Procedures**: Created detailed technical documentation and operational procedures

### Tools Used

***Primary Security Platforms:***

-   **Wazuh**: Open-source SIEM platform for log analysis, intrusion detection, and compliance monitoring
-   **Shuffle**: Open source security orchestration, automation and response (SOAR) platform for workflow automation
-   **TheHive**: Scalable, open-source security incident response platform for case management and collaborative investigations

***Infrastructure and Supporting Technologies:***

-   **DigitalOcean**: Cloud service provider used to host virtual machines and manage the lab’s network infrastructure.
-   **Ubuntu Linux (20.04/22.04 LTS)**: Operating system for the Wazuh server, TheHive instance, and Client 2 VM, providing stability and compatibility with open-source SOC tools.
-   **Windows 10**: Operating system for Client 1 VM to simulate a typical end-user workstation in a SOC environment.
-   **DigitalOcean Network Firewall**: A cloud-based firewall used to control and restrict traffic to and from virtual machines, thereby enhancing network security.
-   **RESTful APIs**: Utilised for integration between Wazuh, TheHive, and Shuffle, enabling automated data exchange and workflow execution.
-   **Virtual Machines (VMs)**: Isolated environments used to simulate endpoints, threat activity, and SOC operations in a controlled setting.
-   **Draw.io**: Used to design network architecture diagrams, workflows, and automation logic for better visualisation and planning.

### System Specifications:

***Cloud-Based VMs (DigitalOcean)***

-   **Wazuh Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **TheHive Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **Client 2**: Ubuntu, 1 CPU, 4 GB RAM, 80 GB disk

***Local Infrastructure (Home Lab)***

-   **Client 1**: Windows 10, 2 CPUs, 4 GB RAM, 40 GB disk

---

## Part 2: Architecture and Data Flow

### Network Diagram

This diagram illustrates how the components of the SOC lab communicate:

**Figure 1: SOC Network Architecture**
![SOC Automation drawio (4)](https://github.com/user-attachments/assets/3b02cd01-7ac2-429c-811e-ca00e65ab7c2)

- Windows and Ubuntu clients send telemetry to the **Wazuh Manager**.
- Wazuh forwards relevant alerts to **Shuffle**.
- **Shuffle** performs enrichment and forwards cases to **TheHive**.
- **TheHive** allows analysts to investigate and track incidents.
- Internet connectivity is used for threat enrichment (e.g., VirusTotal) and alert notification via email.

### Automation Workflow

The following playbook describes the alert triage and response process:

**Figure 2: SOC Automation Playbook Logic**
![SOC drawio (2)](https://github.com/user-attachments/assets/083230f1-172f-43fc-a91a-6130894e3ecb)

***Workflow Summary***

1.  **Clients Sending Events**:
    -   Wazuh agents on client machines (Windows and Ubuntu) send logs and events to the **Wazuh Manager**.
2.  **Wazuh Manager**:
    -   Analyses events and generates alerts.
    -   Alerts are checked for severity (e.g., `Level >= 5` is considered for automated response).
3.  **Alert Routing via Shuffle**:
    -   If the alert level is high enough, it's forwarded to **Shuffle**.
    -   Shuffle checks the **Rule ID** associated with the alert and applies a specific action path.
4.  **Conditional Automation Paths** (based on Rule IDs):
    -   **Rule ID 100006**:
        -   Trigger VirusTotal enrichment.
        -   Create a case in **TheHive**.
        -   Send notification email.
    -   **Rule ID 100002**:
        -   Similar enrichment, TheHive alert, and email.
        -   Then asks: “Would you like to block the IP?”
            -   If **Yes** → Block IP, Delete File, and send notification.
            -   If **No** → Do nothing.
    -   **Rule ID 100003 / 100004**:
        -   Only virus enrichment + TheHive case.
        -   Ask if IP blocking and user disabling are needed.
            -   If **Yes** → Block IP, Disable User, Notify.
            -   If **No** → Do nothing.
5.  **Human Interaction Points**:
    -   Decisions like blocking an IP or disabling a user are interactive and can be escalated to an analyst.

***SOC Automation Playbook Table***

| **Step** | **Trigger Condition** | **Rule ID** | **Enrichment** | **Action(s)** |
| --- | --- | --- | --- | --- |
| 1 | Alert received from Wazuh | Any | None | Check if alert level is ≥ 5 |
| 2 | Alert level ≥ 5 | Any | None | Forward alert to Shuffle for processing |
| 3 | Match on specific rule | `100003`, `100004`| VirusTotal | Create TheHive case, send email notification, ask: "Block IP?" → If Yes → Block IP, send email notification |
| 4 | Match on specific rule | `100002` | VirusTotal | Create TheHive case, send email, ask: "Delete file?" → If Yes →, Delete File, send email notification |
| 5 | Match on specific rule | `100006` | VirusTotal | Create TheHive case, send email, ask: "Block IP & Disable User?", If Yes → Block IP and Disable User, send email notification |
| 6 | No rule match or alert level < 5 | Any | None | Do nothing |

***Response Action Mapping***

| **Decision Point** | **If Yes** | **If No** |
| --- | --- | --- |
| Delete File? (Rule `100002`) | Delete File → Notify via email | Do nothing |
| Block IP? (Rules `100003/100004`) | Block IP → Notify via email | Do nothing |
| Block IP & Disable User? (Rules `100006`) | Block IP → Disable User → Notify via email | Do nothing |


### Architecture Planning and Diagram Creation

**Objective**: Create a comprehensive visual representation of the SOC automation lab to understand data flow and identify required components.

**Overview**: Before building any infrastructure, it's crucial to map out the logical architecture of our Security Operations Centre (SOC) environment. This planning phase helps us visualise how different security tools will interact, understand the data flow between components, and ensure we have all the necessary pieces to create a functional automated SOC.

**Tools Used**:

-   **Draw.io** (now diagrams.net) - Free, web-based diagramming tool
-   Access: [https://app.diagrams.net/](https://app.diagrams.net/)

**Process**:

1.  **Access Draw.io**
    -   Navigate to the Draw.io website (free to use, no registration required)
    -   Choose to create a new diagram or work with existing templates
2.  **Design Considerations**
    -   Map out the logical flow of security data from endpoints to analysis
    -   Identify all required components for the SOC automation workflow
    -   Plan the integration points between different security tools
    -   Consider network topology and security boundaries
3.  **Key Components to Include**:
    -   **Client Endpoints**: Windows 10 (local) and Ubuntu (cloud) with Wazuh agents
    -   **Network Infrastructure**: Router and internet connectivity
    -   **SIEM Platform**: Wazuh Manager for log collection and analysis
    -   **SOAR Platform**: Shuffle for automation and orchestration
    -   **Case Management**: TheHive for incident tracking
    -   **SOC Analyst Workstation**: For manual investigation and response
    -   **Communication Flows**: Data paths between all components
4.  **Data Flow Mapping**:
    -   Event generation from endpoints
    -   Log forwarding to SIEM
    -   Alert generation and forwarding to SOAR
    -   IOC enrichment and case creation
    -   Notification and response workflows
    -   Closed-loop automation back to endpoints

**Benefits of This Approach**:

-   **Visual Clarity**: Provides a clear understanding of system architecture
-   **Planning Tool**: Helps identify potential integration challenges early
-   **Documentation**: Serves as a reference for implementation and troubleshooting
-   **Communication**: Enables adequate explanation of the project to stakeholders
-   **Scalability Planning**: Shows how the environment can be expanded in the future

**Outcome**: A comprehensive network diagram that serves as the blueprint for the entire SOC automation project, ensuring all components work together cohesively.

---

## Part 3: Infrastructure Deployment

In this phase, we build out the virtual environment that powers the SOC lab. It consists of:

- A Windows 10 client (local VM)
- An Ubuntu client (cloud VM)
- A Wazuh server (cloud VM)
- A TheHive server (cloud VM)

Both local and cloud-based deployment methods are demonstrated.

> _This step has been adapted from @MyDFIR’s original project, with added enhancements and modified configuration steps._

### Windows 10 Virtual Machine Deployment With Sysmon

> **Note:**
> In this project, I used a preconfigured **Windows 10 Pro template** hosted on **Proxmox** and cloned it to deploy the client machine quickly.
> If you're using **Proxmox**, you can skip the VirtualBox setup steps below and refer to the \[Proxmox screenshots\] for guidance.
> For general users, the following instructions detail how to deploy Windows 10 using **VirtualBox**.

#### Option A: VirtualBox (Cross-platform)

1.  **Install VirtualBox**

    -   Visit [virtualbox.org](https://www.virtualbox.org/) and download the installer for your OS.
    -   (Optional) Verify SHA-256 checksum using PowerShell:

```powershell
Get-FileHash .\VirtualBox-*.exe -Algorithm SHA256
```

**Figure 3: VirtualBox download**

![image](https://github.com/user-attachments/assets/d2f7013a-fbca-4d09-94ca-3f2ddea36f8e)<br>

2.  **Download Windows ISO**

    -   Use Microsoft’s [Media Creation Tool](https://www.microsoft.com/en-us/software-download/windows10)
    -   Select “Create installation media” → Choose **ISO file** → Save locally

**Figure 4: Download Media Creation Tool**

![image](https://github.com/user-attachments/assets/64437e1d-0643-45a9-bfed-fc55700a8f48)<br>

**Figure 5: ISO Creation Screen**

![image](https://github.com/user-attachments/assets/4d9c6c44-0bb2-4b06-9927-cf14facafaca)<br>

![image](https://github.com/user-attachments/assets/f4bcbc24-3d60-4798-887d-48f9f4940e02)<br>

![image](https://github.com/user-attachments/assets/36d58126-da55-4403-83fb-62eb718c8ac0)<br>

![image](https://github.com/user-attachments/assets/cd5162ba-40e1-482b-a970-f109bb36e297)<br>

![image](https://github.com/user-attachments/assets/54fa1f0d-0e5a-4948-97ed-4dec093d38c8)<br>

![image](https://github.com/user-attachments/assets/36934d57-ec43-4b14-93eb-c21f9028456f)<br>

3.  **Create Windows 10 VM in VirtualBox**

    -   VM Name: `Win10-Sysmon`
    -   RAM: 4GB, Disk: 50GB
    -   Load the Windows ISO during creation
    -   Check "Skip unattended installation" to do a manual setup

**Figure 6: VirtualBox VM Creation & Settings**

![image](https://github.com/user-attachments/assets/8d7be4d8-26f0-4679-9b14-ba2c8cb68c6f)<br>

4.  **Install Windows 10 OS**

There are numerous guides available online for this process.

5.  **Download and Install Sysmon**
    -   Download Sysmon from Microsoft Sysinternals

**Figure 7: Sysmon Download & Installation**

![Screenshot 2025-06-14 123115](https://github.com/user-attachments/assets/93e6de2c-63e7-4ddc-aaa6-e7efe236f134)<br>

Download the ZIP file and uncompress it.

![Screenshot 2025-06-14 123238](https://github.com/user-attachments/assets/78689806-ce78-47c1-a966-85aca8fad6e3)<br>

Our Windows 10 installation is 64-bit, so we will install that version of Sysmon. 

![Screenshot 2025-06-14 123256](https://github.com/user-attachments/assets/35013eb6-f707-4670-aa64-cc42da116f01)<br>

Navigate to the Sysmon binary location.

![Screenshot 2025-06-14 123608](https://github.com/user-attachments/assets/3491ff17-b83d-4578-82f2-c21c7d26a9fa)<br>

   -   Download the config from [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)

**Figure 8: Sysmon Download & Installation**

![Screenshot 2025-06-14 124020](https://github.com/user-attachments/assets/eac27bf6-71dc-4621-b09c-5ea5334a1d4e)<br>

![Screenshot 2025-06-14 124308](https://github.com/user-attachments/assets/ac00b60e-44fc-4125-b484-dfc7de1f9bbb)<br>

**Figure 9: Sysmon Configuration File**

![Screenshot 2025-06-14 124342](https://github.com/user-attachments/assets/c2a65b1a-b433-41df-aa98-fb3c51d68e3a)<br>

![Screenshot 2025-06-14 124416](https://github.com/user-attachments/assets/e8dae915-1516-4cd3-9422-2a0400fec3cc)<br>

Move the configuration file to the Sysmon main folder.

-   Open PowerShell as Admin:

**Figure 10: Sysmon Installation**

![Screenshot 2025-06-14 123401](https://github.com/user-attachments/assets/27ed729b-50d3-4921-9e46-349a51a8b006)<br>

Run PowerShell as Administrator.

![Screenshot 2025-06-14 124836](https://github.com/user-attachments/assets/96c04dbb-3a17-479b-baf7-0abf4490f707)<br>

Run the following command in PowerShell to start the installation of Sysmon.

```powershell
.\Sysmon64.exe -i sysmonconfig.xml
```

Or

```powershell
Sysmon64.exe -accepteula -i sysmonconfig.xml
```

**Figure 11: Sysmon Installation Process**

![Screenshot 2025-06-14 124907](https://github.com/user-attachments/assets/7860d7b1-f8e2-42aa-8b87-1dc740d12d16)<br>

Accept the EULA.

![Screenshot 2025-06-14 124931](https://github.com/user-attachments/assets/029fa419-ae00-4fe1-bad8-9c88878cba33)<br>


Check Windows Event Viewer → `Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`

**Figure 12: Sysmon running in Event Viewer & Services**

![Screenshot 2025-06-14 125022](https://github.com/user-attachments/assets/a1a3d8a4-1407-45b4-8f40-ca0a2daeef57)<br>

![Screenshot 2025-06-14 125255](https://github.com/user-attachments/assets/0081d901-fb04-4b5f-b154-a6f5f4656e60)<br>

### Option B: Using Proxmox (Project-specific setup)

> 💡 If you're following my exact setup:

-   I cloned a preconfigured Windows 10 Pro template from my **Proxmox** environment.

**Figure 13: Proxmox Templates**
![Screenshot 2025-06-14 111515 PNG](https://github.com/user-attachments/assets/267150d9-cb84-4c7b-bb2c-d5da1adc21f1)<br>

**Figure 14: Clone Windows 10 template**

![Screenshot 2025-06-14 111544](https://github.com/user-attachments/assets/6750d31e-e675-425c-a2bf-0a4e7dbd4628)<br>

**Figure 15: Label the New Windows 10 VM**

![Screenshot 2025-06-14 111756](https://github.com/user-attachments/assets/1da8cde7-376b-42fc-a753-9a01456ff9c3)<br>

![Screenshot 2025-06-14 111829](https://github.com/user-attachments/assets/3ae4a0d8-ee6c-4c64-9469-2381d329a43b)<br>

**Figure 16: Start the Windows 10 VM**

![Screenshot 2025-06-14 112344](https://github.com/user-attachments/assets/4063fb9d-9966-463d-9246-83b3871d406c)<br>

-   After cloning, I installed Sysmon and applied the configuration as described above.

### Deploy Wazuh Server (SIEM)

Deployed on DigitalOcean (Ubuntu 22.04 LTS)

1.  **Create Droplet**

- 2 CPUs, 8 GB RAM, 160 GB SSD
- Hostname: `wazuh`
- Use strong root password
  
-   Image: Ubuntu 22.04 LTS

**Figure 17: Droplet**
![Screenshot 2025-06-14 132308](https://github.com/user-attachments/assets/ac690233-57c0-40a8-ac09-4243ffa2fa3a)<br>

**Figure 18: Select Location & OS**

![Screenshot 2025-06-14 132426](https://github.com/user-attachments/assets/3884dfe6-5608-4a8d-a1e4-e9e0f32ac9c0)<br>

-   Plan: 2 vCPU / 8GB RAM minimum (Premium)

**Figure 19: Select Shared CPU, Premium Intel, 2 CPUs, 160 GB SSDs & 8 GB memory**
![Screenshot 2025-06-14 132609](https://github.com/user-attachments/assets/1fb8aa81-7ef7-442b-8148-7ab3eb3b74fe)<br>

-   Hostname: `wazuh` and Set root password

**Figure 20: Pick Strong Password & Select a Hostname**

![Screenshot 2025-06-14 132913](https://github.com/user-attachments/assets/4c6d6007-7da4-40d0-99da-b23febb76b9d)<br>

**Figure 21: Click on Create Droplet**

![Screenshot 2025-06-14 132936](https://github.com/user-attachments/assets/70283a0d-9de8-4ab7-aedd-6f40073619bc)<br>

2.  **Set Up Cloud Firewall**
    -   Go to **Networking > Firewalls**
  
**Figure 22: Public and Private IPs**

![Screenshot 2025-06-14 133413](https://github.com/user-attachments/assets/c11871bb-951d-4513-86fa-9fa503535c06)<br>

Take note of both the public and private IP addresses for future reference.

**Figure 23: Create Firewall**

![Screenshot 2025-06-14 133428](https://github.com/user-attachments/assets/0fb68643-0186-4c56-bc8f-5615274de017)<br>

Go to Networking → Firewalls.

Allow only your IP for all TCP and UDP ports.

>-   Protocol: TCP and UDP
>-   Port Range: All ports
>-   Sources: Your IP address only

**Figure 24: Allow All TCP and UDP Connections to All Ports for your IP Address**

![Screenshot 2025-06-14 133858](https://github.com/user-attachments/assets/ff3b3635-1adb-4e49-88b6-78135dbacfe0)<br>

**Figure 25: Click on Create Firewall**

![Screenshot 2025-06-14 134204](https://github.com/user-attachments/assets/f0639036-be63-41f0-bbaa-b4a205d9a232)<br>

**Figure 26: Your Private IP**

![Screenshot 2025-06-14 133706](https://github.com/user-attachments/assets/ea4440dc-25f7-4d60-a889-cf945189b282)<br>

To find out what your Public IP is, there are plenty of sites that can help you_

-   Apply a firewall to `wazuh` droplet.

**Figure 27: Firewall Rule**

![Screenshot 2025-06-14 134610](https://github.com/user-attachments/assets/c95c20c1-eff4-47b3-b370-cd4684d9b11e)<br>

**Figure 28: Add Droplet to Role**

![Screenshot 2025-06-14 134635](https://github.com/user-attachments/assets/6db021c4-5877-4d3b-a982-3445136b55cb)<br>

### 2.3 Deploy TheHive Server (Cloud - DigitalOcean)

1.  **Create Droplet**

    -   Same settings as Wazuh

**Figure 29: TheHive Droplet**

![Screenshot 2025-06-14 144407](https://github.com/user-attachments/assets/e0f70066-28e8-4b77-ac94-1cc2969dff04)<br>

Click on create → Droplets and then select location and OS.

**Figure 30: Droplet Specs**

![Screenshot 2025-06-14 144503](https://github.com/user-attachments/assets/82fcfc50-b6c7-452a-8b04-c6372f32dfde)<br>

Select Shared CPU, Premium Intel, 2 CPUs, 160 GB SSDs and 8 GB memory.

-   Hostname: `TheHive`

**Figure 31: Password & Hostname**

![Screenshot 2025-06-14 144702](https://github.com/user-attachments/assets/b16958e4-518d-43ac-b0e3-b5dde72649f6)<br>

Pick a strong password and select a Hostname.

**Figure 32: Create Droplet**

![Screenshot 2025-06-14 144717](https://github.com/user-attachments/assets/395c6877-5b29-4cff-a7b5-926f950ff2cc)<br>

-   Attach to the same firewall

**Figure 33: Firewall Rule**

![Screenshot 2025-06-14 144900](https://github.com/user-attachments/assets/9e7576d4-77dc-42fb-8468-4d8b816048e7)<br>

Add TheHive droplet to Wazuh-Firewall rule.

**Figure 34: Adding Droplet to Rule**

![Screenshot 2025-06-14 144912](https://github.com/user-attachments/assets/0e302266-3a04-4ba9-be56-1209ae7fd5ba)<br>

Select TheHive droplet and add it to the rule.

**Figure 35: Droplet Added**

![Screenshot 2025-06-14 145001](https://github.com/user-attachments/assets/2fb0c9f2-6493-4cf4-be89-5a936f025471)<br>







### Important Firewall Notes

**Security Warning**: Some rules allow "All IPv4, All IPv6" for testing purposes only
-   Production Environment: Always restrict sources to specific IP ranges
-   Temporary Rules: The transcript mentions creating temporary rules for testing that should be removed after lab completion
-   Port 55000: Opened temporarily for Wazuh API access during Shuffle integration
-   Port 9000: Opened temporarily for The Hive access during testing






# Installing and Configuring Wazuh & TheHive Servers

## Installion

### Wazuh Server Setup

-   SSH into the droplet or use the Launch Droplet Console and run:
  
**Figure 36: From Droplet Console**

![Screenshot 2025-06-14 135355](https://github.com/user-attachments/assets/8e5cbf54-5a36-4838-a7eb-03edafefb438)<br>

**Figure 37: SSH from PowerShell**

![image](https://github.com/user-attachments/assets/3357e801-2d20-4833-924e-cf57d77f9892)<br>

```powershell
ssh root@YOUR_WAZUH_PUBLIC_IP
```

-   Before Wazuh installation, it is always recommended to update and upgrade the OS

**Figure 38: Updating**

![Screenshot 2025-06-14 141051](https://github.com/user-attachments/assets/f8c31e1c-2f68-4713-89ba-18716fb72052)<br>

```bash
apt-get update && apt-get upgrade
```

-   Install Wazuh

**Figure 39: Wazuh Installation**

![Screenshot 2025-06-14 141505](https://github.com/user-attachments/assets/3236032c-a888-45f3-937b-0a2a0da1bf39)<br>

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
chmod +x wazuh-install.sh
sudo ./wazuh-install.sh -a
```

**Figure 40: Installation Process**

![Screenshot 2025-06-14 141600](https://github.com/user-attachments/assets/c52e532e-babe-4691-83ac-8c934e941051)<br>

-   Note the default admin credentials

**Figure 41: Wazuh credentials**

![Screenshot 2025-06-14 142343](https://github.com/user-attachments/assets/1454e5d4-4815-4926-8c9b-f2f3d4f236aa)<br>

```bash
tar -xvf wazuh-install-files.tar
cat wazuh-install-files/wazuh-passwords.txt
```
Save admin password and API user credentials and keep these credentials secure for dashboard access.

### Verify Installation

-   Access the dashboard via `https://YOUR_WAZUH_IP`

**Figure 42: Wazuh Login Page**

![Screenshot 2025-06-14 142425](https://github.com/user-attachments/assets/b7e58ddb-e9fe-4ed4-9e1f-1ccca2dd4b74)<br>

-   Use `admin / <generated password>`

**Figure 42: Wazuh Dashboard**

![Screenshot 2025-06-14 144001](https://github.com/user-attachments/assets/80c9c512-fe14-4eda-9acc-5badf1401c49)<br>


### The Hive Server Setup

SSH into The Hive Droplet.

```BASH
ssh root@YOUR_HIVE_PUBLIC_IP
```

**Figure 43: SSH from PowerShell**

![Screenshot 2025-06-14 145256](https://github.com/user-attachments/assets/a0e48a89-51cf-4577-a2a4-cb715b981dff)<br>

Update System
```bash
bashapt update && apt upgrade -y
```

**Figure 44: Updating OS**

![Screenshot 2025-06-14 145346](https://github.com/user-attachments/assets/361b2f42-5fea-4a44-8c79-9766233f54af)<br>

![Screenshot 2025-06-14 145506](https://github.com/user-attachments/assets/40ec915a-df8f-4896-9c0d-5a13b5f03d19)<br>

![Screenshot 2025-06-14 145613](https://github.com/user-attachments/assets/6bc4089e-9899-4896-a510-c7392f368450)<br>


### Install Dependencies

-   SSH into the VM, install:
-   Java (OpenJDK)
-   Cassandra
-   Elasticsearch
-   TheHive


### Install Java

**Figure 45: Installing Java (OpenJDK)**

![Screenshot 2025-06-14 150227](https://github.com/user-attachments/assets/7c455fa2-c788-45fa-8da6-cd1fc915e923)<br>

```bash
# Install Java
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

![Screenshot 2025-06-14 150318](https://github.com/user-attachments/assets/0ca330e9-c151-4d1a-949a-2d6bfc3f48dc)<br>

### Install Cassandra

**Figure 46: Installing Cassandra**

![Screenshot 2025-06-14 150436](https://github.com/user-attachments/assets/2a366b7c-1135-420a-a3ff-c1b76fdab329)<br>


```bash
# Install Cassandra
wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```

![Screenshot 2025-06-14 150448](https://github.com/user-attachments/assets/9563385d-d36d-4a94-88f3-756ca6a0ea4a)<br>

### Install Elasticsearch

**Figure 47: Installing Elasticsearch**

![Screenshot 2025-06-14 150623](https://github.com/user-attachments/assets/b688bc28-774f-4124-956a-afbf780a4a00)<br>

```bash
# Install Elasticsearch
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

![Screenshot 2025-06-14 150632](https://github.com/user-attachments/assets/6fa50e78-fd62-4e3b-94e5-fe12e7834b93)<br>
_Click on OK_

### Install TheHive

**Figure 48: Installing TheHive**

![Screenshot 2025-06-14 230305](https://github.com/user-attachments/assets/47101d45-6434-488d-882f-4f659cb6a55b)<br>


```bash
# Install The Hive
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive
```

***Important:*** Do NOT start services yet - configuration must be done first

## Configuration

### Configuring Cassandra

**Figure 49: Configuring Cassandra**

![Screenshot 2025-06-14 154158](https://github.com/user-attachments/assets/dcd0058a-248c-4ab6-b5bc-ec3a072f7c8b)<br>


```bash
nano /etc/cassandra/cassandra.yaml
```

-   Change `cluster_name` to "mydfir"
-   Change `listen_address` to your Hive public IP
-   Change `rpc_address` to your Hive public IP
-   Change seed address in `seed_provider` to your Hive public IP

**Figure 50: Change Cluster Name**

![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>

Change `cluster_name` to `'mylab'` and the `listen_address` to your TheHive public IP.

**Figure 51: Change the rpc_address**

![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>

Change the `rpc_address` to your TheHive public IP.

**Figure 52: Change the seedaddress seeds**

![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>

Change the seedaddress `seeds` to your TheHive public IP.

**Figure 53: Stop Service**

![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>

Stop Cassandra service.

```bash
systemctl stop cassandra.service
```

**Figure 54: Configuring**

![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>

We need to remove any Cassandra data when setting up Cassandra for the first time and no critical data exists.

```bash
rm -rf /var/lib/cassandra/*
```

**Figure 55: Start Service**

![Screenshot 2025-06-14 155159](https://github.com/user-attachments/assets/851fed1b-c039-46a2-9774-c422eacd5881)<br>

Start and check the status of the Cassandra service.

```bash
systemctl start cassandra.service
```

```bash
systemctl status cassandra.service
```

### Configuring Elasticsearch

**Figure 56: Editing YML**

![Screenshot 2025-06-14 154158](https://github.com/user-attachments/assets/dcd0058a-248c-4ab6-b5bc-ec3a072f7c8b)<br>
_Configuring Elasticsearch_

```bash
nano /etc/elasticsearch/elasticsearch.yml
```

-   Uncomment and change `cluster.name` to "thehive"
-   Uncomment and change `node.name` to "node-1"
-   Uncomment and change `network.host` to your Hive public IP
-   Uncomment `http.port: 9200`
-   Uncomment and modify `cluster.initial_master_nodes: ["node-1"]`

**Figure 56: cluster.name & node.name**

![image](https://github.com/user-attachments/assets/861fb4de-2f1b-48f0-aca9-0f66bb5f5c9e)<br>

Remove the comment # and change `cluster.name` to thehive, remove the comment # to `node.name`.

**Figure 56: network.host, http.port & cluster.initial_nodes**

![Screenshot 2025-06-14 160110](https://github.com/user-attachments/assets/aa9ad9fa-e501-4b63-b226-5df52aad8d6a)<br>

Remove the comment # and change `network.host` to TheHive public IP, remove the comment # for `http.port`, remove the comment # for `cluster.initial_nodes` and delete `"node-2"`.

**Figure 56: Start Service**

![Screenshot 2025-06-14 160340](https://github.com/user-attachments/assets/09cf0c0a-45b3-49e6-a638-192944cee988)<br>
_Start, enable and check the status of the Elasticsearch service_

```bash
systemctl start elasticsearch.service
```

```bash
systemctl enable elasticsearch.service
```

```bash
systemctl status elasticsearch.service
```

### Configuring TheHive

**Figure 57: Change Ownership**

![Screenshot 2025-06-14 230722](https://github.com/user-attachments/assets/2b477265-57db-4330-8687-6b383f38584d)<br>

Check TheHive directory and change ownership, `-R` to change user and group.

```bash
ls -la /opt/thp/
```

```bash
chown -R thehive:thehive /opt/thp
```

```bash
nano /etc/thehive/application.conf
```

-   Change database hostname to your Hive public IP
-   Change Cassandra cluster name to "mydfir"
-   Change Elasticsearch hostname to your Hive public IP
-   Change `application.baseUrl` to your Hive public IP

**Figure 58: Configuring TheHive**

![Screenshot 2025-06-14 230950](https://github.com/user-attachments/assets/e34759bb-6e1b-4ad3-8bd0-16f1bfe29320)<br>


**Figure 59: hostname & application.baseUrl**

![image](https://github.com/user-attachments/assets/c9dbd10d-4ecb-4ddc-b7c6-699ad68f75d5)<br>
_Change `hostname` and `application.baseUrl` to TheHive public IP, give a name to the `cluster-name`, in this case `'mylab'`_

**Figure 60: Start Service**

![Screenshot 2025-06-14 231441](https://github.com/user-attachments/assets/04802257-9b15-46af-9389-864f9e255e7c)<br>

Start, enable and check the status of the TheHive service

```bash
systemctl start thehive.service
```

```bash
systemctl enable thehive.service
```

```bash
systemctl status thehive.service
```

### Run TheHive

**Access The Hive**

-   Navigate to `http://YOUR_HIVE_IP:9000`
-   Login: `admin@thehive.local`
-   Password: `secret`

**Figure 61: TheHive Login Page**

![Screenshot 2025-06-14 231655](https://github.com/user-attachments/assets/d3c0a7e1-6299-403e-bc11-8687b3754092)<br>

-   TheHive will fail on the first login attempt
-   Check Elasticsearch status

**Figure 62: Activation has Failed**

![Screenshot 2025-06-14 232242](https://github.com/user-attachments/assets/8140e2ad-07c1-41de-90fa-840a6c90297b)<br>

> Activation has failed, we need to create a jvm.options file to address the heap memory issue on ElasticSearch.

**Figure 63: jvm.options**

![Screenshot 2025-06-14 232242](https://github.com/user-attachments/assets/a229bd64-fcd0-4593-af51-f5314fcef669)<br>

Navigate to `/etc/elasticsearch/jvm. options.d/` and create the file jvm.options.

```bash
nano /etc/elasticsearch/jvm.options.d/jvm.options
```

**Figure 64: Heap Memory**

![Screenshot 2025-06-14 232323](https://github.com/user-attachments/assets/6e0f2081-0426-4a25-9e4a-c2b06aa67f15)<br>

Under heap memory allocation, copy and paste the below setup and save

```bash
-Dlog4j2. formatMsgNoLookups=true
-Xms2g
-Xmx2g
```

Then restart Elasticsearch.

```bash
systemctl stop elasticsearch.service
```

```bash
systemctl start elasticsearch.service
```

Try to access The Hive again.

-   Navigate to `http://YOUR_HIVE_IP:9000`
-   Login: `admin@thehive.local`
-   Password: `secret`

**Note:**
>The `jvm.options` file serves as a critical configuration point for Java applications, particularly for controlling the Java Virtual Machine's (JVM) behaviour and resource allocation. Within this file, lines such as `-    Dlog4j2.formatMsgNoLookups=true`, `-Xms2g`, and `-Xmx2g` instruct the JVM on how to operate. The `-Dlog4j2.formatMsgNoLookups=true` option is a vital security measure, specifically disabling dangerous message lookup features in the Log4j2 logging framework to mitigate the Log4Shell vulnerability and prevent remote code execution. Concurrently, `-Xms2g` and `-Xmx2g` are used to manage the JVM's memory heap: precisely `-Xms2g` sets the initial heap size to 2 gigabytes, ensuring the application has sufficient memory from the moment it starts, while `-Xmx2g` defines the maximum heap size also as 2 gigabytes, preventing the application from consuming excessive system resources. Together, these settings optimise application performance by preventing dynamic heap resizing and enhancing security, making them fundamental for stable and secure Java deployments.

**Figure 65: TheHive Dashboard**

![Screenshot 2025-06-14 232510](https://github.com/user-attachments/assets/2787b884-13f5-4855-8b70-3f37fa9e7c86)<br>

### Configure Wazuh

**Access Wazuh Dashboard**

-   Navigate to `https://YOUR_WAZUH_IP`
-   Use admin credentials from password file

**Figure 66: Wazuh Dashboard**

![image](https://github.com/user-attachments/assets/030479e0-1cfb-47b3-b3fe-bb10184a4b98)<br>

**Find API Credentials (if lost)**

```bash
tar -xvf wazuh-install.tar
cd wazuh-install-files
cat wazuh-passwords.txt
```

### Add Windows Agent

-   On Wazuh dashboard: `Agents > Add Agent > Platform: Windows`

**Figure 67: Adding agent**

![Screenshot 2025-06-14 234041](https://github.com/user-attachments/assets/be97cb46-6cf7-4e31-ae19-c5e3730c0c4d)<br>

Use Wazuh public IP as the manager address.

**Figure 68: Selecting the Platform**

![image](https://github.com/user-attachments/assets/99ad8269-6b94-43c3-8e2c-8da8103a7a05)<br>

Copy the generated install command.

**Figure 69: Copy PowerShell Command**

![Screenshot 2025-06-14 234309](https://github.com/user-attachments/assets/9777b528-0ca0-4aab-8848-2fc0dec7640f)<br>

### Install Agent on Windows Client

Open **PowerShell as Administrator**.

**Figure 70: Admin PowerShell**

![image](https://github.com/user-attachments/assets/2b38dab5-21c0-447a-81f4-09a3ff7f0c40)<br>

Paste and run the command.

**Figure 71: Installing**

![image](https://github.com/user-attachments/assets/5d5ab1e6-872b-4989-b439-d325ca7fee9b)<br>
 
Then start the Wazuh client service.

**Figure 72: Starting Wazuh Service**

![image](https://github.com/user-attachments/assets/9cb8f941-e637-4d9c-9b8d-c47db5c03ae6)<br>

```bash
net start WazuhSvc
```

**Figure 72: Service Running**
![Screenshot 2025-06-14 235441](https://github.com/user-attachments/assets/107913db-02ea-4cc5-b662-90d8e09d2834)<br>

### Verify Agent Connectivity

-   Wazuh dashboard → Agents tab → Confirm agent shows as "Active"
-   Navigate to **Security Events** to confirm telemetry ingestion

**Figure 73: Agent Active**

![image](https://github.com/user-attachments/assets/980f0354-686e-46d3-b2b6-b2cfca7970d8)<br>

![Screenshot 2025-06-14 235719](https://github.com/user-attachments/assets/65c68e8d-1f68-418c-a2fb-b0d5642b5e5f)<br>

**Figure 74: Events Records**

![Screenshot 2025-06-14 235756](https://github.com/user-attachments/assets/711b48e6-ac6e-4309-8dd2-4abda5b177b9)<br>

By completing this step, you now have:

-   TheHive fully configured and accessible via port 9000
-   Cassandra and Elasticsearch integrated
-   Wazuh ingesting Sysmon telemetry from Windows 10 client
-   All components verified and communicating

## Generate Telemetry and Create Alerts

> _Objective: Ingest telemetry from the Windows 10 client using Sysmon, configure Wazuh to receive and log all events, and create a custom detection rule for Mimikatz based on the original binary name._

## Configure Windows Client for Sysmon Ingestion

### Modify Agent Config (Windows 10 Client)

-   Navigate to:

`C:\Program Files (x86)\ossec-agent`

**Figure 75: Wazuh Agent Configuration**

![Screenshot 2025-06-15 000304](https://github.com/user-attachments/assets/7b5d46c9-8c8e-4346-a88f-cf43150efd84)<br>

Backup the file (e.g., rename to `ossec-backup.conf`).

```cmd
copy ossec.conf as ossec-backup.conf
```

**Figure 76: Backup ossec.conf**

![Screenshot 2025-06-15 000613](https://github.com/user-attachments/assets/18e3ee10-cc94-48f5-8037-c1d220aaf43b)<br>

Backup `ossec.conf` is recommended in case we need to restore the system.

Open `ossec.conf` with admin rights via Notepad.

**Figure 77: ossec.conf**

![Screenshot 2025-06-15 000505](https://github.com/user-attachments/assets/3393db7f-b8c4-4150-9620-ce791df69660)<br>

Find `<log_analysis>` section, remove application, security, and system log entries and add Sysmon configuration.

**Figure 78: Parameters**

![Screenshot 2025-06-15 000803](https://github.com/user-attachments/assets/331282fb-df1d-43fa-a8a2-566680eba680)<br>

-   Under `<localfile>` entries, add:

**Figure 78: Sysmon parameter**

![Screenshot 2025-06-15 001037](https://github.com/user-attachments/assets/384b20c0-f2ad-4f74-9312-b0c581c0a6a0)<br>
_Sysmon parameter, for this project, we will just be using the Sysmon events, but we can ingest other logs like PowerShell, System, etc_

```xml
<localfile>
   <location>Microsoft-Windows-Sysmon/Operational</location>
   <log_format>eventchannel</log_format>
</localfile>
```

To find out the channel name go to.

**Figure 79: Channel Name**

![image](https://github.com/user-attachments/assets/5d55dfec-21b1-4088-b65d-7db0045b5072)<br>

-   Optional: Remove other logs like Application, Security and System for focused testing.

```xml
<localfile>
  <location>Application</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>Security</location>
  <log_format>eventchannel</log_format>
</localfile>

<localfile>
  <location>System</location>
  <log_format>eventchannel</log_format>
</localfile>
```

### Restart Wazuh Agent Service

-   Open Services → Restart **Wazuh Agent**

**Figure 80: Restart Service**

![image](https://github.com/user-attachments/assets/dcc400f0-547b-49f3-ab75-fdee1d8c20b0)<br>

-   Or run in PowerShell (Admin):

```bash
net stop wazuhsvc
net start wazuhsvc
```

-   Check that we are getting Sysmon telemetry on Wazuh

**Figure 81: Telemetry**

![Screenshot 2025-06-15 001855](https://github.com/user-attachments/assets/6493b5b8-7de4-4d3c-b930-ef7ed26312e0)<br>

## Configure Wazuh Manager for Full Logging

### Edit Wazuh Manager Config (Ubuntu Server)

SSH on the Wazuh server, backup Manager Configuration `ossec.conf` and then edit the `ossec.conf` file.

```bash
sudo cp /var/ossec/etc/ossec.conf ~/ossec-backup.conf
```

```bash
nano /var/ossec/etc/ossec.conf
```

Enable Full Logging by change the following:

-   Scroll to bottom and find `<alerts>` section
-   Change `<log_all>no</log_all>` to `<log_all>yes</log_all>`
-   Change `<log_all_json>no</log_all_json>` to `<log_all_json>yes</log_all_json>`

**Figure 82: Initial Logs Settings**

![image](https://github.com/user-attachments/assets/01cf85aa-8c2c-4851-897b-aa3c5ed2aa7c)<br>

**Figure 83: Full Logs Settings**

![Screenshot 2025-06-15 005127](https://github.com/user-attachments/assets/f8330c30-bfaf-461c-85a6-5c359423e431)<br>

Checking if Wazuh is ingesting JSON logs.

``bash
ls /var/ossec/logs/archives/
``

**Figure 84: Archives logs**

![Screenshot 2025-06-15 005350](https://github.com/user-attachments/assets/b10bd74e-a9bd-4d04-a73d-e27bf5c76082)<br>

### Enable Filebeat to Ingest Archive Logs

```bash
sudo nano /etc/filebeat/filebeat.yml`
````

**Figure 84: Edit filebeat**

![image](https://github.com/user-attachments/assets/7824ce97-520c-4ddb-94b2-fc884277461b)<br>

-   Find `archives.enabled: false`
-   Change to `archives.enabled: true`

**Figure 85: Filebeat Archives Enabled**

![Screenshot 2025-06-15 005628](https://github.com/user-attachments/assets/4e7fb64a-a5f4-4bd8-8410-ad4c0bc19cef)<br>

Restart the Filebeat and Wazuh services.

```bash
systemctl restart wazuh-manager
systemctl restart filebeat
```

### Create Archives Index in Wazuh

-   Go to Wazuh dashboard
-   Click hamburger menu (≡) → Stack Management
-   Click "Index Patterns"

**Figure 86: Stack Management**

![Screenshot 2025-06-15 010100](https://github.com/user-attachments/assets/3b30c83e-17ec-4691-842d-0092be280f40)<br>

**Figure 87: Index Patterns**

![Screenshot 2025-06-15 010225](https://github.com/user-attachments/assets/98a4851b-3bf2-4d5a-a351-bf53c7d6eace)<br>

-   Click "Create index pattern"

**Figure 88: Index Pattern**

![image](https://github.com/user-attachments/assets/a5509989-6e59-4cb6-a79b-894332c6d3a9)<br>


-   Index pattern name: `wazuh-archives-*`

**Figure 89: Index Pattern Name**

![Screenshot 2025-06-15 010500](https://github.com/user-attachments/assets/9d7c5317-c6aa-4692-b004-c56348625ce2)<br>


-   Click "Next step"
-   Time field: Select `@timestamp`
-   Click "Create index pattern"

**Figure 90: Time Field**

![Screenshot 2025-06-15 010537](https://github.com/user-attachments/assets/e8ff09e7-2f92-4065-b4c0-22a2a31e8ce5)<br>

**Figure 91: New Index Pattern**

![Screenshot 2025-06-15 011019](https://github.com/user-attachments/assets/cb3068c7-947b-4f90-adc3-563242d2c52c)<br>

**Access Archives**

-   Click hamburger menu (≡) → Discover
-   Select index dropdown → Choose `wazuh-archives-*`


## Download and Test Mimikatz

### Disable Windows Defender

-   Type "Windows Security" in search
-   Open Windows Security
-   Go to "Virus & threat protection"
-   Click "Manage settings" under "Virus & threat protection settings"
-   Click on disbale

**Figure 92: Access Windows Defender**

![image](https://github.com/user-attachments/assets/63d3f875-5a95-400c-bb84-dd8946e69830)<br>

**Figure 93: Virus & Therat Protection**

![Screenshot 2025-06-15 002112](https://github.com/user-attachments/assets/d833c107-423e-4f0d-9ec7-e7fc43b14fef)<br>

**Figure 94: Disable Virus & Threat Protection**

![Screenshot 2025-06-15 002142](https://github.com/user-attachments/assets/b9aa9360-93e6-41f6-ad3b-9c92776f343e)<br>

**Figure 94: Disable**
![image](https://github.com/user-attachments/assets/7465952f-5cab-4fa3-8518-e79441b85883)

### Download and Execute Mimikatz*

-   Scroll down to "Exclusions"
-   Click "Add or remove exclusions"
-   Click "Add an exclusion" → "Folder"
-   Select your Downloads folder
-   Click "Select Folder"

**Figure 95: Add or Remove Exclusions**

![Screenshot 2025-06-15 002204](https://github.com/user-attachments/assets/bdfd0222-8e77-464c-ac54-5bf923fb1c5e)<br>

**Figure 96: Add an Exclusion**

![image](https://github.com/user-attachments/assets/a1389b62-29d2-46e2-8e3f-ffcca497ffb4)<br>

**Figure 97: Downloads folder**

![image](https://github.com/user-attachments/assets/36592c33-82fb-4460-b45f-b82171edb662)<br>

### Disable Chrome Protection (if needed)

Disable browsing protection for downloading Mimikatz; otherwise, the browser will stop the transfer.

-   Open Chrome → Settings
-   Privacy and security → Security
-   Scroll down → "No protection (not recommended)"
-   Turn off protection temporarily

**Figure 98: Disable Browsing Protection**

![Screenshot 2025-06-15 002657](https://github.com/user-attachments/assets/8f64d7f0-d43e-4da6-b0a8-c3b1c7c2c0bd)<br>

#### Download Mimikatz

-   Go to official [Mimikatz GitHub repository](github.com/gentilkiwi/mimikatz/releases)
-   Download the latest release
-   Extract to Downloads folder
-   Browser may warn about malicious file - proceed anyway

**Figure 99: Mimikatz ZIP**

![image](https://github.com/user-attachments/assets/1fac7816-29ee-4bbc-89f0-7598d1d29969)<br>

**Figure 99: Extracting Mimikatz**

![Screenshot 2025-06-15 002759](https://github.com/user-attachments/assets/13bd51cd-16b8-409a-a4d9-55c2d050c16b)<br>

![Screenshot 2025-06-15 002858](https://github.com/user-attachments/assets/31c232b1-3c44-4833-b845-c18b40be5357)<br>

### Run Mimikatz

powershell

```powershell
# Open PowerShell as Administrator
cd Downloads\mimikatz_trunk\x64
.\mimikatz.exe
```

**Figure 100: Mimikatz running in PowerShell**

![Screenshot 2025-06-15 003018](https://github.com/user-attachments/assets/1bd72f3b-a126-4214-8a48-50f837e9dc03)<br>

![Screenshot 2025-06-15 011356](https://github.com/user-attachments/assets/195b1991-e7e5-41dd-97a4-4ab60655cd0d)<br>

### Verify Events in Wazuh

Go to Wazuh dashboard → Discover
Select wazuh-archives-* index
Search for: mimikatz
Look for Sysmon Event ID 1 (Process Creation)
Expand an event and find: data.win.eventdata.originalFileName

**Figure 101: Wazuh Mimikatz Log**

![Screenshot 2025-06-15 011837](https://github.com/user-attachments/assets/eb7af982-76d5-4bb7-bcfc-d79bc380c97a)<br>

**Figure 102: Wazuh Mimikatz Log Expanded**

![Screenshot 2025-06-15 011952](https://github.com/user-attachments/assets/d6559200-f702-4f29-a2fe-1321bc640425)<br>

Wazuh Mimikatz logs on the web interface, we will use the original filename to build our rule as it will always be the same even if someone changes the name of the file.

#### Verify in Event Viewer

Open Windows Event Viewer
Go to Applications and Services Logs → Microsoft → Windows → Sysmon → Operational
Look for Event ID 1
Should see mimikatz.exe in the events

**Figure 103: Events 1**

![Screenshot 2025-06-15 011449](https://github.com/user-attachments/assets/98d704b9-75ed-4320-b999-ba84ce51de96)<br>

**Figure 104: Mimikazt event being logged by Sysmon**

![Screenshot 2025-06-15 011601](https://github.com/user-attachments/assets/76389baf-a2a8-47b3-bd0d-1fbf23ce2545)<br>

### Check Archive Files on Manager

```bash
ls /var/ossec/logs/archives/
cat /var/ossec/logs/archives/archives.json | grep -i mimikatz
```

**Figure 105: Wazuh Mimikatz Logs**

![Screenshot 2025-06-15 011744](https://github.com/user-attachments/assets/736d78b6-d582-459d-b07e-fa102ed99737)<br>
_Wazuh Mimikatz logs_


## Create Custom Detection Rule for Mimikatz

### Access Wazuh Rules

-   Go to Management → Rules
-   Click "Manage rule files"
-   Search for "sysmon" to see existing rules
-   Click info icon on "0800-sysmon\_id\_1.xml" to see examples

**Figure 106: Accessing Rules**

![Screenshot 2025-06-15 012137](https://github.com/user-attachments/assets/d3b19cd2-bea4-45c6-b69a-cbe99ee518ce)<br>

**Figure 107: Manage Rules Files**

![Screenshot 2025-06-15 012322](https://github.com/user-attachments/assets/ae2c5d77-e608-4a27-af42-fded4e8d3735)<br>

As we are interested in Sysmon event ID 1, we will have a look at the XML file.

**Figure 108: Sysmon ID 1 XML Rule**
![Screenshot 2025-06-15 012409](https://github.com/user-attachments/assets/22c2ab4f-6abc-460c-b9c2-80b2ac05592f)<br>

Then, copy one of the rules to use as an example for our custom rule.

**Figure 109: Copy Rule**

![Screenshot 2025-06-15 012720](https://github.com/user-attachments/assets/515e73ca-1b82-4a1d-926a-68ae99961614)<br>

### Create Custom Rule

-   Go to "Custom rules"
-   Click the pencil icon to edit local rules
-   Below the existing rule (after the `</rule>` tag), add:

**Figure 110: Click on Custom Rules**

![Screenshot 2025-06-15 012802](https://github.com/user-attachments/assets/23019364-fdc8-40a5-a195-5f2d01ff5323)<br>
_Click on custom rules_

**Figure 111: Edit Custom Rules**

![Screenshot 2025-06-15 012926](https://github.com/user-attachments/assets/28d5f8e0-744a-49fe-94a6-2df43030a9ba)<br>
_Edit custom rules_

-   Paste and modify the rule we copied from the Sysmon XML file:

**Figure 112: Rule 100002**

![image](https://github.com/user-attachments/assets/bd69c55f-b7dc-43cf-a672-ec1db8be8817)<br>

Note: custom rules start from `100000`.

```xml
<rule id="100002" level="15">
   <if_sid>80001</if_sid>
   <field name="win.eventdata.originalFileName" type="pcre2">(?i)mimikatz\.exe</field>
   <description>Mimikatz Usage Detected</description>
   <mitre>
      <id>T1003</id>
   </mitre>
</rule>
```

-   **Important**: Use spaces for indentation, not tabs
-   Match the indentation of the rule above
-   Field name is case-sensitive: `win.eventdata.originalFileName`

### Save and Restart Manager

-   Click "Save"
-   Click "Confirm" to restart manager

**Figure 113: Restart From Dashboard**

![image](https://github.com/user-attachments/assets/92b02a75-37b5-497a-8e64-b92e4aa8e0ab)<br>
_Restart from dashboard_

**Figure 114: Confirm**

![image](https://github.com/user-attachments/assets/a98527d2-15f8-4f15-a144-79a2a2f05fcf)<br>
_Confirm_

-   or:

```bash
sudo systemctl restart wazuh-manager
```

## Final Test: Bypass Filename and Trigger Alert

-   Rename `mimikatz.exe` to a decoy name (e.g., `ztakimim.exe`)
-   Run the renamed executable.

**Figure 115: Renamed File**

![Screenshot 2025-06-15 013827](https://github.com/user-attachments/assets/2fc5c5a0-5f73-44dc-b20b-a886dd0660d0)<br>

-   Execute it via PowerShell:

```powershell
.\ztakimim.exe`
```

-   Go to Security Events in Wazuh dashboard
-   Search for "mimikatz"
-   Should see alert with description "Mimikatz usage detected"
-   Note: Detection works despite renamed file because it uses `originalFileName`

**Figure 115: Mimikatz Alert Triggered**

![Screenshot 2025-06-15 015503](https://github.com/user-attachments/assets/1f7a7f51-ff37-479b-a4d8-d60db5a4e7db)<br>

**Figure 115: Original Name Detected**

![Screenshot 2025-06-15 195607](https://github.com/user-attachments/assets/3be90d65-af26-42ec-9b56-04ebda4d9141)<br>

We have now:

-   Configured Wazuh Agent to ingest **Sysmon logs**
-   Tuned **Wazuh Manager** to log and archive everything
-   Created a **custom detection rule** for Mimikatz that resists binary renaming
-   Verified that alerts are triggered based on **original file name**

# SOAR Integration with Shuffle

> _Objective: Finalise SOC automation by integrating Shuffle (SOAR) with Wazuh (SIEM) and TheHive (case management). Automate alert parsing, threat enrichment, email notifications, and responsive actions._

## Connect Shuffle with Wazuh via Webhook

### Setup Shuffle Account

Create Account
-   Go to shuffler.io
-   Create a free account
-   Log in to the dashboard

### Create Workflow

-   Click "Workflows"
-   Click "+" to create new workflow
-   Name: `SOC Automation Project`
-   Description: "MyDFIR project!"
-   Use case: Select any
-   Click "Done"

**Figure 116: SOC Automation Project Workflow**

![image](https://github.com/user-attachments/assets/6d03c4e1-7e96-4dc8-85c1-c3f30fc44258)<br>

![Screenshot 2025-06-15 200132](https://github.com/user-attachments/assets/aba61c09-8e6a-479b-b65d-4dca18f3aca6)<br>

### Set up Webhook Trigger in Shuffle

-   Click "Triggers" tab in workflow
-   Drag "Webhook" app onto canvas
-   Click on webhook
-   Name: "wazuh-alerts"
-   Copy the webhook URI (starts with [https://shuffler.io](https://shuffler.io))

**Figure 117: Shuffle Webhook Configuration**

![image](https://github.com/user-attachments/assets/afcbc490-9919-408c-a2c2-330ca21efc2e)<br>

**Figure 118: Copy Webhook URI**

![Screenshot 2025-06-15 200521](https://github.com/user-attachments/assets/5dfc9c80-b722-4d7e-99db-14457139f14e)<br>

### Configure Wazuh Integration

-   Edit Wazuh Manager config (`/var/ossec/etc/ossec.conf`):

```bash
nano /var/ossec/etc/ossec.conf
```

-   Find the `</global>` closing tag
-   Add AFTER the `</global>` tag:

**Figure 119: ossec.conf**

![image](https://github.com/user-attachments/assets/e80654e4-ad75-4e6c-a26c-edcb88836ab3)<br>

- Edit `<integration>` 

**Figure 120: Suffle/Wazuh Integration**

![Screenshot 2025-06-15 201729](https://github.com/user-attachments/assets/8e3a3ae0-eac6-4da4-a389-d2f8d0b094c6)<br>
__

**Figure 121: Targeting events by rule ID 100002**

![Screenshot 2025-06-15 202234](https://github.com/user-attachments/assets/d93d953e-2392-4dc4-9387-e559e7e30851)<br>

```xml
<integration>
   <name>shuffle</name>
   <hook_url>https://webhook-url-from-shuffle </hook_url>
   <rule_id>100002</rule_id>
   <alert_format>json</alert_format>
</integration>
```

-   **Critical**: Ensure the hook\_url is NOT highlighted in purple
-   If purple, press space to separate it
-   Use correct rule\_id: `100002`

### Restart Wazuh Manager

```bash
sudo systemctl restart wazuh-manager
```

```bash
sudo systemctl status wazuh-manager
```

**Figure 122: Restart Service**

![Screenshot 2025-06-15 202333](https://github.com/user-attachments/assets/748e3a90-39e9-4b43-921e-ef4f66f8d8c5)<br>

### Test Integration

-   In Shuffle, click webhook → Click "Start"

**Figure 123: Webhook Started**

![image](https://github.com/user-attachments/assets/8af5ada1-7081-4882-aa37-7213f50a316e)<br>

-   Run Mimikatz on Windows client:

**Figure 124: Mimikatz Running**

![image](https://github.com/user-attachments/assets/f4515d64-b7bc-4d46-8b5c-728c7100907a)<br>

powershell

```powershell
.\youareawesome.exe
exit
```

-   Click "Executions" tab at the bottom
-   Should see execution with Wazuh alert data

**Figure 125: Wazuh Webhook Capturing Traffic**

![Screenshot 2025-06-15 202858](https://github.com/user-attachments/assets/f9a5fa36-2764-41b3-8900-b87a6970f78c)<br>

**Figure 126: Wazuh, Mimkatz Rule ID 100002 Triggered**

![Screenshot 2025-06-15 202658](https://github.com/user-attachments/assets/a68105cb-3fac-4ea7-bcb1-296b2e98fb82)<br>

**Figure 127: Rule ID 100002 Captured**

![Screenshot 2025-06-15 203111](https://github.com/user-attachments/assets/81977675-f5e4-47bc-b4ea-a6c300ddedb0)<br>



## Build SOAR Workflow

### Hash Extraction with Regex

-   Click "Change me" icon
-   Change from "Repeat back to me" to "Regex capture group"

**Figure 128: Regex Capture Group**

![image](https://github.com/user-attachments/assets/bd696309-9796-4f3d-8779-65c6791940ee)<br>

-   Rename to "SHA256 Regex"
-   Input data: Click "+" → Execution argument → Find hash field
-   **Get Regex from ChatGPT:**
    -   Prompt: "create a regex to parse the sha256 value of the hash"
    -   Copy the regex pattern provided
-   Paste regex in Shuffle
-   Save workflow and test

Input data: 
```bash
$exec.text.win.eventdata.hashes
```
**Figure 129: Regex**

![image](https://github.com/user-attachments/assets/6774e963-5c6c-4258-915b-ebf7f7f23e6d)<br>

Regex:
```regex
(?:^|,)SHA256=([A-Fa-f0-9]{64})(?=,|$)
```

**Figure 129: Regex Parsing Setup in Shuffle**

![image](https://github.com/user-attachments/assets/4539ec30-fd77-465b-ac71-5c1ab99421e7)<br>

## Enrich Alert with VirusTotal

### Configure VirusTotal App in Shuffle

-   Click "Apps" tab → Search "VirusTotal"
-   Click VirusTotal to activate
-   Drag VirusTotal app to canvas
-   Label the App and change Action: `Get hash report`

**Figure 130: Drag VirusTotal App**

![image](https://github.com/user-attachments/assets/4269d3c6-2a75-49d4-9307-492727d46345)<br>

**Figure 130: Label**

![image](https://github.com/user-attachments/assets/cc4f241d-36bd-41bb-86f8-bccee3a62673)<br>

-   Connect SHA256 Regex to VirusTotal
-   **Get VirusTotal API Key:**
    -   Sign up at virustotal.com
    -   Go to your profile → API Key
    -   Copy API key

**Figure 131: Get the ID**

![image](https://github.com/user-attachments/assets/8ddb381a-9809-4482-9ba1-69390fb07f57)<br>

-   Configure VirusTotal app:
    -   Click "Authenticate" button
    -   Paste API key → Submit
    -   Action: "Get a hash report"
    -   ID field: Select SHA256 regex output (list)

**Figure 132: VirusTotal App Setup**

![image](https://github.com/user-attachments/assets/0dc959aa-cbc9-4277-9c1d-9c741dbd5ff0)<br>

-   **Note**: If getting 404 errors, the VirusTotal app may need updating
-   Fork the app and change URL from `.../report` to `.../[ID]`
-   Re-run the last log to test the VirusTotal App

**Figure 133: Re-running Alert**

![image](https://github.com/user-attachments/assets/8b4919e6-8c6f-4b0b-907d-e2cfc3542953)<br>

**Figure 134: VirusTotal Hash Report Setup**

![Screenshot 2025-06-15 210311](https://github.com/user-attachments/assets/d4ecbc37-ac19-4c43-8d28-2c1396c932ef)<br>

## The Hive Integration

### Setup The Hive Users:

-   Login to The Hive web interface
-   Click "+" to create new organization: "mylab"

**Figure 135: Add Organisation**

![image](https://github.com/user-attachments/assets/911cb58b-bd97-4ad2-b04c-8ff9bce4378c)<br>

**Figure 136: Create New Organisation**

-   Organisation: `MyLab`
-   Description: `SOC Automation Project`

![image](https://github.com/user-attachments/assets/ea14bf4a-4caf-449e-9dc4-44148713038e)<br>

-   Create analyst user: `maylab@test.com`

**Figure 137: Add Users**

![Screenshot 2025-06-15 211123](https://github.com/user-attachments/assets/b6e11f7a-4d6b-498a-8c25-9644687955ee)<br>

![image](https://github.com/user-attachments/assets/93490510-1f3f-4d57-ace6-1f81527bbc61)<br>

>-   Type: `Normal`
>-   Login: `mylab@test.com`
>-   Name: `mylab`
>-   Profile: `Analyst`

-   Create service account: `shuffle@test.com` (type: service)

**Figure 138: Service Account**

![Screenshot 2025-06-15 211414](https://github.com/user-attachments/assets/3f422943-1089-4b90-8db7-51ec4d3b39db)

>-   Type: `Service`
>-   Login: `shuffle@test.com`
>-   Name: `SOAR`
>-   Profile: `Analyst`

-   Set password for the analyst account

**Figure 139: Preview Normal-Analyst Account**

![Screenshot 2025-06-15 211450](https://github.com/user-attachments/assets/a5485a03-d46e-4b01-b025-1fde7d81ac30)<br>

**Figure 140: Set Password for Normal-Analyst Account**

![Screenshot 2025-06-15 211530](https://github.com/user-attachments/assets/59b93849-71e0-4f2f-91c9-ec8d11e7371f)<br>

-   Generate API key for service account

**Figure 141: Preview Service-Analyst Account**

![image](https://github.com/user-attachments/assets/95de12b6-5fac-4ceb-89c8-c80802eba1dd)<br>

**Figure 142: TheHive API Key**

![Screenshot 2025-06-15 211751](https://github.com/user-attachments/assets/9823acc1-4492-4263-8911-a75fe4c31701)<br>


**Configure The Hive App:**

-   Search and add "The Hive" app in Shuffle
-   Drag to canvas and connect VirusTotal to The Hive

**Figure 143: TheHive App**

![image](https://github.com/user-attachments/assets/9bca6b72-17e9-41e5-80f3-9e289cca502c)<br>

**Figure 144: TheHive Label**

![image](https://github.com/user-attachments/assets/43ef2114-c971-449b-b634-30e7534fede2)<br>

-   Authenticate with API key and URL: `http://YOUR_HIVE_IP:9000`

**Figure 145: TheHive Authentication**

![image](https://github.com/user-attachments/assets/58a37e2e-4472-431c-96d3-0bff80955a5f)<br>
_apiKey, the API key can be copied from TheHive service account_

**Figure 146: TheHive Public IP**

![Screenshot 2025-06-15 212307](https://github.com/user-attachments/assets/5616050a-f1cf-4d1e-b900-b06b9291f009)<br>
_TheHive Public IP and port `http://<TheHive public IP>:9000`, click on Submit_

-   Action: "Create alert"
-   Configure fields:
    -   Title: `$exec.title`
    -   Tags: `["T1003"]` (in array format)
    -   Summary:
    ```bash
    Mimikatz detected on host: $exec.text.win.system.computer with the processor ID: $exec.text.win.system.processID and the command line: $exec.text.win.eventdata.commandLine, VirusTotal Reputation:   $virustotal.#.body.data.attributes.reputation//$virustotal.#.body.data.attributes.sandbox_verdicts.Zenbox.confidence`
    ```
    Severity: `2`

    **Figure 147: TheHive Alert Field Mapping**
    
    ![Screenshot 2025-06-15 215814](https://github.com/user-attachments/assets/cd46b131-1847-46ba-aa9a-0010840eabf8)<br>

    -   Type: `Internal`
    -   TLP: `2`
    -   Status: `New`
    -   Sourceref: `100002`
    -   Source: `Wazuh`
    -   Pap: `2`
    -   Flag: `false`
    -   Description: 
    ```bash
    Mimikatz Detected on Host: $exec.text.win.system.computer from user: $exec.text.win.eventdata.user
    ```

    **Figure 148: TheHive Alert Field Mapping**
    
    ![image](https://github.com/user-attachments/assets/37079a2e-fd0e-44c3-ab47-137125a2e5bc)<br>
    _TheHive alert field mapping `Type`, `Tip`, `Status`, `Sourceref`, `Source`, `Pap`, `Flag`, `Description`_

### Expose TheHive API Port

-   Open DigitalOcean firewall port `9000` for inbound traffic

**Figure 149: Open firewall port `9000`**

![image](https://github.com/user-attachments/assets/76b2bc1f-5450-4c8f-83b0-3be47bf7f7c6)<br>

![Screenshot 2025-06-15 220202](https://github.com/user-attachments/assets/e1502738-1d67-4483-a862-108b7d73cda1)<br>


### Rerunning a 100002 Alert 

> **Note:** I faced an issue with the ThHive API After troubleshooting, I found out the API was passing the arguments in the wrong format. I managed to fix the API body; see below the default setup and the changes I made. If you are facing similar issues, this may help you resolve them. I noted that Steven from @MyDFIR has a different problem with another API.

**Figure 150: Default API Request**

![Screenshot 2025-06-16 093947](https://github.com/user-attachments/assets/12363a95-4b28-4e23-b6db-4ab0dd2053ac)<br>

I found out that `description`, `flag`, `pap`, `tags` and `tip` were not passing the values in the correct format, so I changed them.

**Figure 151: New TheHive Body Format**

![image](https://github.com/user-attachments/assets/f2d82347-897a-48cc-9ffe-27c342b55d58)<br>

```json
{
  "description": "{{ '''${description}''' | replace: '\n', '\\r\\n' }}",
  "externallink": "${externallink}",
  "flag": ${flag},
  "pap": ${pap},
  "severity": "${severity}",
  "source": "${source}",
  "sourceRef": "${sourceref}",
  "status": "${status}",
  "summary": "${summary}",
  "tags": "${tags}",
  "title": "${title}",
  "tlp": ${tlp},
  "type": "${type}"
}
```

**Figure 152: Submit the API changes**

![image](https://github.com/user-attachments/assets/e6b8efa5-7bde-4ab0-94a4-8f3953872f02)<br>

**Figure 153: Save API**

![image](https://github.com/user-attachments/assets/37258b31-98a9-4567-8272-d5c75300b849)<br>

-   Testing TheHive API changes again by re-running the alert

**Figure 153: Error 400**

![Screenshot 2025-06-16 094414](https://github.com/user-attachments/assets/1bae997b-8b59-4cba-891a-43abb19d7e9c)<br>

Error 400, we need to change the way we pass the Tags value.

Tags: `T1003`

**Figure 154: New Tags Format**

![Screenshot 2025-06-16 094636](https://github.com/user-attachments/assets/36e6a046-a6bf-4376-887a-e35d5ac5198d)<br>

### Rerunning the 100002 Alert With the API Changes

**Figure 154: Status 200**

![Screenshot 2025-06-16 094833](https://github.com/user-attachments/assets/57af4177-b940-454f-9119-b95e68d84742)<br>

Status 200!!, the alert has been sent to TheHive.

### TheHive Alert

**Figure 155: Alert**

![Screenshot 2025-06-16 094853](https://github.com/user-attachments/assets/c859951d-1714-4949-9849-6048e5faf947)<br>

The alert has been created on TheHive Dashboard.

**Figure 156: TheHive Ddashboard**

![Screenshot 2025-06-16 094949](https://github.com/user-attachments/assets/c3512820-f815-4bb2-8bae-68156bbf477b)<br>

All values have been passed to TheHive dashboard.

We have now completed our flow for executing Mimikatz on the Windows client's Downloads folder. So far, our workflow should look like the one below.

**Figure 157: Shuffle Workflow Overview**

![Screenshot 2025-06-16 095202](https://github.com/user-attachments/assets/a5a56ece-6731-4730-90d9-7515a5edf1c7)<br>




### Email Notification

-   Add "Email" app

**Figure 158: Email App**

![image](https://github.com/user-attachments/assets/523f5963-cd89-413e-89ce-11d383558c08)<br>
_Drag the Email App_

-   Connect VirusTotal to Email
-   Configure:
    -   Recipient: Your email address (can use a disposable email from SquareX or [temp-mail.org](temp-mail.org))
    -   Subject: "Mimikatz detected!"
    -   Body: Include execution arguments for time, title, and computer name

-   Name: `Email Notification`
-   Recipients: `email@dispoableemail.com`   
-   Subject: `Mimikatz Detected!`
-   Body: (You can use my template to create yours)
```bash
Time: $exec.text.win.eventdata.utcTime
Title: $exec.titleComputer: $exec.text.win.system.computer
User: $exec.text.win.eventdata.user
Command Line: $exec.text.win.eventdata.commandLine
File Original Name: $exec.text.win.eventdata.originalFileName
Spawned From: $exec.text.win.eventdata.parentImage
```

**Figure 159: Temp Email**

![Screenshot 2025-06-16 100135](https://github.com/user-attachments/assets/d6a93ba3-96c9-4c81-a5c3-9f209ec38656)<br>

**Figure 160: Email App Setup**

![image](https://github.com/user-attachments/assets/66394e83-9469-463c-b872-a3c291478e24)<br>

### Test Complete Workflow

-   Save workflow
-   Run mimikatz on Windows client
-   Verify execution in Shuffle
-   Check The Hive for new alert
-   Check email for notification

**Figure 161: Email Sent Successfully**

![Screenshot 2025-06-16 101015](https://github.com/user-attachments/assets/f73177da-c252-4e46-b19b-3e9cb2a86b74)<br>

**Figure 162: Email Output**

![Screenshot 2025-06-16 101419](https://github.com/user-attachments/assets/58098a94-61b0-4a43-9704-ccb5a281e613)<br>






# Advanced: Response Actions (Ubuntu Machine)

## Create Ubuntu Test Machine

-   Create new Digital Ocean droplet
-   Ubuntu 20.04, minimum specs (1GB RAM, 25GB storage)
-   **Important**: Create firewall rule allowing ALL inbound traffic
-   Install Wazuh agent on Ubuntu machine

**Figure 163: Create Droplet**

![image](https://github.com/user-attachments/assets/3ea15ac7-4bd6-4dc8-91e8-586c5b009730)<br>

**Figure 164: Select Options**

![Screenshot 2025-06-16 102353](https://github.com/user-attachments/assets/5af8ddc7-3cd3-408e-84b2-78473d3591f5)<br>

-   Hostname: `mylab-ubuntu`

**Figure 165: Password**

![Screenshot 2025-06-16 102610](https://github.com/user-attachments/assets/ee066439-6a2c-4ee8-b4a3-66ee481344cf)<br>

**Figure 166: Public IP**

![Screenshot 2025-06-16 103015](https://github.com/user-attachments/assets/2e96fbe1-6776-49be-a70b-514d6ebaac05)<br>

Create a new firewall rule set to accept all TCP connections in all ports.

**Figure 167: New Firewall Rule **
  
![Screenshot 2025-06-16 103657](https://github.com/user-attachments/assets/eb1b0992-47c3-4331-a273-c54033f07eac)<br>

Add the newly created Ubuntu client to that firewall rule.

**Figure 168: Add Ubuntu Client**

![Screenshot 2025-06-16 103810](https://github.com/user-attachments/assets/b21ab24a-0126-465f-a783-9ba8c5c9b16c)<br>

## Add Ubuntu Client Agent

-   On Wazuh dashboard: `Agents > Add Agent > Platform: Linux`

You should follow similar instructions to the Windows agent, but this time you can select Linux DEB amd64 and Wazuh Server public IP.

**Figure 169: Ubuntu Agent**

![Screenshot 2025-06-16 105827](https://github.com/user-attachments/assets/7b80f617-2b3e-445d-8076-7bb01b23f99e)

Copy the generated install command to install the Wazuh agent on the Ubuntu client.

**Figure 170: Command**

![Screenshot 2025-06-16 104618](https://github.com/user-attachments/assets/1c34d012-2357-4740-8e9a-98cda1230645)<br>

## Install on Ubuntu Client

SSH into the Ubuntu client.

**Figure 171: SSH**

![Screenshot 2025-06-16 104821](https://github.com/user-attachments/assets/4f0d7177-2fd2-4450-a23d-c8fe2cd46777)<br>

Update and upgrade the Linux client `apt update && apt upgrade`.

**Figure 172: Update**

![Screenshot 2025-06-16 104905](https://github.com/user-attachments/assets/4526a640-5c82-4d69-b43a-bd7b672100a5)<br>

**Figure 173: Click Ok**

![Screenshot 2025-06-16 105048](https://github.com/user-attachments/assets/cadd7a1b-cc3a-409d-912c-e3d35e6dc383)<br>

**Figure 174: Click Ok**

![Screenshot 2025-06-16 105149](https://github.com/user-attachments/assets/49aa2a13-2d46-43c2-9710-480412181d54)<br>

**Figure 175: Click Ok**

![Screenshot 2025-06-16 105211](https://github.com/user-attachments/assets/e7d737c3-750a-44fa-8c06-170993e446f1)<br>

Paste the command copied from the Wazuh web interface and execute it.

**Figure 176: Shell Command**

![Screenshot 2025-06-16 105701](https://github.com/user-attachments/assets/3040b797-7447-49a5-a786-2f8fd06a4c74)<br>

Then enable and start the Wazuh service.

**Figure 177: Start Service**

![image](https://github.com/user-attachments/assets/9cb8f941-e637-4d9c-9b8d-c47db5c03ae6)<br>

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Verify Agent Connectivity

Check on Wazuh to ensure the Ubuntu client has been added. Navigate to the Wazuh dashboard → Agents tab → and confirm the agent shows as "Active".

**Figure 178: Ubuntu Client on Wazuh**

![Screenshot 2025-06-16 114234](https://github.com/user-attachments/assets/baa542dc-3e0c-4fca-a0cf-a8a7124825af)<br>

-   Navigate to **Security Events** to confirm telemetry ingestion

**Figure 179: Events Being Recorded**

![image](https://github.com/user-attachments/assets/5738895d-b3a9-42cb-94bf-eff9cdd03453)<br>

### Expose Wazuh API Port

Open DigitalOcean firewall port `55000` for inbound traffic.

**Figure 180: Port 55000 Accepting Traffic**

![image](https://github.com/user-attachments/assets/bab20adb-1b80-4345-b3d1-0511d51326ce)
_Port 55000 accepting all incoming traffic_

### Add API Authentication to Shuffle

-   Add "HTTP" app for API authentication
-   Rename to "Get API"

**Figure 181: Drag HTTP App**

![Screenshot 2025-06-16 114607](https://github.com/user-attachments/assets/8bb203f5-2466-454e-8262-87d5fab67011)

-   Action: "Curl"
-   Configure curl command:

```
curl -u wazuh-wui:WAZUH_API_PASSWORD -k -X GET "https://WAZUH_IP:55000/security/user/authenticate"
```

**Figure 182: HTTP Request to Get Wazuh Token**

![Screenshot 2025-06-16 115247](https://github.com/user-attachments/assets/e4bab1af-dd42-49fe-972f-c5b4b78673c4)

## Configure Active Response in Wazuh

bash

```bash
nano /var/ossec/etc/ossec.conf
```

**Figure 183: Command**

![image](https://github.com/user-attachments/assets/5c3981b2-8b1e-42e5-b103-ad2a93d726eb)

-   Scroll to the bottom, find the active response section
-   Add after existing commands:

xml

```xml
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <level>5</level>
  <timeout>no</timeout>
</active-response>
```

**Figure 184: Command**

![image](https://github.com/user-attachments/assets/d2ef9e78-7612-4eb7-a274-08c6562f7d72)

**Figure 185: Active Response**

![image](https://github.com/user-attachments/assets/d288fc7f-0585-49e5-8fe1-90594e378e63)

Change Webhook to accept alerts by Level and not by Rule ID. We will set the level to 5, so all rules five and above will be passed to Shuffle.

**Figure 186: Changing Shuffle Integration**

![image](https://github.com/user-attachments/assets/45586ed9-df9b-4de4-b234-ba7314e61ef4)

### Test Active Response Manually 

Let's test how active response works manually. To do that, we will navigate to the following directory.

```bash
cd /var/ossec/bin/
```

We are interested in the `agent_control`, let's run it and see what available options we have `./agent_control`.

**Figure 187: Agent Control**

![Screenshot 2025-06-16 131502](https://github.com/user-attachments/assets/7cc0ef34-f363-43b1-a6e9-b4e167948ea6)

Use `-L` to list what available responses are activated. 

**Figure 188: firewall-drop**

![image](https://github.com/user-attachments/assets/7294ef06-d1a7-4e09-9498-f12eb749c223)

We can confirm `firewall-drop0` is activated. The `0` is the TimeOut and is appended to the command.

```bash
# Check available responses
/var/ossec/bin/agent_control -L
```

Let's test by blocking a known public IP `8.8.8.8` on our Ubuntu client.

**Figure 189: Ping**

![Screenshot 2025-06-16 132537](https://github.com/user-attachments/assets/b523a861-78d2-4b13-aa53-5c13f1bc2273)

Now run the `./agent_control` command on the Wazuh server to block IP `8.8.8.8` on the Ubuntu client.

-   -b block IP address `8.8.8.8`
-   -f active response command `firewall-drop0`
-   -u client ID  `002` Ubuntu machine

```bash
# Test blocking Google DNS
/var/ossec/bin/agent_control -b 8.8.8.8 -f firewall-drop0 -u 002

# Check if it worked (on Ubuntu machine)
ping 8.8.8.8  # Should fail
iptables -L   # Should show drop rule
```

**Figure 189: Client ID**

![image](https://github.com/user-attachments/assets/f7366735-3db4-4044-80d5-fbe00cfa8beb)

**Figure 189: Command Running**

![image](https://github.com/user-attachments/assets/338a23b5-2ada-4a11-bca9-2630a9be2206)

Verify that the IP address was successfully blocked. On the Ubuntu client, `iptables -- list`.

**Figure 190: IPTables - IP 8.8.8.8 Blocked**

![image](https://github.com/user-attachments/assets/3ae116af-9e52-4435-bdc5-84919b9837cb)

Take a look at the Active Response logs to see if the active response was successful.

```bash
cd /var/ossec/logs/
```
```bash
ls
```

```bash
cat active-responses. log
```

**Figure 191: Active Response Logs**

![Screenshot 2025-06-16 133440](https://github.com/user-attachments/assets/67c75b3b-f39a-4ffb-b868-f1ec1c249070)

## Project Modifications

Before we proceed with the project proposed by @MyDFIR, I decided to take it a step further and add some extra features. He suggests blocking IP addresses based on ID 5710, which is SSH failed login attempts using a non-existent user. However, I decided to add failed authentication attempts for known users, ID 5760, which can send us alerts of brute force on root or any specific existing account, and also send the alert to TheHive. So the aim is the following.

**Figure 192: Modified Project Workflow**

![Screenshot 2025-06-17 131746](https://github.com/user-attachments/assets/5d7ce409-4328-44b0-b3b0-82193311367a)

Now that we have identified the parameters to be passed to the Wazuh App, let's add a VirusTotal App to enrich the data collected and determine whether the source IP of the attack is blacklisted on the portal.

### Add a VirusTotal App for IP Enrichment

**Figure 192: VirusTotal App**

![image](https://github.com/user-attachments/assets/2fb80e57-81b0-4a1d-96aa-a9d71db86b6b)

### Add TheHive App for Notifications

**Figure 193: TheHive App**

![image](https://github.com/user-attachments/assets/516f3295-f705-41c2-9be9-a2e3c209046d)

### Add User Input App for Manual Responses

**Figure 193: User Input App**

![image](https://github.com/user-attachments/assets/f2b645d9-8711-4ed1-9c2c-dcbcc4781d45)

### Add Wazuh Tools

Add a Shuffle Tool App; we will use this to access branches for specific conditions. I could not find an alternative approach for this (change it to Execute Python).

**Figure 194: Wazuh Tools**

![image](https://github.com/user-attachments/assets/dd54e239-1947-412f-8dee-eed5eb62fd1f)

### Add Wazuh App

**Figure 195: Wazuh App**

![Screenshot 2025-06-16 115551](https://github.com/user-attachments/assets/d47f9962-94ab-4127-966e-5646120ca702)

### Connect All Apps

**Figure 196: Wazuh App**

![Screenshot 2025-06-17 131746](https://github.com/user-attachments/assets/6e4b087f-514b-43a3-a191-af77e47f5bda)

### Wazuh Custom Rules.

We create two rules: one to capture non-existent account attempts (ID 100003, targeting 5710 events) and the other for existing accounts (ID 100004, targeting 5760 events). We do this to reduce the number of API calls.

The rule will only be triggered if the attempt fails three times within 60 seconds. You can use the XML below and make any necessary changes as needed.

```xml
  <rule id="100003" level="5" frequency="3" timeframe="60">
    <if_matched_sid>5710</if_matched_sid>
    <same_source_ip />
    <description>Multiple SSH login attempts using non-existent usernames</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
  
  <rule id="100004" level="5" frequency="3" timeframe="60">
    <if_matched_sid>5760</if_matched_sid>
    <same_source_ip />
    <description>Multiple SSH login attempts using root username</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
```

**Figure 197: Wazuh Custom Rule**

![image](https://github.com/user-attachments/assets/4e836125-b661-4a00-876a-923cd0e2d98e)

## Conditions 

### Apps for Conditions

**Figure 198: Conditions**

![image](https://github.com/user-attachments/assets/5feea2ad-8a7a-4933-8b42-1078aa5b17c1)

### Branch Condition Rule 100003

**Figure 199: Condition 100003**

![image](https://github.com/user-attachments/assets/ddf76564-aae3-4790-94ee-547246fd6518)

We need to let Rule ID `100003` pass through.

**Figure 200: Condition 100003**

![image](https://github.com/user-attachments/assets/802071ad-23d4-4d86-8004-bda01ca91cb2)

### Python App Setup

I will use this App as a bridge to allow me to get an extra branch for the other condition. It should be a better way, but for the purpose os the project, this works just fine.

**Figure 201: Phyton App**

![image](https://github.com/user-attachments/assets/83669112-fea9-4186-ac83-6efecd129ff7)

### Branch Condition Rule 100004

**Figure 202: Condition 100004**

![image](https://github.com/user-attachments/assets/70f6b187-31a9-4a04-bd9d-deccb53d0b04)

We need to let Rule ID `100004` pass through.

**Figure 203: Condition 100004**

![image](https://github.com/user-attachments/assets/27d8c4f6-096a-4186-a755-721509c41a38)


### Setup VirusTotal App

We first give the app a name, then pass the arguments.

Find Actions: `Get an IP address report`
IP: `$exec.all_fields.data.srcip`, this is the source IP we want to block.

**Figure 204: VT App**

![image](https://github.com/user-attachments/assets/20503af2-ec9f-434e-a96f-a53953a10cc9)

### Setup TheHive App

We first give the app a name, then pass the arguments.

Find Actions: `Create alert`
Body:
```xml
{
  "description": "${description}",
  "externallink": "${externallink}",
  "flag": "${flag}",
  "pap": "${pap}",
  "severity": "${severity}",
  "source": "${source}",
  "sourceRef": "${sourceref}",
  "status": "${status}",
  "summary": "${summary}",
  "tags": ["${tags}"],
  "title": "${title}",
  "tlp": "${tlp}",
  "type": "${type}"
}
```
Title: `$exec.title`
Tags: `T1110`

**Figure 205: TheHive Arguments**

![image](https://github.com/user-attachments/assets/aad4f583-69cf-4f6e-92d0-0a145e372534)

Summary:
```bash
$exec.title were detected on Host: $exec.all_fields.predecoder.hostname from source IP: $exec.all_fields.data.srcip on $utc_time.message, the IP was idetified coming from $virustotal_ip_block.body.data.attributes.country with $virustotal_ip_block.body.data.attributes.last_analysis_stats.malicious highlighting as malicius. Check emails to block the IP if needed.
```
Severity: `2`

**Figure 206: TheHive Arguments**

![image](https://github.com/user-attachments/assets/c23b92ac-a463-4dfa-b23c-62889bd42270)

### Setup User Input for Approval

-   Add "User Input" app between VirusTotal and Wazuh
-   Configure:
    -   Email: Your email address
    -   Question: "Would you like to block this source IP: \[source\_ip\]?"
    -   Include source IP from execution arguments

We first give the app a name, then pass the arguments.

Email: `disposable email`, Email delivery to analyst
Input options: `Email`
Information: With the help of ChatGPT I got this template.
```markdown
<h3>🚨 Suspicious IP Activity Detected</h3>

  The IP <strong>$exec.all_fields.data.srcip</strong> made multiple login attempts from an unknown location.
  A <strong>VirusTotal</strong> scan returned:

<table style="border-collapse:collapse; margin:0;">
  <tr><th align="left">Metric</th><th align="left">Result</th></tr>
  <tr><td>Last Analysis Date</td><td>$utc_time.message</td></tr>
  <tr><td>Malicious</td><td>$virustotal_ip_block.body.data.attributes.last_analysis_stats.malicious</td></tr>
  <tr><td>Suspicious</td><td>$virustotal_ip_block.body.data.attributes.last_analysis_stats.suspicious</td></tr>
  <tr><td>Undetected</td><td>$virustotal_ip_block.body.data.attributes.last_analysis_stats.undetected</td></tr>
  <tr><td>Harmless</td><td>$virustotal_ip_block.body.data.attributes.last_analysis_stats.harmless</td></tr>
  <tr><td>Country</td><td>$virustotal_ip_block.body.data.attributes.country</td></tr>
  <tr><td>VirusTotal Report</td>
      <td><a href="$virustotal_ip_block.body.data.links.self">$virustotal_ip_block.body.data.links.self</a></td></tr>
</table>

<strong>Block source IP $exec.all_fields.data.srcip?</strong>
```
**Figure 207: User Input Arguments**

![image](https://github.com/user-attachments/assets/a4f9ddd4-d3ec-442e-95a2-9375bd625722)

**Figure 208: User Input Arguments**

![image](https://github.com/user-attachments/assets/8be04994-5452-40b4-94d8-045cc3a6bc92)


### Add Wazuh App

**Figure 209: User Wazuh App**

![Screenshot 2025-06-16 115551](https://github.com/user-attachments/assets/d47f9962-94ab-4127-966e-5646120ca702)


### Setup Wazuh Response Action

-   Add "Wazuh" app to workflow
-   Connect Get API → VirusTotal → User Input → Wazuh
-   Configure Wazuh app:
    -   API Key: Select "Get API" output
    -   URL: `https://YOUR_WAZUH_IP:55000`
    -   Action: "Run command"
    -   Command: "firewall-drop0"
    -   Agent list: Get from execution arguments (agent.id)
    -   Alert: Paste entire alert JSON from execution arguments

We first give the app a name, then pass the arguments.

Find Actions: `Run Command`
API Key: You need to get this one from the Get API App

**Figure 210: API**

![image](https://github.com/user-attachments/assets/b672c8cf-910d-483f-9880-5966bc43b9d5)

Url: `https://<Wazuh Server IP>:55000`

**Figure 211: URI**

![image](https://github.com/user-attachments/assets/53250be1-ed29-4999-95f4-98cea1c991dd)

Command: `firewall-drop0`, the Active Response command
Alert: make it dynamic
```xml
{"data":{"srcip":"$exec.all_fields.data.srcip"}}
```

**Figure 212: Command**

![image](https://github.com/user-attachments/assets/a1db85df-83be-415f-ab6f-dcf61b2a36b5)

Agent list: `$exec.all_fields.agent.id`, make it dynamic

**Figure 213: Agent List**

![image](https://github.com/user-attachments/assets/291ee4e8-e535-45a8-9299-bd9eaaade6b5)

Now that we have all the nodes connected and the setup, it is time to run or rerun the flow to test if it is working.

### Test End-to-End Response

-   Generate SSH brute force on Ubuntu machine
-   Verify alert flows through Shuffle
-   Receive email asking for approval
-   Click "Yes" to approve
-   Verify IP is blocked in iptables on Ubuntu machine

Check the Ubuntu client IP table is empty `iptables --list`, if it doesn't use `iptables --flush`.

**Figure 214: IP Tables**

![Screenshot 2025-06-17 132533](https://github.com/user-attachments/assets/f39cb48b-1543-4160-bef9-d5c6abd51844)

I will rerun on of the flows cotaining Rule ID 100003 or 100004, if you don't have any you need to generate it trying to SSH the Unbuntu client with a VPN to get a IP to block with a non-existen account or try to login with the root with the wrong password more than three times to generate the alert. 

**Figure 215: Wazuh Dashboard**

![Screenshot 2025-06-17 132931](https://github.com/user-attachments/assets/76231f84-3b48-4531-8737-f827589ece10)

**Figure 216: Shuffle Alerts**

![Screenshot 2025-06-17 133028](https://github.com/user-attachments/assets/06652bb9-0ef2-445b-a96f-d24e411a8aa0)

Shuffle alerts: Remember, Shuffle receives alerts from level 5 and above.

**Figure 217: Rule ID 100003 on the list**

![Screenshot 2025-06-17 133047](https://github.com/user-attachments/assets/efe8e547-0b34-4df2-aaa4-bc0892e6ceb4)

-   Check TheHive for alerts

**Figure 218: TheHive Alert**

![Screenshot 2025-06-17 133215](https://github.com/user-attachments/assets/4d14a32a-0112-4743-a313-5393b250d264)

**Figure 219: TheHive Arguments**

![Screenshot 2025-06-17 133248](https://github.com/user-attachments/assets/f89053bf-de34-4f38-805c-e1de67007558)

**Figure 220: Email Alert**

![image](https://github.com/user-attachments/assets/05e2dca5-2a96-4dc6-9d7b-23eb5b0601bf)

Email alert received, arguments passed correctly.

**Figure 221: Response Link**

![Screenshot 2025-06-17 134002](https://github.com/user-attachments/assets/46cea115-da7b-4f8b-a176-64d7482fd424)

Please copy and paste the response into a blank web tab to activate it.

**Figure 222: IP Block**

![Screenshot 2025-06-17 133944](https://github.com/user-attachments/assets/77855fa7-bf5d-4bdd-99ec-a1b3a9f07d4f)

The Ubuntu client's IP tables are dropping the connection for the malicious IP.




-----------------------------------------------------

- Log in to the `mylab@test.com` account

![Screenshot 2025-06-15 211936](https://github.com/user-attachments/assets/dccb6c3f-cbcd-4861-9e53-dab51f004caa)<br>
_`mylab@test.com` account_

------------------------------------------------------


















## **Testing the Workflow**



## **Expanding the Project**

Let's take this project a step further by adding a flow to block IPs originating outside the office IP range and disabling the user account. Also, for the Mimikatz flow, please delete the binary when found and send an email notification when the response is executed.

![image](https://github.com/user-attachments/assets/de239cbf-19e0-456f-b243-08688c58c2f5)
_Final workflow_

Let's address the flow for deleting when Mimikatz is detected first. Drag all the Apps needed to complete this task.

![image](https://github.com/user-attachments/assets/f742779c-7c2b-4623-b88e-00730bc24654)
_Adding Apps to deleted Mimikatz when detected_

-   Adding Python App

The Python App will be used to convert the VirusTotal timespan and give a friendly format.

We first give the app a name, then pass the arguments.

Find Actions: `Execute Python`, we used the Last Analysis Date generated by the VirusTotal App for our alert.
Code: Using ChatGPT, we can quickly crack a script to achieve this.
```python
from datetime import datetime, timezone

# Unix timestamp (seconds since 1970-01-01 00:00:00 UTC)
ts = $virustotal.#.body.data.attributes.last_analysis_date

# Convert to an aware datetime object in UTC
dt_utc = datetime.fromtimestamp(ts, tz=timezone.utc)

# Or, if you prefer the classic “YYYY-MM-DD HH:MM:SS UTC” format:
print(dt_utc.strftime("%Y-%m-%d %H:%M:%S UTC"))
```

![image](https://github.com/user-attachments/assets/157ee359-b167-44b2-a2e9-76cd42f2a226)
_Python App setup_

-   User Input App

We first give the app a name, then pass the arguments.

Input options: `Email`
Email: `disposable email`, Email delivery to the analyst.
Information:
```markdown
<h3>🚨 Mimikatz Activity Detected</h3>

Mimikatz detected on host: <strong>$exec.text.win.system.computer</strong> with the process ID: <strong>$exec.text.win.system.processID</strong> and the command line: <strong>$exec.text.win.eventdata.commandLine</strong>. VirusTotal Reputation: <strong>$virustotal.#.body.data.attributes.reputation/$virustotal.#.body.data.attributes.sandbox_verdicts.Zenbox.confidence</strong>

<strong>VirusTotal</strong> scan returned:

<table style="border-collapse:collapse; margin:0;">
  <tr><th align="left">Metric</th><th align="left">Result</th></tr>
  <tr><td>Last Analysis Date</td><td>$date_format.#.message</td></tr>
  <tr><td>Malicious</td><td>$virustotal.#.body.data.attributes.last_analysis_stats.malicious</td></tr>
  <tr><td>Suspicious</td><td>$virustotal.#.body.data.attributes.last_analysis_stats.suspicious</td></tr>
  <tr><td>Undetected</td><td>$virustotal.#.body.data.attributes.last_analysis_stats.undetected</td></tr>
  <tr><td>Harmless</td><td>$virustotal.#.body.data.attributes.last_analysis_stats.harmless</td></tr>
  <tr><td>VirusTotal Report</td>
      <td><a href="$virustotal.#.body.data.links.self">$virustotal.#.body.data.links.self</a></td></tr>
</table>

<strong>Would you like to delete the binary?</strong>
```

![image](https://github.com/user-attachments/assets/b39c7dd8-42a4-436d-bb9d-a69d81c3a87c)

![image](https://github.com/user-attachments/assets/87b8867f-754c-4b8d-85c6-dc6043bb77e1)

Before setting up the Wazuh App, we need to find out what Active Response we need to use to remove the malicious file `Mimikatz`, I found the following Proof of Concept on Wazuh documentation ([Detecting and removing malware using VirusTotal integration](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-remove-malware-virustotal.html#detecting-and-removing-malware-using-virustotal-integration)).

The proof of concept leverages the VirusTotal integration. With some rules integrated on Wazuh XML rules and using a Python script, they manage to delete the file on a Linux system, then translate the script into a binary (EXE) to be used on a Windows machine they aslo manage to delete the file on the Windows machine; the only issue that this method work using the VirusTotal integration. After several days trailing and failing I cracked it.

The following is the Python script used for the VirusTotal Integration `remove-threat.sh` to remove the file on the Windows client.

```python
#!/usr/bin/python3
# Copyright (C) 2015-2022, Wazuh Inc.
# All rights reserved.

import os
import sys
import json
import datetime

if os.name == 'nt':
    LOG_FILE = "C:\\Program Files (x86)\\ossec-agent\\active-response\\active-responses.log"
else:
    LOG_FILE = "/var/ossec/logs/active-responses.log"

ADD_COMMAND = 0
DELETE_COMMAND = 1
CONTINUE_COMMAND = 2
ABORT_COMMAND = 3

OS_SUCCESS = 0
OS_INVALID = -1

class message:
    def __init__(self):
        self.alert = ""
        self.command = 0

def write_debug_file(ar_name, msg):
    with open(LOG_FILE, mode="a") as log_file:
        log_file.write(str(datetime.datetime.now().strftime('%Y/%m/%d %H:%M:%S')) + " " + ar_name + ": " + msg +"\n")

def setup_and_check_message(argv):

    # get alert from stdin
    input_str = ""
    for line in sys.stdin:
        input_str = line
        break


    try:
        data = json.loads(input_str)
    except ValueError:
        write_debug_file(argv[0], 'Decoding JSON has failed, invalid input format')
        message.command = OS_INVALID
        return message

    message.alert = data

    command = data.get("command")

    if command == "add":
        message.command = ADD_COMMAND
    elif command == "delete":
        message.command = DELETE_COMMAND
    else:
        message.command = OS_INVALID
        write_debug_file(argv[0], 'Not valid command: ' + command)

    return message


def send_keys_and_check_message(argv, keys):

    # build and send message with keys
    keys_msg = json.dumps({"version": 1,"origin":{"name": argv[0],"module":"active-response"},"command":"check_keys","parameters":{"keys":keys}})

    write_debug_file(argv[0], keys_msg)

    print(keys_msg)
    sys.stdout.flush()

    # read the response of previous message
    input_str = ""
    while True:
        line = sys.stdin.readline()
        if line:
            input_str = line
            break

    # write_debug_file(argv[0], input_str)

    try:
        data = json.loads(input_str)
    except ValueError:
        write_debug_file(argv[0], 'Decoding JSON has failed, invalid input format')
        return message

    action = data.get("command")

    if "continue" == action:
        ret = CONTINUE_COMMAND
    elif "abort" == action:
        ret = ABORT_COMMAND
    else:
        ret = OS_INVALID
        write_debug_file(argv[0], "Invalid value of 'command'")

    return ret

def main(argv):

    write_debug_file(argv[0], "Started")

    # validate json and get command
    msg = setup_and_check_message(argv)

    if msg.command < 0:
        sys.exit(OS_INVALID)

    if msg.command == ADD_COMMAND:
        alert = msg.alert["parameters"]["alert"]
        keys = [alert["rule"]["id"]]
        action = send_keys_and_check_message(argv, keys)

        # if necessary, abort execution
        if action != CONTINUE_COMMAND:

            if action == ABORT_COMMAND:
                write_debug_file(argv[0], "Aborted")
                sys.exit(OS_SUCCESS)
            else:
                write_debug_file(argv[0], "Invalid command")
                sys.exit(OS_INVALID)

        try:
            file_path = msg.alert["parameters"]["alert"]["data"]["virustotal"]["source"]["file"]
            if os.path.exists(file_path):
                os.remove(file_path)
            write_debug_file(argv[0], json.dumps(msg.alert) + " Successfully removed threat")
        except OSError as error:
            write_debug_file(argv[0], json.dumps(msg.alert) + "Error removing threat")


    else:
        write_debug_file(argv[0], "Invalid command")

    write_debug_file(argv[0], "Ended")

    sys.exit(OS_SUCCESS)

if __name__ == "__main__":
    main(sys.argv)
```

The script requires the information (payload) to be passed in the following JSON format.

```json
payload = {
    "parameters": {
        "alert": {
            "rule": {"id": rule_id},
            "data": {
                "virustotal": {
                    "source": {
                        "file": file_path
                    }
                }
            }
        }
    }
}
```

This makes sense, as the information passed in the proof of concept comes from the VirusTotal App. However, in our case, the information comes from the Webhook, so we need to modify the script to suit our JSON structure.

## **Windows Active Response Script**

Knowing the JSON structure from the Webhook, and after some modifications to the original script, we have the following.


We need to replace these arguments from the JSON above.

```json
"data": {
    "virustotal": {
        "source": {
            "file": file_path
        }
    }
}
```

New JSON structure, designed to use Webhook data.

```json
payload = {
    "parameters": {
        "alert": {
            "rule": {"id": rule_id},
            "data": {
                "win": {
                    "eventdata": {
                        "image": file_path
                    }
                }
            }
        }
    }
}
```

Also, we need to make sure the script is able to remove the file, even if the application is running; for that, we need to check if the process is running and then kill the process, then remove the file afterwards. We require Python's psutil module to find and kill the process. You must ensure psutil is installed on the agent, so let's install the modue on our Windows client.

```powershell
pip install psutil
```

Modifying the line `file_path = alert["data"]["win"]["eventdata"]["image"]`, the final script is as follows.

```python
#!/usr/bin/python3
# Copyright (C) 2015-2022, Wazuh Inc.
# All rights reserved.

import os
import sys
import json
import datetime
import psutil

if os.name == 'nt':
    LOG_FILE = "C:\\Program Files (x86)\\ossec-agent\\active-response\\active-responses.log"
else:
    LOG_FILE = "/var/ossec/logs/active-responses.log"

ADD_COMMAND = 0
DELETE_COMMAND = 1
CONTINUE_COMMAND = 2
ABORT_COMMAND = 3

OS_SUCCESS = 0
OS_INVALID = -1

class message:
    def __init__(self):
        self.alert = ""
        self.command = 0

def write_debug_file(ar_name, msg):
    with open(LOG_FILE, mode="a") as log_file:
        log_file.write(str(datetime.datetime.now().strftime('%Y/%m/%d %H:%M:%S')) + " " + ar_name + ": " + msg + "\n")

def setup_and_check_message(argv):
    input_str = ""
    for line in sys.stdin:
        input_str = line
        break

    try:
        data = json.loads(input_str)
    except ValueError:
        write_debug_file(argv[0], 'Decoding JSON has failed, invalid input format')
        message.command = OS_INVALID
        return message

    message.alert = data

    command = data.get("command")

    if command == "add":
        message.command = ADD_COMMAND
    elif command == "delete":
        message.command = DELETE_COMMAND
    else:
        message.command = OS_INVALID
        write_debug_file(argv[0], 'Not valid command: ' + str(command))

    return message

def send_keys_and_check_message(argv, keys):
    keys_msg = json.dumps({
        "version": 1,
        "origin": {"name": argv[0], "module": "active-response"},
        "command": "check_keys",
        "parameters": {"keys": keys}
    })

    write_debug_file(argv[0], keys_msg)

    print(keys_msg)
    sys.stdout.flush()

    input_str = ""
    while True:
        line = sys.stdin.readline()
        if line:
            input_str = line
            break

    try:
        data = json.loads(input_str)
    except ValueError:
        write_debug_file(argv[0], 'Decoding JSON has failed, invalid input format')
        return message

    action = data.get("command")

    if "continue" == action:
        return CONTINUE_COMMAND
    elif "abort" == action:
        return ABORT_COMMAND
    else:
        write_debug_file(argv[0], "Invalid value of 'command'")
        return OS_INVALID

def kill_process_by_path(file_path, log_prefix):
    killed = False
    for proc in psutil.process_iter(['pid', 'exe']):
        try:
            if proc.info['exe'] and proc.info['exe'].lower() == file_path.lower():
                proc.kill()
                write_debug_file(log_prefix, f"Killed process PID {proc.pid} for {file_path}")
                killed = True
        except (psutil.NoSuchProcess, psutil.AccessDenied):
            continue
    if not killed:
        write_debug_file(log_prefix, f"No running process found for {file_path}")

def main(argv):
    write_debug_file(argv[0], "Started")

    msg = setup_and_check_message(argv)

    if msg.command < 0:
        sys.exit(OS_INVALID)

    if msg.command == ADD_COMMAND:
        alert = msg.alert["parameters"]["alert"]
        keys = [alert["rule"]["id"]]
        action = send_keys_and_check_message(argv, keys)

        if action != CONTINUE_COMMAND:
            if action == ABORT_COMMAND:
                write_debug_file(argv[0], "Aborted")
                sys.exit(OS_SUCCESS)
            else:
                write_debug_file(argv[0], "Invalid command")
                sys.exit(OS_INVALID)

        try:
            file_path = alert["data"]["win"]["eventdata"]["image"]
            kill_process_by_path(file_path, argv[0])  # 🔪 Kill the process first
            if os.path.exists(file_path):
                os.remove(file_path)
                write_debug_file(argv[0], json.dumps(msg.alert) + " Successfully removed threat file.")
            else:
                write_debug_file(argv[0], json.dumps(msg.alert) + " File not found: " + file_path)
        except Exception as error:
            write_debug_file(argv[0], json.dumps(msg.alert) + " Error removing threat: " + str(error))

    else:
        write_debug_file(argv[0], "Invalid command")

    write_debug_file(argv[0], "Ended")
    sys.exit(OS_SUCCESS)

if __name__ == "__main__":
    main(sys.argv)
```

This script does

-   Identifies and kills processes matching the exact file path.
-   Then deletes the file (if it exists).
-   Logs every step and failure point.

## Configuration for the Windows endpoint (Steps copied from Wazuh Documentation)

### Windows endpoint

1.  Perform the following steps to configure Wazuh to monitor near real-time changes in the `/Downloads` directory. These steps also install the necessary packages and create the active response script to remove malicious files. Open the `ossec.conf` file on the Windows client.

2.  Search for the `<syscheck>` block in the Wazuh agent `C:\Program Files (x86)\ossec-agent\ossec.conf` file. Make sure that `<disabled>` is set to `no`. This enables the Wazuh FIM module to monitor for directory changes.

3.  Add an entry within the `<syscheck>` block to configure a directory to be monitored in near real-time. In this use case, you configure Wazuh to monitor the `C:\Users\<USER_NAME>\Downloads` directory. Replace the `<USER_NAME>` variable with the appropriate user name:

    `<directories realtime="yes"\>C:\\Users\\<USER\_NAME>\\Downloads</directories>`
    
![Screenshot 2025-06-17 184452](https://github.com/user-attachments/assets/c65ee645-3e5e-4772-827c-69776052f00f)

![Screenshot 2025-06-17 184557](https://github.com/user-attachments/assets/7dfb0d0b-7ab6-4842-ad37-8b09c7c3093d)

4.  Download the Python executable installer from the [official Python website](https://www.python.org/downloads/windows/).
5.  Run the Python installer once downloaded. Make sure to check the following boxes:
    -   `Install launcher for all users`
    -   `Add Python 3.X to PATH` (This places the interpreter in the execution path)

![Screenshot 2025-06-17 184949](https://github.com/user-attachments/assets/837b2d79-b544-45fb-959c-1bb74dc90100)

![Screenshot 2025-06-17 185005](https://github.com/user-attachments/assets/60aeafc0-39da-42e6-b472-04e0529b23a1)
  
![image](https://github.com/user-attachments/assets/be9056e3-bcd2-42c3-9eef-557a6376fe51)

![Screenshot 2025-06-17 185142](https://github.com/user-attachments/assets/614b0954-e4e4-4187-b675-4213f219a39e)

![image](https://github.com/user-attachments/assets/4c6331b9-483d-41c4-94a3-c43f4c56ec10)

6.  Once Python completes the installation process, open an administrator PowerShell terminal and use `pip` to install PyInstaller:

    ```powershell
    pip install pyinstaller
    ```
    ![Screenshot 2025-06-17 185610](https://github.com/user-attachments/assets/ecedd883-df1c-423f-83f5-57a6a0fb2916)

    ```powershell
    pyinstaller \-\-version
    ```
    ![Screenshot 2025-06-17 185700](https://github.com/user-attachments/assets/991592b3-2286-4f82-a11c-02d61a3967aa)

    You use Pyinstaller here to convert the active response Python script into an executable application that can run on a Windows endpoint.

7.  Create an active response script `remove-threat-shuffle.py` to remove a file from the Windows endpoint. Copy and paste from the modified script above:

![image](https://github.com/user-attachments/assets/e4bc3d74-08dc-42a3-ad68-f2f34dacdc4a)

8.  Convert the active response Python script `remove-threat.py` to a Windows executable application. Run the following PowerShell command as an administrator to create the executable:

    ```powershell
    pyinstaller \-F \\path\_to\_remove-threat-shuffle.py
    ```
    
    ![Screenshot 2025-06-17 190308](https://github.com/user-attachments/assets/7a2984a8-d0ff-4ed3-bbc5-b4c0ec960370)

    Take note of the path where `pyinstaller` created `remove-threat-shuffle.exe`.

    ![Screenshot 2025-06-17 190416](https://github.com/user-attachments/assets/e67fb15c-cf8a-48a9-aa79-dc2f2caf8d17)

    ![image](https://github.com/user-attachments/assets/97cd8dbf-6284-409b-9eb4-dd1013430ea6)

10.  Move the executable file `remove-threat.exe` to the `C:\Program Files (x86)\ossec-agent\active-response\bin` directory.

![image](https://github.com/user-attachments/assets/67207f0b-cb57-4297-bd45-a15f7ea0dde3)

11.  Restart the Wazuh agent to apply the changes. Run the following PowerShell command as an administrator:

     ```powershell
     Restart-Service \-Name wazuh
     ```
     
     ![Screenshot 2025-06-17 210236](https://github.com/user-attachments/assets/e78dcbb4-5975-4226-9051-b2b8a8c7529f)

   
## Wazuh server

Perform the following steps on the Wazuh server to configure the Active Response. These steps also enable and trigger the active response script whenever a suspicious file is detected.

12.  Append the following blocks to the Wazuh server `/var/ossec/etc/ossec.conf` file. This enables Active Response and triggers the `remove-threat-shuffle.exe` executable when the VirusTotal query returns positive matches for threats:

Paste within `<ossec_config>   </ossec_config>`.
```xml
<command>
  <name>remove-threat-shuffle</name>
  <executable>remove-threat-shuffle.exe</executable>
  <timeout_allowed>no</timeout_allowed>
</command>
```

![image](https://github.com/user-attachments/assets/1519a604-6977-4ad0-a14b-f602a0b156d5)

```xml
<active-response>
  <disabled>no</disabled>
  <command>remove-threat-shuffle</command>
  <location>local</location>
  <rules_id>100010</rules_id>
</active-response>
```
![image](https://github.com/user-attachments/assets/98d5a733-566b-4e68-bec9-6d4eacc10e02)

13.   Check that we still have the initial Rule ID 100002 on the Wazuh server `/var/ossec/etc/rules/local_rules.xml` file to alert about the Active Response results.

![image](https://github.com/user-attachments/assets/48ad0f1e-cdf8-4b35-999d-9dc2379d3885)

14.  Restart the Wazuh manager to apply the configuration changes:

     ```bash
     sudo systemctl restart wazuh-manager
     ```


## Wazuh App to Delete File Set Up

We first give the app a name, then pass the arguments.

Apikey: `<Get_API>`, you need to get it from the Get API App
Url: Url: `https://<Wazuh Server IP>:55000`

![image](https://github.com/user-attachments/assets/709cb17c-836f-40c2-819f-94565ad2a517)

Command: `remove-threat-shuffle0`, we call the binary we create.
Agent list: `$exec.all_fields.agent.id`, we get the agent ID dynamically
Alert: The script is expecting the Rule ID and the location of the file.

```json
{"rule":{"id":"$exec.all_fields.rule.id"},"data":{"win":{"eventdata":{"image":"$exec.text.win.eventdata.image"}}}}
```

![image](https://github.com/user-attachments/assets/7b3c5784-2e81-49a4-98b4-bebdea0d34d7)


-   Send email notification after removal

We first give the app a name, then pass the arguments.

Find Actions: `Send email shuffle`
Recipients: `Disposable email`
Subject: `$exec.text.win.eventdata.originalFileName file has been deleted`, I used the file name dynamically here.
Body: With the help of AI.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Notification: Malicious File Deleted</title>
    <style>
        /* Basic CSS for email compatibility */
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            color: #333333;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 20px auto;
            background-color: #ffffff;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .header {
            text-align: center;
            padding-bottom: 20px;
            border-bottom: 1px solid #eeeeee;
        }
        .header h1 {
            color: #d9534f; /* Red for security alert */
            margin: 0;
            font-size: 24px;
        }
        .content {
            padding: 20px 0;
        }
        .content p {
            margin-bottom: 15px;
        }
        .footer {
            text-align: center;
            padding-top: 20px;
            border-top: 1px solid #eeeeee;
            font-size: 12px;
            color: #777777;
        }
        .button {
            display: inline-block;
            background-color: #007bff;
            color: #ffffff !important; /* Important for email client override */
            padding: 10px 20px;
            text-decoration: none;
            border-radius: 5px;
            margin-top: 20px;
        }
        .button:hover {
            background-color: #0056b3;
        }
        .highlight {
            font-weight: bold;
            color: #d9534f;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Security Notification</h1>
        </div>
        <div class="content">
            <p>Dear User,</p>
            <p>This is an automated notification from the Shuffle email app to inform you about a recent security action taken on your account.</p>
            <p>A <span class="highlight">malicious file</span> identified as <span class="highlight">"[$exec.text.win.eventdata.originalFileName]"</span> (or similar description) was detected and has been <span class="highlight">successfully deleted</span> from $exec.text.win.system.computer device.</p>
            <p>No further action is required from your side regarding this specific file. We recommend regularly scanning your devices with up-to-date antivirus software and being cautious about opening unexpected attachments.</p>
            <p>If you have any questions or concerns, please do not hesitate to contact our support team.</p>
            <p>
                <a href="mailto:support@shufflemail.com" class="button">Contact Support</a>
            </p>
        </div>
        <div class="footer">
            <p>Thank you for using Shuffle Email App.</p>
            <p>&copy; 2025 Shuffle Email App. All rights reserved.</p>
        </div>
    </div>
</body>
</html>
```

## Let's test Mimikatz detection and removal all together

-   Run Mimikatz on the Windows client machine

![image](https://github.com/user-attachments/assets/94229f05-e787-493d-970a-98a913593a03)
_Mimikatz running_

-   Check if the log is being sent to Wazuh under Rule ID 100002

![image](https://github.com/user-attachments/assets/e06e59e9-539d-422d-8ba6-8d02e5c7ce58)
_Wazuh log Rule ID 100002_

 ![image](https://github.com/user-attachments/assets/ba2914c7-75d7-4a74-b7bf-f24b5b1a51ab)
 _File original name_

-   Check that the Shuffle Webhook is receiving the alert

![image](https://github.com/user-attachments/assets/0fbf8479-5e44-42f1-b861-07bf23a3c35c) 

![image](https://github.com/user-attachments/assets/de012939-9475-4d10-9fe8-d9c93ffe2081)
_Webhook alert_

-   Confirm the alert is being sent to TheHive (Note: if the alert already exists, you will not see the alert; make sure to delete any old Mimikatz alert to see it working)

![image](https://github.com/user-attachments/assets/de6b3de8-dc96-4506-bda9-9963caab525a)
_TheHive alert_

- Check email notifications (Note: for this workflow, you should get two initial emails, one from the email app and the second from the trigger)

![image](https://github.com/user-attachments/assets/c6a64255-ae71-4a0d-866e-3a9dab00625f) 
_Email notifications_

![image](https://github.com/user-attachments/assets/d1d3e700-c341-4aa4-b4f7-ac76acebf750)
_Email app notification_

![image](https://github.com/user-attachments/assets/11a6aa8f-1e45-4934-911b-2cfb7c933b3c)
_Trigger notification_

-   Executing the response (True)

![image](https://github.com/user-attachments/assets/e20cf352-9bc2-4652-ada5-dce8d77a2062) 
_Web trigger_

-   Confirming the file process is stopped and the file has been deleted

![image](https://github.com/user-attachments/assets/085a7eff-a48a-427e-a98a-56786a7868a1) 
_Mimikatz process killed and fie deleted_

-   Check email confirming removal

![image](https://github.com/user-attachments/assets/c694c0e5-e6f6-4e12-a778-6672b7202975) 
_Email file deletion notification_

![image](https://github.com/user-attachments/assets/a27f5e1f-df48-4e1a-8c0e-4798cef126bd)
_Email file deletion notification_

-   Wazah logs for file deteletion

![image](https://github.com/user-attachments/assets/e30afcd9-8453-447b-818e-e61f1c852e34) 
_Wazuh file delete log_


## Workflow Logon from IP range outside of the office IP range, BLock IP and disbale user

The first step on this flow is to create a custom rule to capture events accessing the Ubuntu client through SSH externally. Go to the Wazuh web interface and in custom rules add the following.

```xml
  <rule id="100006" level="5">
    <if_sid>5715</if_sid>
    <srcip negate="yes"><add your public IP>/24</srcip>
    <description>Successful SSH logins originating outside of the office</description>    
    <mitre>
      <id>T1078</id>
      <id>T1021.004</id>      
    </mitre>
  </rule>
```
With the piece of code above, we are telling it to record any successful SSH connection with event code 5717, and it is outside our public IP range, you can find out your public IP using available websetes onlone, e.g if your public IP is 205.198.5.6 you will put 205.198.5.0/24 to cover the range. The negate=yes is to evaluate that the IP is not in the range.

![image](https://github.com/user-attachments/assets/4e6f7799-7fe7-41f6-a7d0-45d292307327)


-  Editing ossec.conf

Now we need to edit our ossec.conf and enable the Block IP and Disable account Active responses. As we have already enabled the blocking IP part, just copy the following code for disabling accounts on Linux.

```xml
  <active-response>
    <disabled>no</disabled>
    <command>disable-account</command>
    <location>local</location>
    <rules_id>100007</rules_id>
    <timeout>no</timeout>
  </active-response>
```

![image](https://github.com/user-attachments/assets/4d5135a6-40de-4d58-90e3-2451f3dacaba)

For this part of the workflow we are going to filter aletr with Rule ID 100006 where we are tracking successful SSH logins originating outside of the office. if the rule is triggered we will enrich the IP with VirusTotal App, we will get an alert on TheHive and an email notification asking us if we want to block the IP and disable the user account.

![image](https://github.com/user-attachments/assets/0e0352b5-3fd2-4e87-ac33-6b7046049389)

Let's drag all the components need it for this and connect the nodes as desscibe on the figure above.

-   Setup condition

![image](https://github.com/user-attachments/assets/04ac70a6-e20f-468b-921e-ce6c3ae382bb)


-   Enrich IP address with VirusTotal App

![image](https://github.com/user-attachments/assets/5c001bfe-c253-4634-a96f-5267f9ffb847)

-   Setup TheHive App

Name: `TheHive_Out_Office_Logon`
Find Actions: `Create alert`
Title: `$exec.title (IP: $exec.all_fields.data.srcip)`
Tags: `T1078, T1021.004`
Severity: `2`
Type: `External`
Tip: `2`
Status: `New`
Sourceref: `\"Rule: 100006-$exec.all_fields.data.srcip\"`
Source: `Wazuh`
Pap: `2`
Flag: `false`
Description:
```json
false
```
Summary:
```json
Successful external VPN access was detected on host $exec.all_fields.predecoder.hostname from source IP $exec.all_fields.data.srcip on $exec.all_fields.timestamp . The IP address was identified as originating from $virustotal_ip_check.body.data.attributes.country, and its VirusTotal score indicates $virustotal_ip_check.body.data.attributes.last_analysis_stats.malicious malicious detections. Please check email alerts immediately to block the source IP address and disable the associated user account if necessary.
```

![image](https://github.com/user-attachments/assets/2c6ea78c-a9da-4f2d-af26-b753aea12873)

![image](https://github.com/user-attachments/assets/736f0328-5bca-4a11-98bc-029a564c4647)

-   Trigger App

Name: `Block_Acc_n_IP`
Input options: `Email`
Email: `Disposable email`
Information: 
```html
<h2 style="color:#d9534f;">🚨 	Successful SSH login originating outside of the office</h2>


A login was detected from an IP address <strong>outside the official office IP range</strong>.

<ul>
  <li><strong>Source IP:</strong> $exec.all_fields.data.srcip</li>
  <li><strong>Target Host:</strong> $exec.all_fields.predecoder.hostname</li>
  <li><strong>Username Used:</strong> $exec.all_fields.data.dstuser</li>
</ul>

<h3>🧪 VirusTotal Scan Summary</h3>

The IP <strong>$exec.all_fields.data.srcip</strong> was checked with VirusTotal. Results:

<ul>
  <li><strong>Last Analysis Date:</strong> 11111</li>
  <li><strong>Malicious:</strong> $virustotal_ip_check.body.data.attributes.last_analysis_stats.malicious</li>
  <li><strong>Suspicious:</strong> $virustotal_ip_check.body.data.attributes.last_analysis_stats.suspicious</li>
  <li><strong>Undetected:</strong> $virustotal_ip_check.body.data.attributes.last_analysis_stats.undetected</li>
  <li><strong>Harmless:</strong> $virustotal_ip_check.body.data.attributes.last_analysis_stats.harmless</li>
  <li><strong>Country:</strong> $virustotal_ip_check.body.data.attributes.country</li>
  <li><strong>Full Report:</strong> <a href="$virustotal_ip_check.body.data.links.self" target="_blank">View on VirusTotal</a></li>
</ul>

<h3>🛡️ Action Required</h3>

Would you like to <strong>block the source IP</strong> <code>$exec.all_fields.data.srcip</code>?

Please review and respond to take manual action.
```

![image](https://github.com/user-attachments/assets/7d3c7486-fc0e-42cf-9612-9bee70f9bc49)

-   Wazuh responds to disable the user


Name: `Wazuh_Response_Acc`
Find actions: `Run command`
Apikey: `From Get_API App`
Url: `https://<Wazuh Public IP>:55000`
Command: `disable-account0`
Alert: `{"data":{"dstuser":"$exec.all_fields.data.dstuser"}}`
Agent list: `$exec.all_fields.agent.id`

![image](https://github.com/user-attachments/assets/3e35978e-4420-4eb5-b838-7462ddbaee21)

![image](https://github.com/user-attachments/assets/6c60c29f-e860-41bd-b82a-60d2c5f4dfd4)


-   Wazuh responds to Block IP

Name: `Wazuh_Response_IP`
Find actions: `Run command`
Apikey: `From Get_API App`
Url: `https://<Wazuh Public IP>:55000`
Command: `firewall-drop0`
Alert: `{"data":{"srcip":"$exec.all_fields.data.srcip"}}`
Agent list: `$exec.all_fields.agent.id`

![image](https://github.com/user-attachments/assets/42f017d5-dd1a-4865-a51d-96b70fe11ce6)

![image](https://github.com/user-attachments/assets/5374d331-9ac4-430d-b874-5509d9e99ab7)

- Email notification

-   Name: `email_ip_and_user_blocked`
-   Recipients: `email@dispoableemail.com`   
-   Subject: `IP Blocked & Account Disabled ($exec.all_fields.data.srcip, $exec.all_fields.data.dstuser)`
-   Body: (You can use my template to create yours)
```html
<!DOCTYPE html>
<html>
<head>
<style>
  /* Basic styling for email client compatibility */
  body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
  }
  .email-container {
    max-width: 600px;
    margin: 20px auto;
    background-color: #ffffff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  .header {
    background-color: #dc3545; /* Red for alert, indicating urgency */
    color: #ffffff;
    padding: 10px 20px;
    border-top-left-radius: 8px;
    border-top-right-radius: 8px;
    text-align: center;
  }
  .content {
    padding: 20px;
    line-height: 1.6;
    color: #333333;
  }
  .footer {
    text-align: center;
    padding: 10px;
    font-size: 0.8em;
    color: #777777;
    border-top: 1px solid #eeeeee;
    margin-top: 20px;
  }
  .highlight {
    font-weight: bold;
    color: #dc3545; /* Red for emphasis on critical information */
  }
</style>
</head>
<body>
  <div class="email-container">
    <div class="header">
      <h2>Security Alert: Immediate Action Required</h2>
    </div>
    <div class="content">
      <p>Dear Security Team,</p>
      <p>This is an automated notification from Wazuh regarding a recent security incident.</p>
      <p>
        An unauthorized connection was originated from an IP address outside our allowed office range.
        As a result, the following actions have been taken:
      </p>
      <ul>
        <li>The IP address <span class="highlight">$exec.all_fields.data.srcip</span> has been <span class="highlight">blocked</span> on the host.</li>
        <li>The account <span class="highlight">$exec.all_fields.data.dstuser</span> has been <span class="highlight">disabled</span>.</li>
      </ul>
      <p>
        **Details:** The connection was originated from <span class="highlight">$exec.all_fields.data.srcip</span>, which is outside the designated office IP range.
      </p>
      <p>Please investigate this incident immediately and take any further necessary actions.</p>
      <p>Thank you,</p>
      <p>Wazuh Security Monitoring System</p>
    </div>
    <div class="footer">
      This is an automated email. Please do not reply.
    </div>
  </div>
</body>
</html>
```

![image](https://github.com/user-attachments/assets/0525efcd-1023-468a-b04b-2eed0ac26a18)

![image](https://github.com/user-attachments/assets/d8121c43-6614-4f58-aec2-43994c63ead6)


-  Let's see the workflow in action.

Using a VPN or any way to obtain a public IP differentfrom  the one set up on 100006 rule, let's SSH on te Ubuntu client, make sure you are using a account different ot the root acoount as we will aim to disble the account.

Follow these steps if you have not set up one.

```bash
useradd mylab
```
```bash
passwd mylab
```

-   Checking the IP and user are not disbaled or blacklisted

To check IP table, run the following command
```bash
iptables --list
```
If the IP from your computer is trying to connect is blocked, run the following to clear the table
```bash
iptables --flush
```
Run the following to check the user
```bash
passwd -S mylab
```
If the result is an L on instead of P, that means the user is locked. To unlock it, run the following command
```bash
passwd -u mylab
```

![image](https://github.com/user-attachments/assets/da4db960-39b4-4945-9079-d33ffded615b)


-   SSH to the Ubuntu client using the user you created

```bash
SSH mylab@<Ubuntu client Public IP>
```

- Wazuh logs of successful login outside IP range

![image](https://github.com/user-attachments/assets/4b1fa794-ebf6-4f70-adce-55f13bea46fa)

![image](https://github.com/user-attachments/assets/be2a1ad3-b93c-4612-9297-10efae99f434)

-    TheHive alert

![image](https://github.com/user-attachments/assets/45d4c44d-a2a4-44c2-8b30-eab17577c8a3)

![image](https://github.com/user-attachments/assets/696ca23f-5cd9-448b-94b7-74b1996e1901)


-   Email notification trigger

![image](https://github.com/user-attachments/assets/bc4ace94-ddcf-4b07-aef0-b99d327f554d)

- Triger the response (True)

![image](https://github.com/user-attachments/assets/5ac997c2-7a45-4a80-a53d-1ff746f89290)
_Triggering action_

![image](https://github.com/user-attachments/assets/1805d45f-d532-460e-89c2-ffb782a0a825)
_IP has been blocked_

![image](https://github.com/user-attachments/assets/0e2eb6a0-415d-4ae0-acda-7ee231abbb96)
_User disable_

This concludes our project.



