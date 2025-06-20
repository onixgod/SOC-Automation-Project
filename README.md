# SOC Automation Project (Wazuh, TheHive and Shuffle)
## **Project Overview**

This project explores the design, deployment, and automation of a Security Operations Centre (SOC) using open-source tools, **Wazuh** for SIEM, **TheHive** for case management, and **Shuffle** for orchestration and automation (SOAR). The goal is to simulate and streamline Tier 1 SOC analyst workflows in a cloud-based lab environment.

The project is modular, with each section dedicated to a specific component. Just so you know, future updates will be noted at the beginning of the relevant module to maintain accuracy and clarity.

The project was inspired and guided by _Steven (@MyDFIR on YouTube)_, whose free educational content has been instrumental in developing practical cybersecurity skills.

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

-   **DigitalOcean**: Cloud service provider for virtual machine hosting and network infrastructure
-   **Ubuntu Linux**: Operating system for all virtual machines (20.04/22.04 LTS)
-   **DigitalOcean Network Firewall**: Cloud-based firewall for network security and access control
-   **APIs**: RESTful APIs for platform integration and data exchange
-   **Virtual Machines**: Isolated computing environments for secure testing and deployment

## **System Specifications:**

### Cloud-Based VMs (DigitalOcean)

-   **Wazuh Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **TheHive Server**: Ubuntu, 2 CPUs, 8 GB RAM, 160 GB disk
-   **Client 2**: Ubuntu, 1 CPU, 4 GB RAM, 80 GB disk

### Local Infrastructure (Home Lab)

-   **Client 1**: Windows 10, 2 CPUs, 4 GB RAM, 40 GB disk

### Workflow Description

The following diagram illustrates the complete automation workflow implemented in this project:

**![SOC Automation drawio (4)](https://github.com/user-attachments/assets/3b02cd01-7ac2-429c-811e-ca00e65ab7c2)<br>
 _Network diagram showing the integration of Wazuh SIEM, Shuffle SOAR, and TheHive case management platforms with a hybrid client infrastructure_

The automated SOC workflow operates through the following sequence:

**1\. Event Generation and Collection**

-   Multiple client systems generate security events:
    -   Windows 10 client with Wazuh agent (local home lab)
    -   Ubuntu client with Wazuh agent (DigitalOcean cloud)
-   Events are transmitted through the network router to the internet gateway
**2\. SIEM Processing (Wazuh Manager)**

-   Wazuh Manager receives and processes security events
-   Events are analyzed for potential threats and security incidents
-   Alerts are generated based on predefined rules and threat intelligence
**3\. SOAR Orchestration (Shuffle)**

-   Wazuh sends alerts to Shuffle for automated response orchestration
-   Shuffle processes the alerts and initiates appropriate response workflows
-   Automated actions are triggered based on alert severity and type
**4\. Case Management Integration (TheHive)**

-   Shuffle enriches the Indicators of Compromise (IOCs) with additional threat intelligence
-   Enriched alerts are forwarded to TheHive for case creation and management
-   Security incidents are tracked and managed through collaborative case workflows
**5\. Notification and Response**

-   Email notifications are sent to SOC analysts for critical alerts
-   Response actions are automatically performed on affected systems
-   Analysts receive comprehensive incident information for further investigation
**6\. Closed-Loop Automation**

-   Response actions are executed on both client systems through automated workflows
-   Windows 10 and Ubuntu clients receive automated response actions
-   Incident status updates are communicated back through the integrated platforms
-   Complete audit trail is maintained across all platforms for both environments

This integrated approach ensures rapid threat detection across hybrid cloud and on-premises environments, automated initial response, and comprehensive incident management while maintaining detailed documentation for compliance and analysis.





























