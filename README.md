# SOC Automation Project (Wazuh, TheHive and Shuffle)
## **Project Overview**

This project focuses on the design, deployment, and automation of a cloud-based Security Operations Centre (SOC) using open-source tools: **Wazuh** for Security Information and Event Management (SIEM), **TheHive** for case management, and **Shuffle** for orchestration and automation (SOAR). The primary objective is to simulate and streamline Tier 1 SOC analyst workflows in a lab environment.

The project is structured in modular sections, each dedicated to a specific component or function. Please note that I have implemented several modifications and added extra features beyond the original setup by @MyDFIR. Any future changes or enhancements will be clearly documented at the beginning of the relevant module for accuracy and transparency.

This project was inspired and guided by the work of **Steven (@MyDFIR on YouTube)**, whose free educational content has been invaluable in building real-world cybersecurity skills.

## Objectives

### Primary Objectives:

-   **Establish a Functional SOC Environment**: Deploy and configure a complete SOC infrastructure using Wazuh (SIEM), Shuffle (SOAR), and TheHive (Case Management) on cloud-based virtual machines
-   **Implement Multi-Platform Integration**: Successfully integrate three distinct security platforms through APIs and custom configurations to create a unified security ecosystem
-   **Automate Incident Response Workflows**: Develop automated playbooks that can detect, analyse, and respond to security incidents with minimal manual intervention
-   **Demonstrate SOC Tier 1 Competencies**: Showcase proficiency in tools and processes essential for entry-level SOC analyst roles

### Secondary Objectives:

-   **Cloud Infrastructure Management**: Gain hands-on experience with cloud service deployment and network security configuration using DigitalOcean
-   **Security Tool Orchestration**: Master the configuration and management of enterprise-grade security tools in a distributed environment
-   **Documentation and Knowledge Transfer**: Create comprehensive documentation that can serve as a reference for similar implementations

## Acknowledgments

This project was inspired and guided by Steven from @MyDFIR on YouTube. His comprehensive tutorials and free educational content provided the foundational knowledge necessary to complete this implementation. Special thanks to the cybersecurity community for their continuous knowledge sharing and support in building practical security skills.

## Skills Learned

### **Technical Skills:**

-   **SIEM Configuration and Management**: Deployed and configured Wazuh for log collection, analysis, and threat detection across multiple systems
-   **SOAR Platform Implementation**: Set up Shuffle workflows for automated incident response and security orchestration
-   **Case Management System Integration**: Configured TheHive for incident tracking, investigation management, and collaborative security operations
-   **Cloud Infrastructure Deployment**: Provisioned and managed virtual machines on DigitalOcean with appropriate resource allocation and security configurations
-   **Network Security Configuration**: Implemented cloud-based firewall rules and access controls for secure multi-VM communication
-   **API Integration and Management**: Established secure API connections between disparate security platforms for seamless data flow
-   **Linux System Administration**: Managed Ubuntu-based systems, including service configuration, user management, and system monitoring

### **Analytical and Operational Skills:**

-   **Security Incident Workflow Design**: Developed end-to-end processes for incident detection, analysis, and response
-   **Multi-Platform Security Operations**: Coordinated security activities across integrated SIEM, SOAR, and case management platforms
-   **Threat Detection and Response**: Implemented detection rules and automated response mechanisms for common security threats
-   **Documentation and Standard Operating Procedures**: Created detailed technical documentation and operational procedures

## Tools Used

### **Primary Security Platforms:**

-   **Wazuh**: Open-source SIEM platform for log analysis, intrusion detection, and compliance monitoring
-   **Shuffle**: Open source security orchestration, automation and response (SOAR) platform for workflow automation
-   **TheHive**: Scalable, open-source security incident response platform for case management and collaborative investigations

### **Infrastructure and Supporting Technologies:**

-   **DigitalOcean**: Cloud service provider used to host virtual machines and manage the lab’s network infrastructure.
-   **Ubuntu Linux (20.04/22.04 LTS)**: Operating system for the Wazuh server, TheHive instance, and Client 2 VM, providing stability and compatibility with open-source SOC tools.
-   **Windows 10**: Operating system for Client 1 VM to simulate a typical end-user workstation in a SOC environment.
-   **DigitalOcean Network Firewall**: Cloud-based firewall used to control and restrict traffic to and from the virtual machines, enhancing network security.
-   **RESTful APIs**: Utilised for integration between Wazuh, TheHive, and Shuffle, enabling automated data exchange and workflow execution.
-   **Virtual Machines (VMs)**: Isolated environments used to simulate endpoints, threat activity, and SOC operations in a controlled setting.
-   **Draw.io**: Used to design network architecture diagrams, workflows, and automation logic for better visualisation and planning.

## **System Specifications:**

### Cloud-Based VMs (DigitalOcean)

