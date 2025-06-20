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

## **Automation Workflow Overview**

To streamline SOC operations, I implemented an automated workflow in **Shuffle** to respond to alerts received from **Wazuh**. The automation integrates **VirusTotal**, **TheHive**, and Wazuh’s response APIs to detect, enrich, and respond to malicious events in real-time.

### **Workflow Description**

1.  **Trigger: Wazuh Alert Webhook**
    -   The workflow is initiated by an incoming alert from Wazuh via a webhook to Shuffle.
2.  **Initial Processing**
    -   The alert is parsed and key indicators (e.g., IP address, user, rule ID) are extracted.
3.  **IP Reputation Check**
    -   The IP address involved in the alert is sent to **VirusTotal** for enrichment.
    -   If the IP is marked as malicious:
        -   The IP is added to a block list.
        -   An alert is generated in **TheHive** under the _Brute Force_ or _Out-of-Office Logon_ templates.
        -   A **Wazuh active response** is triggered to block the IP.
4.  **Rule-Based Branching**
    -   If the alert corresponds to a _Brute Force Attempt_, _Suspicious Logon_, or _Mimikatz Detection_:
        -   Specific case templates are triggered in **TheHive**.
        -   Supporting evidence is enriched via VirusTotal, where applicable.
        -   The malicious IP is blocked if it is not already.
5.  **TheHive Integration**
    -   For each matched condition, a case is created in **TheHive** with detailed observables, tags (e.g., TTPs), and linked artifacts.
    -   Helps track incidents and assist SOC analysts with structured investigation workflows.
6.  **Wazuh Active Responses**
    -   Automated blocking of suspicious IPs or accounts is handled through **custom Wazuh response scripts** triggered by Shuffle, based on enrichment results and rule logic.
  






























