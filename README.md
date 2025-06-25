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

### Step 1: Architecture Planning and Diagram Creation

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





