-   **Wazuh Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **TheHive Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **Client 2**: Ubuntu, 1 CPU, 4 GB RAM, 80 GB disk

### Local Infrastructure (Home Lab)

-   **Client 1**: Windows 10, 2 CPUs, 4 GB RAM, 40 GB disk

## Network Architecture and Data Flow

This diagram shows how the different components communicate in your cloud-based SOC lab.

**![SOC Automation drawio (4)](https://github.com/user-attachments/assets/3b02cd01-7ac2-429c-811e-ca00e65ab7c2)<br>
 _Network diagram showing the integration of Wazuh SIEM, Shuffle SOAR, and TheHive case management platforms with a hybrid client infrastructure_
 
### Network Components:

-   **VMs with Wazuh Agents**:
    -   Windows 10 and Ubuntu clients send events to the **Wazuh Manager** via the **Router**.
-   **Wazuh Manager**:
    -   Receives logs/events → sends alerts to **Shuffle**.
    -   Also sends alerts to the internet or internal components.
-   **Shuffle (SOAR)**:
    -   Receives alerts from Wazuh → performs playbook actions.
    -   Interacts with **TheHive** for case creation and **Internet APIs** for enrichment (like VirusTotal).
    -   Sends emails or response tasks as needed.
-   **TheHive (Case Management)**:
    -   Receives enriched IOC data from Shuffle.
    -   Enables analyst investigation and tracking.
-   **SOC Analyst**:
    -   Monitors email alerts and cases.
    -   Can manually trigger response actions if needed.
-   **Internet**:
    -   Used for enrichment (VirusTotal), email delivery, or external communication.

## Automation Workflow (Playbook Logic)

This flowchart illustrates the logic behind your automated incident response process using **Wazuh (SIEM)**, **Shuffle (SOAR)**, and **TheHive (Case Management)**.

![SOC drawio (2)](https://github.com/user-attachments/assets/083230f1-172f-43fc-a91a-6130894e3ecb)<br>
 _Automated alert triage and response flow using Wazuh, Shuffle, and TheHive._

### Workflow Summary:

1.  **Clients Sending Events**:
    -   Wazuh agents on client machines (Windows/Ubuntu) send logs and events to the **Wazuh Manager**.
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

### SOC Automation Playbook Table

| **Step** | **Trigger Condition** | **Rule ID** | **Enrichment** | **Action(s)** |
| --- | --- | --- | --- | --- |
| 1 | Alert received from Wazuh | Any | None | Check if alert level is ≥ 5 |
| 2 | Alert level ≥ 5 | Any | None | Forward alert to Shuffle for processing |
| 3 | Match on specific rule | `100003`, `100004`| VirusTotal | Create TheHive case, send email notification, ask: "Block IP?" → If Yes → Block IP, send email notification |
| 4 | Match on specific rule | `100002` | VirusTotal | Create TheHive case, send email, ask: "Delete file?" → If Yes →, Delete File, send email notification |
| 5 | Match on specific rule | `100006` | VirusTotal | Create TheHive case, send email, ask: "Block IP & Disable User?", If Yes → Block IP and Disable User, send email notification |
| 6 | No rule match or alert level < 5 | Any | None | Do nothing |

### Response Action Mapping

| **Decision Point** | **If Yes** | **If No** |
| --- | --- | --- |
| Delete File? (Rule `100002`) | Delete File → Notify via email | Do nothing |
| Block IP? (Rules `100003/100004`) | Block IP → Notify via email | Do nothing |
| Block IP & Disable User? (Rules `100006`) | Block IP → Disable User → Notify via email | Do nothing |

## Implementation Steps

## Step 1: Architecture Planning and Diagram Creation

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

## Step 2: Deploying the SOC Lab Infrastructure

> **Goal:** Install and configure four core virtual machines:

-   A **Windows 10 client** with Sysmon
-   A **Linux Ubuntu cleint** for Linux enviroment testing  
-   A **Wazuh server** for SIEM
-   A **TheHive server** for case management

> _This step has been adapted from @MyDFIR’s original project, with added enhancements and modified configuration steps._

### **2.1 Set Up Windows 10 Virtual Machine with Sysmon**

> **Note:**
> In this project, I used a preconfigured **Windows 10 Pro template** hosted on **Proxmox** and cloned it to deploy the client machine quickly.
> If you're using **Proxmox**, you can skip the VirtualBox setup steps below and refer to the \[Proxmox screenshots\] for guidance.
> For general users, the following instructions detail how to deploy Windows 10 using **VirtualBox**.

#### **Option A: Using VirtualBox (for general users)**

1.  **Install VirtualBox**
    -   Visit [virtualbox.org](https://www.virtualbox.org/) and download the installer for your OS.
    -   (Optional) Verify SHA-256 checksum using PowerShell:

        ```powershell
        Get-FileHash .\VirtualBox-*.exe -Algorithm SHA256
        ```
    ![image](https://github.com/user-attachments/assets/d2f7013a-fbca-4d09-94ca-3f2ddea36f8e)<br>
    _VirtualBox download_

2.  **Download Windows ISO**
    -   Use Microsoft’s [Media Creation Tool](https://www.microsoft.com/en-us/software-download/windows10)
    -   Select “Create installation media” → Choose **ISO file** → Save locally
    
    ![image](https://github.com/user-attachments/assets/64437e1d-0643-45a9-bfed-fc55700a8f48)<br>
    _Binary download_
    ![image](https://github.com/user-attachments/assets/4d9c6c44-0bb2-4b06-9927-cf14facafaca)<br>
    _ISO creation screen_
    ![image](https://github.com/user-attachments/assets/f4bcbc24-3d60-4798-887d-48f9f4940e02)<br>
    _ISO Creation_
    ![image](https://github.com/user-attachments/assets/36d58126-da55-4403-83fb-62eb718c8ac0)<br>
    _ISO Creation_
    ![image](https://github.com/user-attachments/assets/cd5162ba-40e1-482b-a970-f109bb36e297)<br>
    _ISO Creation_
    ![image](https://github.com/user-attachments/assets/54fa1f0d-0e5a-4948-97ed-4dec093d38c8)<br>
    _ISO dile location_
    ![image](https://github.com/user-attachments/assets/36934d57-ec43-4b14-93eb-c21f9028456f)<br>
    _ISO downloading_

4.  **Create Windows 10 VM in VirtualBox**
    -   VM Name: `Win10-Sysmon`
    -   RAM: 4GB, Disk: 50GB
    -   Load the Windows ISO during creation
    -   Check "Skip unattended installation" to do a manual setup

    ![image](https://github.com/user-attachments/assets/8d7be4d8-26f0-4679-9b14-ba2c8cb68c6f)<br>
    _VirtualBox VM creation settings_

5.  **Install Windows 10 OS**
6.  **Download and Install Sysmon**
    -   Download Sysmon from Microsoft Sysinternals
    ![Screenshot 2025-06-14 123115](https://github.com/user-attachments/assets/93e6de2c-63e7-4ddc-aaa6-e7efe236f134)<br>
    _Sysmon download_
    ![Screenshot 2025-06-14 123238](https://github.com/user-attachments/assets/78689806-ce78-47c1-a966-85aca8fad6e3)<br>
    _Sysmon download location_
    ![Screenshot 2025-06-14 123256](https://github.com/user-attachments/assets/35013eb6-f707-4670-aa64-cc42da116f01)<br>
    _Decompress Sysmon_   
    -   Download the config from [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)
    ![Screenshot 2025-06-14 123608](https://github.com/user-attachments/assets/3491ff17-b83d-4578-82f2-c21c7d26a9fa)<br>
    _Navigate to the Sysmon binary location_
    ![Screenshot 2025-06-14 124020](https://github.com/user-attachments/assets/eac27bf6-71dc-4621-b09c-5ea5334a1d4e)<br>
    ![Screenshot 2025-06-14 124308](https://github.com/user-attachments/assets/ac00b60e-44fc-4125-b484-dfc7de1f9bbb)<br>
    ![Screenshot 2025-06-14 124342](https://github.com/user-attachments/assets/c2a65b1a-b433-41df-aa98-fb3c51d68e3a)<br>
    _Download Sysmon configuration files_
    ![Screenshot 2025-06-14 124416](https://github.com/user-attachments/assets/e8dae915-1516-4cd3-9422-2a0400fec3cc)<br>
    _Move the configuration file to Sysmon folder_
    -   Open PowerShell as Admin:
    ![Screenshot 2025-06-14 123401](https://github.com/user-attachments/assets/27ed729b-50d3-4921-9e46-349a51a8b006)<br>
    _Run PowerShell as Administrator_
    ![Screenshot 2025-06-14 124836](https://github.com/user-attachments/assets/96c04dbb-3a17-479b-baf7-0abf4490f707)<br>
    _PowerShell admin command execution_

        ```powershell
        .\Sysmon64.exe -i sysmonconfig.xml
        ```
    ![Screenshot 2025-06-14 124907](https://github.com/user-attachments/assets/7860d7b1-f8e2-42aa-8b87-1dc740d12d16)<br>
    _Accept EULA_
    ![Screenshot 2025-06-14 124931](https://github.com/user-attachments/assets/029fa419-ae00-4fe1-bad8-9c88878cba33)<br>
    _Sysmon Installed_
    ![Screenshot 2025-06-14 125022](https://github.com/user-attachments/assets/a1a3d8a4-1407-45b4-8f40-ca0a2daeef57)<br>
    ![Screenshot 2025-06-14 125255](https://github.com/user-attachments/assets/0081d901-fb04-4b5f-b154-a6f5f4656e60)<br>
    _Sysmon running in Event Viewer & Services_

#### **Option B: Using Proxmox (Project-specific setup)**

> 💡 If you're following my exact setup:

-   I cloned a preconfigured Windows 10 Pro template from my **Proxmox** environment.
![Screenshot 2025-06-14 111515 PNG](https://github.com/user-attachments/assets/267150d9-cb84-4c7b-bb2c-d5da1adc21f1)<br>
_Proxmox Templates_
![Screenshot 2025-06-14 111544](https://github.com/user-attachments/assets/6750d31e-e675-425c-a2bf-0a4e7dbd4628)<br>
_Clone Windows 10 template_
![Screenshot 2025-06-14 111756](https://github.com/user-attachments/assets/1da8cde7-376b-42fc-a753-9a01456ff9c3)<br>
_Label the new Windows 10 VM_
![Screenshot 2025-06-14 111829](https://github.com/user-attachments/assets/3ae4a0d8-ee6c-4c64-9469-2381d329a43b)<br>
_Labelled Windows 10 VM_
![Screenshot 2025-06-14 112344](https://github.com/user-attachments/assets/4063fb9d-9966-463d-9246-83b3871d406c)<br>
_Start the Windows 10 VM_
-   After cloning, I installed Sysmon and applied the configuration as described above.

### **2.2 Deploy Wazuh Server (Cloud - DigitalOcean)**

#### Step-by-Step:

1.  **Create Droplet**
    -   Image: Ubuntu 22.04 LTS
    ![Screenshot 2025-06-14 132308](https://github.com/user-attachments/assets/ac690233-57c0-40a8-ac09-4243ffa2fa3a)<br>
    _Click on create → Droplets_
    ![Screenshot 2025-06-14 132426](https://github.com/user-attachments/assets/3884dfe6-5608-4a8d-a1e4-e9e0f32ac9c0)<br>
    _Select location and OS_
    -   Plan: 2 vCPU / 8GB RAM minimum (Premium)
    ![Screenshot 2025-06-14 132609](https://github.com/user-attachments/assets/1fb8aa81-7ef7-442b-8148-7ab3eb3b74fe)<br>
    _Select Shared CPU, Premium Intel, 2 CPUs, 160 GB SSDs and 8 GB memory_   
    -   Hostname: `wazuh` and Set root password
    ![Screenshot 2025-06-14 132913](https://github.com/user-attachments/assets/4c6d6007-7da4-40d0-99da-b23febb76b9d)<br>
    _Pick a strong password and select a Hostname_
    ![Screenshot 2025-06-14 132936](https://github.com/user-attachments/assets/70283a0d-9de8-4ab7-aedd-6f40073619bc)<br>
    _Click on Create Droplet_  

2.  **Set Up Cloud Firewall**
    -   Go to **Networking > Firewalls**
    ![Screenshot 2025-06-14 133413](https://github.com/user-attachments/assets/c11871bb-951d-4513-86fa-9fa503535c06)
    _Take note of your Public and Private IPs, then click on edit_
    ![Screenshot 2025-06-14 133428](https://github.com/user-attachments/assets/0fb68643-0186-4c56-bc8f-5615274de017)
    _Click on Create Firewall_
    -   Allow only your IP for SSH (TCP/22)
    ![Screenshot 2025-06-14 133858](https://github.com/user-attachments/assets/ff3b3635-1adb-4e49-88b6-78135dbacfe0)
    _Allow All TCP connections to All Ports for your IP address_
    ![Screenshot 2025-06-14 134204](https://github.com/user-attachments/assets/f0639036-be63-41f0-bbaa-b4a205d9a232)
    _Click on Create Firewall_
    ![Screenshot 2025-06-14 133706](https://github.com/user-attachments/assets/ea4440dc-25f7-4d60-a889-cf945189b282)
    _To find out what your Public IP is, there are plenty of sites that can help you_   
    -   Apply firewall to `wazuh` droplet
    ![Screenshot 2025-06-14 134610](https://github.com/user-attachments/assets/c95c20c1-eff4-47b3-b370-cd4684d9b11e)
    _Click on Networking → Droplets → Add Droplets_
    ![Screenshot 2025-06-14 134635](https://github.com/user-attachments/assets/6db021c4-5877-4d3b-a982-3445136b55cb)
    _Select Add Droplet → Wazuh droplet_

3.  **Install Wazuh**
    -   SSH into the droplet and run:

        bash

        CopyEdit

        `curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh bash wazuh-install.sh -a`

    -   Note the default admin credentials

    📸 _Insert Screenshot 7: Wazuh Dashboard login page_

4.  **Login**
    -   Access the dashboard via `https://<your-public-ip>`
    -   Use `admin / <generated password>`

























