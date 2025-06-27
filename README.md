# SOC Automation Project (Wazuh, TheHive and Shuffle)

## **Project Overview**

This project focuses on the design, deployment, and automation of a cloud-based Security Operations Centre (SOC) using open-source tools: **Wazuh** for Security Information and Event Management (SIEM), **TheHive** for case management, and **Shuffle** for orchestration and automation (SOAR). The primary objective is to simulate and streamline Tier 1 SOC analyst workflows in a lab environment.

The project is structured in modular sections, each dedicated to a specific component or function. Please note that I have implemented several modifications and added extra features beyond the original setup by @MyDFIR. Any future changes or enhancements will be documented at the beginning of the relevant module for accuracy and transparency.

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
-   **DigitalOcean Network Firewall**: A cloud-based firewall used to control and restrict traffic to and from virtual machines, thereby enhancing network security.
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

3.  **Create Windows 10 VM in VirtualBox**
    -   VM Name: `Win10-Sysmon`
    -   RAM: 4GB, Disk: 50GB
    -   Load the Windows ISO during creation
    -   Check "Skip unattended installation" to do a manual setup

    ![image](https://github.com/user-attachments/assets/8d7be4d8-26f0-4679-9b14-ba2c8cb68c6f)<br>
    _VirtualBox VM creation settings_

4.  **Install Windows 10 OS**
5.  **Download and Install Sysmon**
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
    ![Screenshot 2025-06-14 133413](https://github.com/user-attachments/assets/c11871bb-951d-4513-86fa-9fa503535c06)<br>
    _Take note of your Public and Private IPs, then click on edit_
    
    ![Screenshot 2025-06-14 133428](https://github.com/user-attachments/assets/0fb68643-0186-4c56-bc8f-5615274de017)<br>
    _Click on Create Firewall_
    -   Allow only your IP for SSH (TCP/22)
    
    ![Screenshot 2025-06-14 133858](https://github.com/user-attachments/assets/ff3b3635-1adb-4e49-88b6-78135dbacfe0)<br>
    _Allow All TCP connections to All Ports for your IP address_

    ![Screenshot 2025-06-14 134204](https://github.com/user-attachments/assets/f0639036-be63-41f0-bbaa-b4a205d9a232)<br>
    _Click on Create Firewall_

    ![Screenshot 2025-06-14 133706](https://github.com/user-attachments/assets/ea4440dc-25f7-4d60-a889-cf945189b282)<br>
    _To find out what your Public IP is, there are plenty of sites that can help you_
       
    -   Apply firewall to `wazuh` droplet
    ![Screenshot 2025-06-14 134610](https://github.com/user-attachments/assets/c95c20c1-eff4-47b3-b370-cd4684d9b11e)<br>
    _Click on Networking → Droplets → Add Droplets_
    
    ![Screenshot 2025-06-14 134635](https://github.com/user-attachments/assets/6db021c4-5877-4d3b-a982-3445136b55cb)<br>
    _Select Add Droplet → Wazuh droplet_


### 2.3 Deploy TheHive Server (Cloud - DigitalOcean)

#### Step-by-Step:

1.  **Create Droplet**
    -   Same settings as Wazuh
    ![Screenshot 2025-06-14 144407](https://github.com/user-attachments/assets/e0f70066-28e8-4b77-ac94-1cc2969dff04)<br>
    _Click on create → Droplets and then select location and OS_

    ![Screenshot 2025-06-14 144503](https://github.com/user-attachments/assets/82fcfc50-b6c7-452a-8b04-c6372f32dfde)<br>
    _Select Shared CPU, Premium Intel, 2 CPUs, 160 GB SSDs and 8 GB memory_

    -   Hostname: `TheHive`
    ![Screenshot 2025-06-14 144702](https://github.com/user-attachments/assets/b16958e4-518d-43ac-b0e3-b5dde72649f6)<br>
    _Pick a strong password and select a Hostname_

    ![Screenshot 2025-06-14 144717](https://github.com/user-attachments/assets/395c6877-5b29-4cff-a7b5-926f950ff2cc)<br>
    _Click on Create Droplet_
    
    -   Attach to the same firewall
    ![Screenshot 2025-06-14 144900](https://github.com/user-attachments/assets/9e7576d4-77dc-42fb-8468-4d8b816048e7)<br>
    _Add TheHive droplet to Wazuh-Firewall rule_

    ![Screenshot 2025-06-14 144912](https://github.com/user-attachments/assets/0e302266-3a04-4ba9-be56-1209ae7fd5ba)<br>
    _Select TheHive droplet_

    ![Screenshot 2025-06-14 145001](https://github.com/user-attachments/assets/2fb0c9f2-6493-4cf4-be89-5a936f025471)<br>
    _Wazuh-Firewall rue droplets_
    
# **Step 3 – Installing and Configuring TheHive and Wazuh Servers**

1.  **Install Wazuh**
    -   SSH into the droplet or use the Launch Droplet Console and run:
    ![Screenshot 2025-06-14 135355](https://github.com/user-attachments/assets/8e5cbf54-5a36-4838-a7eb-03edafefb438)<br>
    _From Droplet Console_
    
    ![image](https://github.com/user-attachments/assets/3357e801-2d20-4833-924e-cf57d77f9892)<br>
    _SSH from PowerShell_
    
    ```powershell
    ssh root@Wazuh Public IP
    ```
    
    -   Before Wazuh installation, it is always recommended to update and upgrade the OS
    ![Screenshot 2025-06-14 141051](https://github.com/user-attachments/assets/f8c31e1c-2f68-4713-89ba-18716fb72052)<br>
    _Updating OS_

    ```bash
    apt-get update && apt-get upgrade
    ```
    
    -   Install Wazuh using curl command
    ![Screenshot 2025-06-14 141505](https://github.com/user-attachments/assets/3236032c-a888-45f3-937b-0a2a0da1bf39)<br>
    _Wazuh installation_

    ```bash
    curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh bash wazuh-install.sh -a
    ```
    
    ![Screenshot 2025-06-14 141600](https://github.com/user-attachments/assets/c52e532e-babe-4691-83ac-8c934e941051)<br>
    _Installation process_
    
    -   Note the default admin credentials
    
    ![Screenshot 2025-06-14 142343](https://github.com/user-attachments/assets/1454e5d4-4815-4926-8c9b-f2f3d4f236aa)<br>
    _Wazuh credentials_

2.  **Login**
    -   Access the dashboard via `https://<Wazuh public IP>`
    
    ![Screenshot 2025-06-14 142425](https://github.com/user-attachments/assets/b7e58ddb-e9fe-4ed4-9e1f-1ccca2dd4b74)<br>
    _Wazuh login page_
   
    -   Use `admin / <generated password>`
    
    ![Screenshot 2025-06-14 144001](https://github.com/user-attachments/assets/80c9c512-fe14-4eda-9acc-5badf1401c49)<br>
    _Wazuh Dashboard page_

3.  **Install Dependencies**
    -   SSH into the VM, install:
        -   Java (OpenJDK)
        -   Cassandra
        -   Elasticsearch
        -   TheHive

     ![Screenshot 2025-06-14 145256](https://github.com/user-attachments/assets/a0e48a89-51cf-4577-a2a4-cb715b981dff)<br>
     _SSH from PowerShell_
    
    ```powershell
    ssh root@TheHive Public IP
    ```

4.  **Install Java**

    -   Before TheHive installation, it is always recommended to update and upgrade the OS
    ![Screenshot 2025-06-14 145346](https://github.com/user-attachments/assets/361b2f42-5fea-4a44-8c79-9766233f54af)<br><br>

    ![Screenshot 2025-06-14 145506](https://github.com/user-attachments/assets/40ec915a-df8f-4896-9c0d-5a13b5f03d19)<br><br>

    ![Screenshot 2025-06-14 145613](https://github.com/user-attachments/assets/6bc4089e-9899-4896-a510-c7392f368450)<br><br>
    _Updating OS_
    
    ![Screenshot 2025-06-14 150227](https://github.com/user-attachments/assets/7c455fa2-c788-45fa-8da6-cd1fc915e923)<br>
    _Installing Java (OpenJDK)_

    ```bash
    wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
    echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
    sudo apt update
    sudo apt install java-common java-11-amazon-corretto-jdk
    echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
    export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
    ```
    
    ![Screenshot 2025-06-14 150318](https://github.com/user-attachments/assets/0ca330e9-c151-4d1a-949a-2d6bfc3f48dc)<br>
    _Click on OK_

5.  **Install Cassandra**

    ![Screenshot 2025-06-14 150436](https://github.com/user-attachments/assets/2a366b7c-1135-420a-a3ff-c1b76fdab329)<br>
    _Installing Cassandra_

    ```bash
    wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
    echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
    sudo apt update
    sudo apt install cassandra
    ```
    
    ![Screenshot 2025-06-14 150448](https://github.com/user-attachments/assets/9563385d-d36d-4a94-88f3-756ca6a0ea4a)<br>
    _Click on OK_

6.  **Install Elasticsearch**
    
    ![Screenshot 2025-06-14 150623](https://github.com/user-attachments/assets/b688bc28-774f-4124-956a-afbf780a4a00)<br>
    _Installing Elasticsearch_

    ```bash
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
    sudo apt-get install apt-transport-https
    echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
    sudo apt update
    sudo apt install elasticsearch
    ```

    ![Screenshot 2025-06-14 150632](https://github.com/user-attachments/assets/6fa50e78-fd62-4e3b-94e5-fe12e7834b93)<br>
    _Click on OK_

7.  **Configuring Cassandra**

    ![Screenshot 2025-06-14 154158](https://github.com/user-attachments/assets/dcd0058a-248c-4ab6-b5bc-ec3a072f7c8b)<br>
    _Configuring Cassandra_

    ```bash
    nano /etc/cassandra/cassandra.yaml
    ```

    ![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>
    _Change `cluster_name` to `'mylab'` and the `listen_address` to your TheHive public IP_

    ![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>
    _Change the `rpc_address` to your TheHive public IP_

    ![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>
    _Change the seedaddress `seeds` to your TheHive public IP_

    ![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>
    _Stop Cassandra service_
    
    ```bash
    systemctl stop cassandra.service
    ```

    ![Screenshot 2025-06-14 154421](https://github.com/user-attachments/assets/12c35f11-9342-40e7-8242-6cc524d5ee88)<br>
    _We need to remove any Cassandra data when setting up Cassandra for the first time and no critical data exists_

    ```bash
    rm -rf /var/lib/cassandra/*
    ```

    ![Screenshot 2025-06-14 155159](https://github.com/user-attachments/assets/851fed1b-c039-46a2-9774-c422eacd5881)<br>
    _Start and check the status of the Cassandra service_

    ```bash
    systemctl start cassandra.service
    ```

    ```bash
    systemctl status cassandra.service
    ```

8.  **Configuring Elasticsearch**

    ![Screenshot 2025-06-14 154158](https://github.com/user-attachments/assets/dcd0058a-248c-4ab6-b5bc-ec3a072f7c8b)<br>
    _Configuring Elasticsearch_

    ```bash
    nano /etc/elasticsearch/elasticsearch.yml
    ```

    ![image](https://github.com/user-attachments/assets/861fb4de-2f1b-48f0-aca9-0f66bb5f5c9e)<br>
    _Remove the comment # and change `cluster.name` to thehive, remove the comment # to `node.name`_

    ![Screenshot 2025-06-14 160110](https://github.com/user-attachments/assets/aa9ad9fa-e501-4b63-b226-5df52aad8d6a)<br>
    _Remove the comment # and change `network.host` to TheHive public IP, remove the comment # for `http.port`, remove the comment # for `cluster.initial_nodes` and delete `"node-2"`_

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

9.  **Install TheHive**

    ![Screenshot 2025-06-14 230305](https://github.com/user-attachments/assets/47101d45-6434-488d-882f-4f659cb6a55b)<br>
    _Installing TheHive_

    ```bash
    wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
    echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
    sudo apt-get update
    sudo apt-get install -y thehive
    ```

    ![Screenshot 2025-06-14 230722](https://github.com/user-attachments/assets/2b477265-57db-4330-8687-6b383f38584d)<br>
    _Check TheHive directory and change ownership, `-R` to change user and group_

    ```bash
    ls -la /opt/thp/
    ```
    
    ```bash
    chown -R thehive:thehive /opt/thp
    ```

10. **Configuring TheHive**

    ![Screenshot 2025-06-14 230950](https://github.com/user-attachments/assets/e34759bb-6e1b-4ad3-8bd0-16f1bfe29320)<br>
    _Configuring TheHive_

    ```bash
    nano /etc/thehive/application.conf
    ```

    ![image](https://github.com/user-attachments/assets/c9dbd10d-4ecb-4ddc-b7c6-699ad68f75d5)<br>
    _Change `hostname` and `application.baseUrl` to TheHive public IP, give a name to the `cluster-name`, in this case `'mylab'`_

    ![Screenshot 2025-06-14 231441](https://github.com/user-attachments/assets/04802257-9b15-46af-9389-864f9e255e7c)<br>
    _Start, enable and check the status of the TheHive service_

    ```bash
    systemctl start thehive.service
    ```

    ```bash
    systemctl enable thehive.service
    ```

    ```bash
    systemctl status thehive.service
    ```

11. **Run TheHive**
    -   Launch the service
    -   Access via `http://<TheHive public IP>:9000`

    ![Screenshot 2025-06-14 231655](https://github.com/user-attachments/assets/d3c0a7e1-6299-403e-bc11-8687b3754092)<br>
    _TheHive login page_

    -   TheHive will fail on the first login attempt
    -   Check Elasticsearch status
  
    ![Screenshot 2025-06-14 232242](https://github.com/user-attachments/assets/8140e2ad-07c1-41de-90fa-840a6c90297b)<br>
    _Activation has failed, we need to create a jvm.options file to address the heap memory issue on ElasticSearch_

    ![Screenshot 2025-06-14 232242](https://github.com/user-attachments/assets/a229bd64-fcd0-4593-af51-f5314fcef669)<br>
    _Navigate to `/etc/elasticsearch/jvm. options.d/` and create the file jvm.options_

    ```bash
    nano /etc/elasticsearch/jvm.options.d/jvm. options
    ```

    ![Screenshot 2025-06-14 232323](https://github.com/user-attachments/assets/6e0f2081-0426-4a25-9e4a-c2b06aa67f15)<br>
    _Heap memory allocation, copy and paste the below setup and save_

    ```bash
    -Dlog4j2. formatMsgNoLookups=true
    -Xms2g
    -Xmx2g
    ```

    ```bash
    systemctl stop elasticsearch.service
    ```

    ```bash
    systemctl start elasticsearch.service
    ```
    
    **Note:**
    >The `jvm.options` file serves as a critical configuration point for Java applications, particularly for controlling the Java Virtual Machine's (JVM) behaviour and resource allocation. Within this file, lines such as `-    Dlog4j2.formatMsgNoLookups=true`, `-Xms2g`, and `-Xmx2g` instruct the JVM on how to operate. The `-Dlog4j2.formatMsgNoLookups=true` option is a vital security measure, specifically disabling dangerous message lookup features in the Log4j2 logging framework to mitigate the Log4Shell vulnerability and prevent remote code execution. Concurrently, `-Xms2g` and `-Xmx2g` are used to manage the JVM's memory heap: precisely `-Xms2g` sets the initial heap size to 2 gigabytes, ensuring the application has sufficient memory from the moment it starts, while `-Xmx2g` defines the maximum heap size also as 2 gigabytes, preventing the application from consuming excessive system resources. Together, these settings optimise application performance by preventing dynamic heap resizing and enhancing security, making them fundamental for stable and secure Java deployments.

    -   Launch TheHive
    ![Screenshot 2025-06-14 232510](https://github.com/user-attachments/assets/2787b884-13f5-4855-8b70-3f37fa9e7c86)<br>
    _TheHive Dashboard_

12. **Configure Wazuh and Connect Windows Client**

**Access Wazuh Dashboard**

-   Login using admin credentials from Step 2
-   URL: `https://<WAZUH_PUBLIC_IP>`

![image](https://github.com/user-attachments/assets/030479e0-1cfb-47b3-b3fe-bb10184a4b98)<br>
_Wazuh Dashboard_

**Find API Credentials (if lost)**

```bash
tar -xvf wazuh-install.tar cd wazuh-install-files cat wazuh-passwords.txt
```

**Add Windows Agent**

-   On Wazuh dashboard:
    `Agents > Add Agent > Platform: Windows`

![Screenshot 2025-06-14 234041](https://github.com/user-attachments/assets/be97cb46-6cf7-4e31-ae19-c5e3730c0c4d)<br>
-Adding agent_

-   Use Wazuh public IP as the manager address

![image](https://github.com/user-attachments/assets/99ad8269-6b94-43c3-8e2c-8da8103a7a05)<br>
_Selecting the platform_

-   Copy the generated install command

![Screenshot 2025-06-14 234309](https://github.com/user-attachments/assets/9777b528-0ca0-4aab-8848-2fc0dec7640f)<br>
-PowerShell command for agent installation_

**Install on Windows Client**

-   Open **PowerShell as Administrator**

![image](https://github.com/user-attachments/assets/2b38dab5-21c0-447a-81f4-09a3ff7f0c40)<br>
_PowerShell running as administrator_

-   Paste and run the command

![image](https://github.com/user-attachments/assets/5d5ab1e6-872b-4989-b439-d325ca7fee9b)<br>
_PowerShell command_
 
-   Then start the service:

![image](https://github.com/user-attachments/assets/9cb8f941-e637-4d9c-9b8d-c47db5c03ae6)<br>
_Starting Wazuh service from PowerShell_


```bash
net start WazuhSvc
```

![Screenshot 2025-06-14 235441](https://github.com/user-attachments/assets/107913db-02ea-4cc5-b662-90d8e09d2834)<br>
_Check Wazuh service is running_

**Verify Agent Connectivity**

-   Wazuh dashboard → Agents tab → Confirm agent shows as "Active"
-   Navigate to **Security Events** to confirm telemetry ingestion

![image](https://github.com/user-attachments/assets/980f0354-686e-46d3-b2b6-b2cfca7970d8)<br>
_Agent appears in Wazuh Dashboard_

![Screenshot 2025-06-14 235719](https://github.com/user-attachments/assets/65c68e8d-1f68-418c-a2fb-b0d5642b5e5f)<br>
_Agent appears in Wazuh Dashboard_

![Screenshot 2025-06-14 235756](https://github.com/user-attachments/assets/711b48e6-ac6e-4309-8dd2-4abda5b177b9)<br>
_Events being recorded_

**End of Step 3 – Expected Outcome**

By completing this step, you now have:

-   TheHive fully configured and accessible via port 9000
-   Cassandra and Elasticsearch integrated
-   Wazuh ingesting Sysmon telemetry from Windows 10 client
-   All components verified and communicating

# **Step 4: Generating Telemetry and Creating Custom Alerts in Wazuh**

> _Objective: Ingest telemetry from the Windows 10 client using Sysmon, configure Wazuh to receive and log all events, and create a custom detection rule for Mimikatz based on the original binary name._

## **Configure Wazuh Agent to Ingest Sysmon Logs**

### 1\. **Modify Agent Config (Windows 10 Client)**

-   Navigate to:

`C:\Program Files (x86)\ossec-agent`

![Screenshot 2025-06-15 000304](https://github.com/user-attachments/assets/7b5d46c9-8c8e-4346-a88f-cf43150efd84)<br>
_Wazuh agent configuration files location_

-   Backup the file (e.g., rename to `ossec-backup.conf`).

![Screenshot 2025-06-15 000613](https://github.com/user-attachments/assets/18e3ee10-cc94-48f5-8037-c1d220aaf43b)<br>
_Backup `ossec.conf` is recommended in case we need to restore the system_

-   Open `ossec.conf` with admin rights via Notepad.

![Screenshot 2025-06-15 000505](https://github.com/user-attachments/assets/3393db7f-b8c4-4150-9620-ce791df69660)<br>
_`ossec.conf`_

-   Copy one of the `<localfile>` parameters to use for our Sysmon parameters

![Screenshot 2025-06-15 000803](https://github.com/user-attachments/assets/331282fb-df1d-43fa-a8a2-566680eba680)<br>
_Copying application parameters_

-   Under `<localfile>` entries, add:

![Screenshot 2025-06-15 001037](https://github.com/user-attachments/assets/384b20c0-f2ad-4f74-9312-b0c581c0a6a0)<br>
_Sysmon parameter, for this project, we will just be using the Sysmon events, but we can ingest other logs like PowerShell, System, etc_

```xml
<localfile>
   <location>Microsoft-Windows-Sysmon/Operational</location>
   <log_format>eventchannel</log_format>
</localfile>
```

-   To find out the channel name

![image](https://github.com/user-attachments/assets/5d55dfec-21b1-4088-b65d-7db0045b5072)<br>
_Sysmon channel name_

-   Optional: Remove other logs like Application, Security and System for focused testing.

### 2\. **Restart Wazuh Agent Service**

-   Open Services → Restart **Wazuh Agent**

![image](https://github.com/user-attachments/assets/dcc400f0-547b-49f3-ab75-fdee1d8c20b0)<br>
_Services window_

-   Or run in PowerShell (Admin):

```bash
net stop wazuhsvc net start wazuhsvc
```

-   Check that we are getting Sysmon telemetry on Wazuh

![Screenshot 2025-06-15 001855](https://github.com/user-attachments/assets/6493b5b8-7de4-4d3c-b930-ef7ed26312e0)<br>
_Sysmon telemetry on Wazuh_

## **Generate Mimikatz Telemetry**

### 1\. **Exclude Defender Protection**

-   Windows Security → Virus & Threat Protection → Manage Settings → Add Exclusion (Downloads folder)

![image](https://github.com/user-attachments/assets/63d3f875-5a95-400c-bb84-dd8946e69830)<br>

![Screenshot 2025-06-15 002112](https://github.com/user-attachments/assets/d833c107-423e-4f0d-9ec7-e7fc43b14fef)<br>

![Screenshot 2025-06-15 002142](https://github.com/user-attachments/assets/b9aa9360-93e6-41f6-ad3b-9c92776f343e)<br>
_Disabling Defender_

### 2\. **Download and Execute Mimikatz**

![Screenshot 2025-06-15 002204](https://github.com/user-attachments/assets/bdfd0222-8e77-464c-ac54-5bf923fb1c5e)<br>

![image](https://github.com/user-attachments/assets/a1389b62-29d2-46e2-8e3f-ffcca497ffb4)<br>

![image](https://github.com/user-attachments/assets/36592c33-82fb-4460-b45f-b82171edb662)<br>
_Exclude Downloads from Defender_

-   Download Mimikatz ZIP

Go to the link github.com/gentilkiwi/mimikatz/releases

![image](https://github.com/user-attachments/assets/1fac7816-29ee-4bbc-89f0-7598d1d29969)<br>
_Mimikatz installer_

- Disable browsing protection for downloading Mimikatz; otherwise, the browser will stop the transfer.

![Screenshot 2025-06-15 002657](https://github.com/user-attachments/assets/8f64d7f0-d43e-4da6-b0a8-c3b1c7c2c0bd)<br>
_Disable browsing protection_

-   Extract to the Downloads folder

![Screenshot 2025-06-15 002759](https://github.com/user-attachments/assets/13bd51cd-16b8-409a-a4d9-55c2d050c16b)<br>

![Screenshot 2025-06-15 002858](https://github.com/user-attachments/assets/31c232b1-3c44-4833-b845-c18b40be5357)<br>
_Extracting Mimikatz_

- Using PowerShell as an Admin, navigate to the Mimikatz folder. Run the binary.

```powershell
cd C:\Users\Jhon\Downloads\mimikatz_trunk\x64
```

```powershell
cd Downloads\mimikatz .\mimikatz.exe
```

![Screenshot 2025-06-15 003018](https://github.com/user-attachments/assets/1bd72f3b-a126-4214-8a48-50f837e9dc03)<br>
_Mimikatz running in PowerShell_


## Configure Wazuh manager to Archive All Logs**

### 1\. **Edit Wazuh Manager Config (Ubuntu Server)**

SSH on the Wazuh server and edit the `ossec.conf` file.

First, make a copy of the file for safety.

```bash
sudo cp /var/ossec/etc/ossec.conf ~/ossec-backup.conf
```

```bash
nano /var/ossec/etc/ossec.conf
```

Change the following:

```xml
<logall>yes</logall>
<logall_json>yesk/logall_json>
```

![image](https://github.com/user-attachments/assets/01cf85aa-8c2c-4851-897b-aa3c5ed2aa7c)<br>
_Initial setup_

![Screenshot 2025-06-15 005127](https://github.com/user-attachments/assets/f8330c30-bfaf-461c-85a6-5c359423e431)<br>
_Wazuh `ossec.conf` logall settings_

Checking if Wazuh is ingesting JSON logs.

``bash
ls /var/ossec/logs/archives/
``

![Screenshot 2025-06-15 005350](https://github.com/user-attachments/assets/b10bd74e-a9bd-4d04-a73d-e27bf5c76082)<br>
_Archives logs_

### 2\. **Enable Filebeat to Ingest Archive Logs**

```bash
sudo nano /etc/filebeat/filebeat.yml`
````

![image](https://github.com/user-attachments/assets/7824ce97-520c-4ddb-94b2-fc884277461b)<br>
_Edit filebeat_

-   Change:

`archives: enabled: true`

![Screenshot 2025-06-15 005628](https://github.com/user-attachments/assets/4e7fb64a-a5f4-4bd8-8410-ad4c0bc19cef)<br>
_Filebeat archives enabled_

Then restart the Filebeat service

```bash
systemctl restart filebeat. service
```

## **Create Custom Index for Archive Logs**

1.  **Wazuh Dashboard → Stack Management → Index Patterns**

![Screenshot 2025-06-15 010100](https://github.com/user-attachments/assets/3b30c83e-17ec-4691-842d-0092be280f40)<br>
_Stack Management_

![Screenshot 2025-06-15 010225](https://github.com/user-attachments/assets/98a4851b-3bf2-4d5a-a351-bf53c7d6eace)<br>
_Index Patterns_

2.  **Create New Index**:

![image](https://github.com/user-attachments/assets/a5509989-6e59-4cb6-a79b-894332c6d3a9)<br>
_Create index pattern_

-   Pattern: `wazuh-archives-*`

![Screenshot 2025-06-15 010500](https://github.com/user-attachments/assets/9d7c5317-c6aa-4692-b004-c56348625ce2)<br>
_Index pattern name_

-   Select time field: `@timestamp` and then click on Create index pattern.

![Screenshot 2025-06-15 010537](https://github.com/user-attachments/assets/e8ff09e7-2f92-4065-b4c0-22a2a31e8ce5)<br>
_Time field_

![Screenshot 2025-06-15 011019](https://github.com/user-attachments/assets/cb3068c7-947b-4f90-adc3-563242d2c52c)<br>
_New Index Pattern for archives_

## **Create Custom Detection Rule for Mimikatz**

### 1\. **Find Sysmon Event Data**

-   Generate Mimikatz events by running it again

![Screenshot 2025-06-15 011356](https://github.com/user-attachments/assets/195b1991-e7e5-41dd-97a4-4ab60655cd0d)<br>
_Run Mimikatz_

-   Search in Archive Index: `event_id:1` and `original_file_name:mimikatz`

On Windows Event Viewer, go to Event Viewer (Local) → Applications and Services Logs → Microsoft → Windows → Sysmon → Operational, and filter the events 1

![Screenshot 2025-06-15 011449](https://github.com/user-attachments/assets/98d704b9-75ed-4320-b999-ba84ce51de96)<br>
_Events 1_

![Screenshot 2025-06-15 011601](https://github.com/user-attachments/assets/76389baf-a2a8-47b3-bd0d-1fbf23ce2545)<br>
_Mimikazt event being logged by Sysmon_

- Check whether Wazuh manager is logging the Mimikatz logs gathered by Sysmon_

```bash
cat archives.json | grep -i mimikatz
```
 
![Screenshot 2025-06-15 011744](https://github.com/user-attachments/assets/736d78b6-d582-459d-b07e-fa102ed99737)<br>
_Wazuh Mimikatz logs_

![Screenshot 2025-06-15 011837](https://github.com/user-attachments/assets/eb7af982-76d5-4bb7-bcfc-d79bc380c97a)<br>
_Wazuh Mimikatz logs on the web interface_

![Screenshot 2025-06-15 011952](https://github.com/user-attachments/assets/d6559200-f702-4f29-a2fe-1321bc640425)<br>
_Wazuh Mimikatz logs on the web interface, we will use the original filename to build our rule as it will always be the same even if someone changes the name of the file_

### 2\. **Write Custom Rule via Dashboard**

-   Go to: **Rules → Custom Rules → Edit Local Rules**

![Screenshot 2025-06-15 012137](https://github.com/user-attachments/assets/d3b19cd2-bea4-45c6-b69a-cbe99ee518ce)<br>
_Add a rule_

![Screenshot 2025-06-15 012322](https://github.com/user-attachments/assets/ae2c5d77-e608-4a27-af42-fded4e8d3735)<br>
_Manage rules files_

As we are interested on Sysmon event ID 1, we will have a look at the XML file.

![Screenshot 2025-06-15 012409](https://github.com/user-attachments/assets/22c2ab4f-6abc-460c-b9c2-80b2ac05592f)<br>
_Sysmon ID 1 XML rule_

Then, copy one of the rules to use as an example for our custom rule.
![Screenshot 2025-06-15 012720](https://github.com/user-attachments/assets/515e73ca-1b82-4a1d-926a-68ae99961614)<br>
_Copy rule_

-   Go to custom rules

![Screenshot 2025-06-15 012802](https://github.com/user-attachments/assets/23019364-fdc8-40a5-a195-5f2d01ff5323)<br>
_Click on custom rules_

![Screenshot 2025-06-15 012926](https://github.com/user-attachments/assets/28d5f8e0-744a-49fe-94a6-2df43030a9ba)<br>
_Edit custom rules_

-   Paste and modify the rule we copied from the Sysmon XML file:

![image](https://github.com/user-attachments/assets/bd69c55f-b7dc-43cf-a672-ec1db8be8817)<br>
_Our custom rule to alert about Mimikatz usage_

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

### 3\. **Restart Wazuh Manager**

-   Use the Dashboard restart option.

![image](https://github.com/user-attachments/assets/92b02a75-37b5-497a-8e64-b92e4aa8e0ab)<br>
_Restart from dashboard_

![image](https://github.com/user-attachments/assets/a98527d2-15f8-4f15-a144-79a2a2f05fcf)<br>
_Confirm_

-   or:

```bash
sudo systemctl restart wazuh-manager
```

## **Final Test: Bypass Filename and Trigger Alert**

-   Rename Mimikatz executable to a decoy name (e.g., `ztakimim.exe`)

![Screenshot 2025-06-15 013827](https://github.com/user-attachments/assets/2fc5c5a0-5f73-44dc-b20b-a886dd0660d0)<br>
_Rename file_

-   Execute it via PowerShell:

```powershell
.\ztakimim.exe`
```

-   Confirm alert is triggered in Wazuh using `originalFileName`

![Screenshot 2025-06-15 015503](https://github.com/user-attachments/assets/1f7a7f51-ff37-479b-a4d8-d60db5a4e7db)<br>
_Mimikatz alert triggered_

![Screenshot 2025-06-15 195607](https://github.com/user-attachments/assets/3be90d65-af26-42ec-9b56-04ebda4d9141)<br>
_Wazuh alert for renamed Mimikatz_

## **End of Step 4 – Expected Outcome**

You have now:

-   Configured Wazuh Agent to ingest **Sysmon logs**
-   Tuned **Wazuh Manager** to log and archive everything
-   Created a **custom detection rule** for Mimikatz that resists binary renaming
-   Verified that alerts are triggered based on **original file name**

# **Step 5 – Automation Integration with Shuffle, Wazuh, and TheHive**

> _Objective: Finalise SOC automation by integrating Shuffle (SOAR) with Wazuh (SIEM) and TheHive (case management). Automate alert parsing, threat enrichment, email notifications, and responsive actions._

## **Connect Shuffle with Wazuh via Webhook**

### 1\. **Set up Webhook Trigger in Shuffle**

-   Create new workflow: `SOC Automation Project`

![image](https://github.com/user-attachments/assets/6d03c4e1-7e96-4dc8-85c1-c3f30fc44258)<br>

![Screenshot 2025-06-15 200132](https://github.com/user-attachments/assets/aba61c09-8e6a-479b-b65d-4dca18f3aca6)<br>
_SOC Automation Project workflow_

-   Drag a **Webhook** trigger to the canvas and copy the **Webhook URL**

![image](https://github.com/user-attachments/assets/afcbc490-9919-408c-a2c2-330ca21efc2e)<br>
_Shuffle webhook configuration_

![Screenshot 2025-06-15 200521](https://github.com/user-attachments/assets/5dfc9c80-b722-4d7e-99db-14457139f14e)<br>
_Copy Webhook URI_

### 2\. **Configure Wazuh to Use Webhook**

-   Edit Wazuh Manager config (`/var/ossec/etc/ossec.conf`):

![image](https://github.com/user-attachments/assets/e80654e4-ad75-4e6c-a26c-edcb88836ab3)<br>
_ossec.conf_

- Edit `<integration>` 

![Screenshot 2025-06-15 201729](https://github.com/user-attachments/assets/8e3a3ae0-eac6-4da4-a389-d2f8d0b094c6)<br>
_Suffle/Wazuh integration_

![Screenshot 2025-06-15 202234](https://github.com/user-attachments/assets/d93d953e-2392-4dc4-9387-e559e7e30851)<br>
_Targeting events by rule ID 100002, all other events will be ignored by the Webhook_

```xml
<integration>
   <name>shuffle</name>
   <hook_url>https://webhook-url-from-shuffle </hook_url>
   <rule_id>100002</rule_id>
   <alert_format>json</alert_format>
</integration>
```
-   Restart Wazuh Manager:

```bash
sudo systemctl restart wazuh-manager
```

```bash
sudo systemctl status wazuh-manager
```

![Screenshot 2025-06-15 202333](https://github.com/user-attachments/assets/748e3a90-39e9-4b43-921e-ef4f66f8d8c5)<br>
_Restarting and checking the status of Wazuh manager_

-   Start Wazuh Webhook to recieve logs

![image](https://github.com/user-attachments/assets/8af5ada1-7081-4882-aa37-7213f50a316e)<br>
_Webhook started_

- Generate Mimikatz logs by running the binary again

![image](https://github.com/user-attachments/assets/f4515d64-b7bc-4d46-8b5c-728c7100907a)<br>
_Mimikatz running_

![Screenshot 2025-06-15 202658](https://github.com/user-attachments/assets/a68105cb-3fac-4ea7-bcb1-296b2e98fb82)<br>
_Wazuh, Mimkatz rule ID 100002 triggered_

![Screenshot 2025-06-15 202858](https://github.com/user-attachments/assets/f9a5fa36-2764-41b3-8900-b87a6970f78c)<br>
_Wazuh Webhook capturing traffic_

![Screenshot 2025-06-15 203111](https://github.com/user-attachments/assets/81977675-f5e4-47bc-b4ea-a6c300ddedb0)<br>
_Rule ID 100002 captured_

## **Create the Workflow Logic in Shuffle**

### 1\. **Parse SHA256 Hash with Regex**

-   Add `Regex Capture Group` app and connect the Webhook to it.

![image](https://github.com/user-attachments/assets/bd696309-9796-4f3d-8779-65c6791940ee)<br>
_Drag a Suffle tool, label it `SHA256_Value` and change it to 'Regex Capture Group'_

-   Parse hash value from field: `SHA256=<value>`

With the help of ChatGPT or Claude, let's generate a regex pattern to extract the SHA256 hash from the input data

![image](https://github.com/user-attachments/assets/6774e963-5c6c-4258-915b-ebf7f7f23e6d)<br>

Input data: 
```bash
$exec.text.win.eventdata.hashes
```

Regex:
```regex
(?:^|,)SHA256=([A-Fa-f0-9]{64})(?=,|$)
```

-   Use this value as input for enrichment 

![image](https://github.com/user-attachments/assets/4539ec30-fd77-465b-ac71-5c1ab99421e7)<br>
_Regex parsing setup in Shuffle_

## **Enrich Alert with VirusTotal**

### 1\. **Configure VirusTotal App in Shuffle**

-   Add VirusTotal app and connect the Regex app to it, authenticate via API key

![image](https://github.com/user-attachments/assets/4269d3c6-2a75-49d4-9307-492727d46345)<br>
_Drag VirusTotal app_

-   Label the App and change Action: `Get hash report`
![image](https://github.com/user-attachments/assets/cc4f241d-36bd-41bb-86f8-bccee3a62673)<br>
_Get hash_

-   Input: SHA256 value extracted via regex

![image](https://github.com/user-attachments/assets/8ddb381a-9809-4482-9ba1-69390fb07f57)<br>

![image](https://github.com/user-attachments/assets/0dc959aa-cbc9-4277-9c1d-9c741dbd5ff0)<br>
_Get the ID from the generated value by the Regex extension_


-   Re-run the last log to test the VirusTotal App

![image](https://github.com/user-attachments/assets/8b4919e6-8c6f-4b0b-907d-e2cfc3542953)<br>
_Re-running alert_

![Screenshot 2025-06-15 210311](https://github.com/user-attachments/assets/d4ecbc37-ac19-4c43-8d28-2c1396c932ef)<br>
_VirusTotal hash report setup_


## **Create Alert in TheHive**

### 1\. **Create API Key and Org in TheHive**

Go to TheHive portal.

![image](https://github.com/user-attachments/assets/911cb58b-bd97-4ad2-b04c-8ff9bce4378c)<br>
_Click on the + button_

-   Create:
    -   Organisation: `MyLab`, Description: `SOC Automation Project`
 
    ![image](https://github.com/user-attachments/assets/ea14bf4a-4caf-449e-9dc4-44148713038e)<br>

    -   Analyst user
 
    ![Screenshot 2025-06-15 211123](https://github.com/user-attachments/assets/b6e11f7a-4d6b-498a-8c25-9644687955ee)<br>
    _Add users_

    ![image](https://github.com/user-attachments/assets/93490510-1f3f-4d57-ace6-1f81527bbc61)<br>
    _Type: `Normal`, Login: `mylab@test.com`, Name: `mylab`, Profile: `Analyst`
    
    -   Service account user for Shuffle
    
    ![Screenshot 2025-06-15 211258](https://github.com/user-attachments/assets/88c17851-451a-4a9f-abca-0645d4b626f4)<br>
    _Type: `Service`, Login: `shuffle@test.com`, Name: `SOAR`, Profile: `Analyst`

    ![Screenshot 2025-06-15 211450](https://github.com/user-attachments/assets/a5485a03-d46e-4b01-b025-1fde7d81ac30)<br>
    _Preview Normal-Analyst account_

    ![Screenshot 2025-06-15 211530](https://github.com/user-attachments/assets/59b93849-71e0-4f2f-91c9-ec8d11e7371f)<br>
    _Set password for Normal-Analyst account_

-   Generate API key for Shuffle

![image](https://github.com/user-attachments/assets/95de12b6-5fac-4ceb-89c8-c80802eba1dd)<br>
_Preview Service-Analyst account_

![Screenshot 2025-06-15 211751](https://github.com/user-attachments/assets/9823acc1-4492-4263-8911-a75fe4c31701)<br>
_TheHive API key_

- Log in to the `mylab@test.com` account

![Screenshot 2025-06-15 211936](https://github.com/user-attachments/assets/dccb6c3f-cbcd-4861-9e53-dab51f004caa)<br>
_`mylab@test.com` account_

### 2\. **Configure TheHive App in Shuffle**

-   Add TheHive App and connecet the VirusTotal App to it

![image](https://github.com/user-attachments/assets/9bca6b72-17e9-41e5-80f3-9e289cca502c)<br>
_Drag TheHive App_

-   Action: `Create Alert`
-   Fill alert fields:
  
   -   Title, Description
  
   ![image](https://github.com/user-attachments/assets/43ef2114-c971-449b-b634-30e7534fede2)<br>
   _TheHive name_

   -   Authenticate TheHive

   ![image](https://github.com/user-attachments/assets/58a37e2e-4472-431c-96d3-0bff80955a5f)<br>
   _apiKey, the API key can be copied from TheHive service account_

   -   TheHive apiKey and URL
   
   ![Screenshot 2025-06-15 212307](https://github.com/user-attachments/assets/5616050a-f1cf-4d1e-b900-b06b9291f009)<br>
   _TheHive Public IP and port `http://<TheHive public IP>:9000`, click on Submit_

   -   Title, Tags, Summary, Severity

   Title:

   ```bash
   $exec.title
   ```

   Tags: MITRE ID (e.g., T1003 for Mimikatz)
   
   ```bash
   ["T1003"]
   ```

   Summary: (Use my example below to create your summary)

   ```bash
   Mimikatz detected on host: $exec.text.win.system.computer with the processor ID: $exec.text.win.system.processID and the command line: $exec.text.win.eventdata.commandLine, VirusTotal Reputation:   $virustotal.#.body.data.attributes.reputation//$virustotal.#.body.data.attributes.sandbox_verdicts.Zenbox.confidence
   ```
   
   Severity:
   
   ```bash
   2
   ```
   
   ![Screenshot 2025-06-15 215814](https://github.com/user-attachments/assets/cd46b131-1847-46ba-aa9a-0010840eabf8)<br>
   _TheHive alert field mapping `Title`, `Tags`, `Summary`, `Severity`_

   -   Type, Tip, Status, Sourceref, Source, Pap, Flag, Description

   Type:

   ```bash
   Internal
   ```
   
   Tip:

   ```bash
   2
   ```

   Status:

   ```bash
   New
   ```
   
   Sourceref:
   
   ```bash
   \"Rule: 100002\"
   ```
   
   Source:
   
   ```bash
   Wazuh
   ```
   
   Pap:
   
   ```bash
   2
   ```
   
   Flag:
   
   ```bash
   false
   ```
   
   Description: (Use my example below to create your description)

   ```bash
   Mimikatz Detected on Host: $exec.text.win.system.computer from user: $exec.text.win.eventdata.user
   ```

   ![image](https://github.com/user-attachments/assets/37079a2e-fd0e-44c3-ab47-137125a2e5bc)<br>
   _TheHive alert field mapping `Type`, `Tip`, `Status`, `Sourceref`, `Source`, `Pap`, `Flag`, `Description`_


### 1\. **Expose TheHive API Port**

-   Open DigitalOcean firewall port `9000` for inbound traffic

![image](https://github.com/user-attachments/assets/76b2bc1f-5450-4c8f-83b0-3be47bf7f7c6)<br>

![Screenshot 2025-06-15 220202](https://github.com/user-attachments/assets/e1502738-1d67-4483-a862-108b7d73cda1)<br>
_Opening port 9000 for incoming traffic_

Note: I faced an issue with the ThHive API After troubleshooting, I found out the API was passing the arguments in the wrong format. I managed to fix the API body; see below the default setup and the changes I made. If you are facing similar issues, this may help you resolve them. I noted that Steven from @MyDFIR has a different problem with another API.

![Screenshot 2025-06-16 093947](https://github.com/user-attachments/assets/12363a95-4b28-4e23-b6db-4ab0dd2053ac)<br>
_Default API request format_

I found out that `description`, `flag`, `pap`, `tags` and `tip` were not passing the values in the correct format, so I changed them.

![image](https://github.com/user-attachments/assets/f2d82347-897a-48cc-9ffe-27c342b55d58)<br>
_New TheHive Body format_

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

![image](https://github.com/user-attachments/assets/e6b8efa5-7bde-4ab0-94a4-8f3953872f02)<br>
_Submit the API changes_

![image](https://github.com/user-attachments/assets/37258b31-98a9-4567-8272-d5c75300b849)<br>
_Save API_


-   Testing TheHive API changes again by re-running the alert

![Screenshot 2025-06-16 094414](https://github.com/user-attachments/assets/1bae997b-8b59-4cba-891a-43abb19d7e9c)<br>
_Error 400, we need to change the way we pass the Tags value_

   Tags: 
   
   ```bash
   T1003
   ```
![Screenshot 2025-06-16 094636](https://github.com/user-attachments/assets/36e6a046-a6bf-4376-887a-e35d5ac5198d)<br>
_Tags value_

-   Testing TheHive API changes by re-running the alert with the new Tags format

![Screenshot 2025-06-16 094833](https://github.com/user-attachments/assets/57af4177-b940-454f-9119-b95e68d84742)<br>
_Status 200!!, the alert has been sent to TheHive_

![Screenshot 2025-06-16 094853](https://github.com/user-attachments/assets/c859951d-1714-4949-9849-6048e5faf947)<br>
_TheHive dashboard, alert created_

![Screenshot 2025-06-16 094949](https://github.com/user-attachments/assets/c3512820-f815-4bb2-8bae-68156bbf477b)<br>
_All values have been passed to TheHive dashboard_

So far, our workflow should look like the one below.

![Screenshot 2025-06-16 095202](https://github.com/user-attachments/assets/a5a56ece-6731-4730-90d9-7515a5edf1c7)<br>
_Workflow stage_

## **Notify Analyst via Email**

### 1\. **Add Email App in Workflow**

![image](https://github.com/user-attachments/assets/523f5963-cd89-413e-89ce-11d383558c08)<br>
_Drag the Email App_

-   Use a disposable email for testing (I used temp-mail.org, but you can use any other site for that)

![Screenshot 2025-06-16 100135](https://github.com/user-attachments/assets/d6a93ba3-96c9-4c81-a5c3-9f209ec38656)<br>
_Temp email_ 

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

![image](https://github.com/user-attachments/assets/66394e83-9469-463c-b872-a3c291478e24)<br>
_Email App setup_

-   Rerun the flow

![Screenshot 2025-06-16 101015](https://github.com/user-attachments/assets/f73177da-c252-4e46-b19b-3e9cb2a86b74)<br>
_Email sent successfully_

![Screenshot 2025-06-16 101419](https://github.com/user-attachments/assets/58098a94-61b0-4a43-9704-ccb5a281e613)<br>
_Email output_

# **Step 6: Responsive Actions – Wazuh API Integration**

### Deploy Ubuntu Client (Cloud - DigitalOcean)

#### Step-by-Step:

1.  **Create Droplet**
    -   Same settings as Wazuh
    ![image](https://github.com/user-attachments/assets/3ea15ac7-4bd6-4dc8-91e8-586c5b009730)<br>
    _Click on create → Droplets and then select location and OS_

    ![Screenshot 2025-06-16 102353](https://github.com/user-attachments/assets/5af8ddc7-3cd3-408e-84b2-78473d3591f5)<br>
    _Select Shared CPU Basic, Regular Disk, 2 CPUs, 80 GB SSDs and 4 GB memory_

    -   Hostname: `mylab-ubuntu`
  
    ![Screenshot 2025-06-16 102610](https://github.com/user-attachments/assets/ee066439-6a2c-4ee8-b4a3-66ee481344cf)<br>
    _Pick a strong password and select a Hostname_

    ![Screenshot 2025-06-16 103015](https://github.com/user-attachments/assets/2e96fbe1-6776-49be-a70b-514d6ebaac05)<br>
    _Take note of the Public IP_

    -   Create a new firewall rule set to accept all TCP connections in all ports 
      
    ![Screenshot 2025-06-16 103657](https://github.com/user-attachments/assets/eb1b0992-47c3-4331-a273-c54033f07eac)<br>
    _New firewall rule set_

    -    Add the newly created Ubuntu client to that firewall rule
  
    ![Screenshot 2025-06-16 103810](https://github.com/user-attachments/assets/b21ab24a-0126-465f-a783-9ba8c5c9b16c)<br>
    _Add Ubuntu client droplet to new Firewall rule_


**Add Ubuntu Client Agent**

-   On Wazuh dashboard:
    `Agents > Add Agent > Platform: Linux`

You should follow similar instructions to the Windows agent, but this time you can select Linux DEB amd64 and Wazuh Server public IP.

![Screenshot 2025-06-16 105827](https://github.com/user-attachments/assets/7b80f617-2b3e-445d-8076-7bb01b23f99e)
_Ubuntu droplet creation_

-   Copy the generated install command

![Screenshot 2025-06-16 104618](https://github.com/user-attachments/assets/1c34d012-2357-4740-8e9a-98cda1230645)<br>
-Copy the bash command for agent installation on Linux_

**Install on Ubuntu Client**

-   SSH into the Ubuntu client

![Screenshot 2025-06-16 104821](https://github.com/user-attachments/assets/4f0d7177-2fd2-4450-a23d-c8fe2cd46777)<br>
_SSH as root on Ubuntu client_

-   Update and upgrade the Linux client

![Screenshot 2025-06-16 104905](https://github.com/user-attachments/assets/4526a640-5c82-4d69-b43a-bd7b672100a5)<br>
_`apt update && apt upgrade`

![Screenshot 2025-06-16 105048](https://github.com/user-attachments/assets/cadd7a1b-cc3a-409d-912c-e3d35e6dc383)<br>
_Click Ok_

![Screenshot 2025-06-16 105149](https://github.com/user-attachments/assets/49aa2a13-2d46-43c2-9710-480412181d54)<br>
_Click Ok_

![Screenshot 2025-06-16 105211](https://github.com/user-attachments/assets/e7d737c3-750a-44fa-8c06-170993e446f1)<br>
_Click Ok_

-   Paste and run the command

![Screenshot 2025-06-16 105701](https://github.com/user-attachments/assets/3040b797-7447-49a5-a786-2f8fd06a4c74)<br>
_Shell command_
 
-   Then start the service:

![image](https://github.com/user-attachments/assets/9cb8f941-e637-4d9c-9b8d-c47db5c03ae6)<br>
_Starting Wazuh service from Shell_

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

**Verify Agent Connectivity**

-   Check on Wazuh if the Ubuntu client has been added, Wazuh dashboard → Agents tab → Confirm agent shows as "Active"

![Screenshot 2025-06-16 114234](https://github.com/user-attachments/assets/baa542dc-3e0c-4fca-a0cf-a8a7124825af)
_Ubuntu client on Wazuh_

-   Navigate to **Security Events** to confirm telemetry ingestion

![image](https://github.com/user-attachments/assets/5738895d-b3a9-42cb-94bf-eff9cdd03453)<br>
_Events being recorded_

### 1\. **Expose Wazuh API Port**

-   Open DigitalOcean firewall port `55000` for inbound traffic

![image](https://github.com/user-attachments/assets/bab20adb-1b80-4345-b3d1-0511d51326ce)
_Port 55000 accepting all incoming traffic_

### 2\. **Use HTTP App to Fetch Wazuh Token**

-   Add the HTTP request App

![Screenshot 2025-06-16 114607](https://github.com/user-attachments/assets/8bb203f5-2466-454e-8262-87d5fab67011)
_Drag HTTP App_

-   POST request with Wazuh API user credentials, we will use the `curl` action to get the Wazuh API.

```bash
curl -u wazuh:<Wazuh user API Key> -k -X GET Http "https://<Wazuh Server Public API>:55000/security/user/authenticate?raw=true"
```

![Screenshot 2025-06-16 115247](https://github.com/user-attachments/assets/e4bab1af-dd42-49fe-972f-c5b4b78673c4)
_HTTP request to get Wazuh token_

### 3\. **Add Wazuh App – Run Command**

-   Add Wazuh App

![Screenshot 2025-06-16 115551](https://github.com/user-attachments/assets/d47f9962-94ab-4127-966e-5646120ca702)
_Drag Wazuh App_

-  Modify `ossec.conf` to set up Active responses `nano /var/ossec/etc/ossec.conf`.

![image](https://github.com/user-attachments/assets/5c3981b2-8b1e-42e5-b103-ad2a93d726eb)
_Command_

-   Navigate to the Active Response section

![image](https://github.com/user-attachments/assets/d288fc7f-0585-49e5-8fe1-90594e378e63)
_Active Response, we are interested in the `firewall-drop` response command_

-   Enable the active response by deleting the comments `<!--` and `-->`, copy and paste the below.

```bash
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <level>5</level>
  <timeout>no</timeout>
</active-response>
```

![image](https://github.com/user-attachments/assets/d2ef9e78-7612-4eb7-a274-08c6562f7d72)

-   Change Webhook to accept alerts by Level and not by Rule ID. We will set the level to 5, so all rules five and above will be passed to Shuffle.

![image](https://github.com/user-attachments/assets/45586ed9-df9b-4de4-b234-ba7314e61ef4)
_Changing Shuffle integration_

-   Let's test how active response works manually. To do that, we will navigate to the following directory.

```bash
cd /var/ossec/bin/
```

We are interested in the `agent_control`, let's run it and see what available options we have `./agent_control`.

![Screenshot 2025-06-16 131502](https://github.com/user-attachments/assets/7cc0ef34-f363-43b1-a6e9-b4e167948ea6)
_agent_control_

Use `-L` to list what available responses are activated. 

![image](https://github.com/user-attachments/assets/7294ef06-d1a7-4e09-9498-f12eb749c223)
_We can confirm `firewall-drop0` is activated. The `0` is the TimeOut and is appended to the command.

-   Let's test by blocking a known public IP `8.8.8.8` on our Ubuntu client.

![Screenshot 2025-06-16 132537](https://github.com/user-attachments/assets/b523a861-78d2-4b13-aa53-5c13f1bc2273)
_`ping 8.8.8.8` to get responses_

-   Now run the `./agent_control` command on the Wazuh server to block IP `8.8.8.8` on the Ubuntu client.

    -   -b block IP address `8.8.8.8`
    -   -f active response command `firewall-drop0`
    -   -u client ID  `002` Ubuntu machine
 
```bash
./agent_control -b 8.8.8.8 -f firewall-drop0 -u 002
```

![image](https://github.com/user-attachments/assets/f7366735-3db4-4044-80d5-fbe00cfa8beb)
_Client ID_

![image](https://github.com/user-attachments/assets/338a23b5-2ada-4a11-bca9-2630a9be2206)
_Command running_

-   Verify that the IP address was successfully blocked. On the Ubuntu client, `iptables -- list`.

![image](https://github.com/user-attachments/assets/3ae116af-9e52-4435-bdc5-84919b9837cb)
_IP `8.8.8.8` blocked_

-   Take a look at the Active Response logs to see if the active response was successful

```bash
cd /var/ossec/logs/
```
```bash
ls
```

```bash
cat active-responses. log
```

![Screenshot 2025-06-16 133440](https://github.com/user-attachments/assets/67c75b3b-f39a-4ffb-b868-f1ec1c249070)
_Active Response logs, script was run successfully_

## **Modifications to the Original Project**

Before we proceed with the project proposed by @MyDFIR, I decided to take it a step further and add some extra features. He suggests blocking IP addresses based on ID 5710, which is SSH failed login attempts using a non-existent user. However, I decided to add failed authentication attempts for known users, ID 5760, which can send us alerts of brute force on root or any specific existing account, and also send the alert to TheHive. So the aim is the following.

![Screenshot 2025-06-17 131746](https://github.com/user-attachments/assets/5d7ce409-4328-44b0-b3b0-82193311367a)
_Modified Workflow_

Now that we have identified the parameters to be passed to the Wazuh App, let's add a VirusTotal App to enrich the data collected and determine whether the source IP of the attack is blacklisted on the portal.

-   Add a VirusTotal App for IP enrichment

![image](https://github.com/user-attachments/assets/2fb80e57-81b0-4a1d-96aa-a9d71db86b6b)
_VirusTotal App_

-   Add TheHive App for Notifications

![image](https://github.com/user-attachments/assets/516f3295-f705-41c2-9be9-a2e3c209046d)


-   Add User Input App for manual reponses

![image](https://github.com/user-attachments/assets/f2b645d9-8711-4ed1-9c2c-dcbcc4781d45)


-   Add a Shuffle Tool App, we will use this to get to branches for some condictions. I could not find another way for this (change it to Execute Python).

![image](https://github.com/user-attachments/assets/dd54e239-1947-412f-8dee-eed5eb62fd1f)

- Connect all the Apps, see the connections bellow.

![Screenshot 2025-06-17 131746](https://github.com/user-attachments/assets/6e4b087f-514b-43a3-a191-af77e47f5bda)


-   Add the rules on our local rules XML, to capture the events.

We create two rules, one to capture non-existing accounts attempts (ID 100003 targetiing 5710 events) and the one for existing accounts (ID 100004 targgeting 5760 events).

The rule will just be triggered if the attempt fails three times in a space of 60 seconds, you can use the XML below and do the modifcation if you need.

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

![image](https://github.com/user-attachments/assets/4e836125-b661-4a00-876a-923cd0e2d98e)






-   Branches conditions and Python setup

![image](https://github.com/user-attachments/assets/6eb5b3f6-a8e8-494d-af57-668e3a836060)


-  Brach connecting Get Api and VirusTotal App

![image](https://github.com/user-attachments/assets/8f93179c-6be4-439b-9acd-36e4fcc4dbe3)





-   Action: `Run Command`
-   Command: `firewalldrop0`
-   Dynamic argument: Source IP
-   Target Agent ID: Dynamically extracted

📷 _Insert Image 9: Shuffle Wazuh run command setup_

* * *

## 🧑‍💼 **Part G: Add User Input for IP Blocking Decision**

### 1\. **Add User Input App**

-   Prompt: “Would you like to block IP \[source\_ip\]?”
-   Email delivery to analyst
-   If “Yes”, execute Wazuh response command

📷 _Insert Image 10: User input form in email_

* * *

## ✅ **End of Step 5 – Expected Outcome**

-   🟢 Wazuh alerts are parsed and sent to Shuffle
-   🧠 Hash is enriched via VirusTotal
-   🔔 Alerts are created in TheHive
-   📧 Analysts receive emails with event context
-   🔒 Optional: Active response triggers IP blocks via Wazuh API upon analyst approval





























