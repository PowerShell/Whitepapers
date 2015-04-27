Configuration Planning and Installation Guide
=============================================

Windows PowerShell Desired State Configuration Pull Server
----------------------------------------------------------

Author: Michael Greene
Reviewers: Ben Gelens, Ravikanth Chaganti, Aleksandar Nikolic

Published: April, 2015

Summary: This document is intended to include process and extensibility to assist engineers who are preparing for the solution. Details should provide best practices as identified by customers and then validated by the product team to ensure recommendations are future facing and considered stable.

© 2015 Microsoft Corporation. All rights reserved. This document is provided "as-is." Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it.
Some examples are for illustration only and are fictitious. No real association is intended or inferred.
This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes. You may modify this document for your internal, reference purposes.
Some information relates to pre-released product which may be substantially modified before it’s commercially released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

Abstract
--------

This document is designed to provide official guidance for anyone planning for a Windows PowerShell Desired State Configuration pull server implementation. A pull server is a simple service that should take only minutes to deploy. Although this document will offer technical how-to guidance that can be used in a deployment, the value of this document is as a reference for best practices and what to think about before deploying.
Readers should have basic familiarity with DSC, and the terms used to describe the components that are included in a DSC deployment. For more information, see the [Windows PowerShell Desired State Configuration Overview](https://technet.microsoft.com/en-us/library/dn249912.aspx)  topic.
As DSC is expected to evolve at cloud cadence, the underlying technology including pull server is also expected to evolve and to introduce new capabilities. This document includes a version table in the appendix that provides references to previous releases and references to future looking solutions to encourage forward-looking designs.
The two major sections of this document:

 - Configuration Planning
 - Installation Guide
 
**Versions of the Windows Management Framework**
The information in this document is intended to apply to Windows Management Framework 5.0. While WMF 5.0 is not required for deploying and operating a pull server, version 5.0 is the focus of this document.

**Windows PowerShell Desired State Configuration**
Desired State Configuration (DSC) is a management platform that enables deploying and managing configuration data by using an industry syntax named the Managed Object Format (MOF) to describe the Common Information Model (CIM). An open source project, Open Management Infrastructure (OMI), exists to further development of these standards across platforms including Linux and network hardware operating systems. For more information, see the [DMTF page linking to MOF specifications](http://dmtf.org/standards/cim) , and [OMI Documents and Source](https://collaboration.opengroup.org/omi/documents.php).

Windows PowerShell provides a set of language extensions for Desired State Configuration that you can use to create and manage declarative configurations.

**Pull server role**
A pull server provides a centralized service to store configurations that will be accessible to target nodes.
 
The pull server role can be deployed as either a Web Server instance or an SMB file share. The web server capability includes an OData interface and can optionally include capabilities for target nodes to report back confirmation of success or failure as configurations are applied. This functionality is useful in environments where there are a large number of target nodes. After configuring a target node (also referred to as a client) to point to the pull server the latest configuration data and any required scripts are downloaded and applied. This can happen as a one-time deployment or as a re-occurring job which also makes the pull server an important asset for managing change at scale. For more information, see [Windows PowerShell Desired State Configuration Pull Servers](https://technet.microsoft.com/en-us/library/dn249913.aspx) and [Push and Pull Configuration Modes](https://technet.microsoft.com/en-us/library/dn249913.aspx).

Configuration planning
======================

For any enterprise software deployment there is information that can be collected in advance to help plan for the correct architecture and to be prepared for the steps required to complete the deployment. The following sections provide information regarding how to prepare and the organizational connections that will likely need to happen in advance.

Software requirements
---------------------

Deployment of a pull server requires the DSC Service feature of Windows Server. This feature was introduced in Windows Server 2012, and has been updated through ongoing releases of Windows Management Framework (WMF).

**Software downloads**
In addition to installing the latest content from Windows Update, there are two downloads considered best practice to deploy a DSC pull server: The latest version of Windows Management Framework, and a DSC module to automate pull server provisioning.

**WMF**
Windows Server 2012 R2 includes a feature named the DSC Service. The DSC Service feature provides the pull server functionality, including the binaries that support the OData endpoint. WMF is included in Windows Server and is updated on an agile cadence between Windows Server releases. [New versions of WMF 5.0](http://aka.ms/wmf5latest)  can include updates to the DSC Service feature. For this reason, it is a best practice to download the latest release of WMF and to review the release notes to determine if the release includes an update to the DSC service feature. You should also review the section of the release notes that indicates whether the design status for an update or scenario is listed as stable or experimental . To allow for an agile release cycle, individual features can be declared stable, which indicates the feature is ready to be used in a production environment even while WMF is released in preview.
Other features that have historically been updated by WMF releases (see the WMF Release Notes for further detail):

 - Windows PowerShell Windows PowerShell Integrated Scripting
 - Environment (ISE) Windows PowerShell Web Services (Management OData
 - IIS Extension)  Windows PowerShell Desired State Configuration (DSC)
 - Windows Remote Management (WinRM) Windows Management Instrumentation (WMI)

**DSC resource**
A pull server deployment can be simplified by provisioning the service using a DSC configuration script. This document includes configuration scripts that can be used to deploy a production ready server node. To use the configuration scripts, a DSC module is required that is not included in Windows Server. The required module name is **xPSDesiredStateConfiguration**, which includes the DSC resource **xDscWebService**. The xPSDesiredStateConfiguration module can be downloaded [here](https://gallery.technet.microsoft.com/xPSDesiredStateConfiguratio-417dc71d).

Use the **Install-Module** cmdlet from the **PowerShellGet** module.

    Install-Module xPSDesiredStateConfiguration

The **PowerShellGet** module will download the module to 

> C:\Program Files\Windows PowerShell\Modules.

|Planning task|
|-|
|Do you have access to the installation files for Windows Server 2012 R2?|
|Will the deployment environment have Internet access to download WMF and the module from the online gallery?|
|How will you install the latest security updates after installing the operating system?|
|Will the environment have Internet access to obtain updates, or will it have a local Windows Server Update Services (WSUS) server?|
|Do you have access to Windows Server installation files that already include updates through offline injection?|

Hardware requirements
---------------------

Pull server deployments are supported on both physical and virtual servers. The sizing requirements for pull server align with the requirements for Windows Server 2012 R2.

CPU: 1.4 GHz 64-bit processor
Memory: 512 MB
Disk Space: 32 GB
Network: Gigabit Ethernet Adapter

**Hardware process planning**
|Planning task|
|-|
|Will you deploy on physical hardware or on a virtualization platform?|
|What is the process to request a new server for your target environment?|
|What is the average turnaround time for a server to become available?|
|What size server will you request?|

Accounts
--------

There are no service account requirements to deploy a pull server instance. However there are scenarios where the website could run in the context of a local user account. As an example, if there is a need to access a storage share for website content and either the Windows Server or the device hosting the storage share are not domain joined.

DNS records
-----------

You will need a server name to use when configuring clients to work with a pull server environment. In test environments, typically the server hostname is used, or the IP address for the server can be used if DNS name resolution is not available. In production environments or in a lab environment that is intended to represent a production deployment, the best practice is to create a DNS CNAME record.

A DNS CNAME allows you to create an alias to refer to your host (A) record. The intent of the additional name record is to increase flexibility should a change be required in the future. A CNAME can help to isolate the client configuration so that changes to the server environment, such as replacing a pull server or adding additional pull servers, will not require a corresponding change to the client configuration.

When choosing a name for the DNS record, keep the solution architecture in mind. If using load balancing, the certificate used to secure traffic over HTTPS will need to share the same name as the DNS record. Similarly, if using a highly available file share the virtual name for the cluster would be used.
|Scenario |Best Practice|
|-|-|
|Test Environment |Reproduce the planned production environment, if possible. A server hostname is suitable for simple configurations. If DNS is not available, an IP address may be used in lieu of a hostname.|
|Single Node Deployment |Create a DNS CNAME record that points to the server hostname.|
|Highly Available Deployment |If clients will connect through a load balancing solution, create a hostname for the virtual IP and a CNAME record that references that hostname. If DNS round robin will be used to distribute client requests across pull servers, you must configure the name records to include the host names of all deployed pull server instances.|

For more information, see [Configuring DNS Round Robin in Windows Server](https://technet.microsoft.com/en-us/library/cc787484(v=ws.10).aspx).

|Planning task|
|-|
|Do you know who to contact to have DNS records created and changed?|
|What is the average turnaround for a request for a DNS record?|
|Do you need to request static Hostname (A) records for servers?|
|What will you request as a CNAME?|
|If needed, what type of Load Balancing solution will you utilize? (see section titled Load Balancing for details)|

Public Key Infrastructure
-------------------------

Most organizations today require that network traffic, especially traffic that includes such sensitive data as how servers are configured, must be validated and/or encrypted during transit. While it is possible to deploy a pull server using HTTP which facilitates client requests in clear text, it is a best practice to secure traffic using HTTPS. The service can be configured to use HTTPS using a set of parameters in the DSC resource **xPSDesiredStateConfiguration**.
The certificate requirements to secure HTTPS traffic for pull server are not different than securing any other HTTPS web site. The **Web Server** template in a Windows Server Certificate Services satisfies the required capabilities.
|Planning task|
|-|
|If certificate requests are not automated, who will you need to contact to requests a certificate?|
|What is the average turnaround for the request?|
|How will the certificate file be transferred to you?|
|How will the certificate private key be transferred to you?|
|How long is the default expiration time?|
|Have you settled on a DNS name for the pull server environment, that you can use for the certificate name?|

Choosing an architecture
------------------------
A pull server can be deployed using either a web service hosted on IIS, or an SMB file share. In most situations, the web service option will provide greater flexibility. It is not uncommon for HTTPS traffic to traverse network boundaries, whereas SMB traffic is often filtered or blocked between networks. The web service also offers the option to include a Conformance Server or Web Reporting Manager (both topics to be addressed in a future version of this document) that provide a mechanism for clients to report status back to a server for centralized visibility. SMB provides an option for environments where policy dictates that a web server should not be utilized, and for other environmental requirements that make a web server role undesirable. In either case, remember to evaluate the requirements for signing and encrypting traffic. HTTPS, SMB signing, and IPSEC policies are all options worth considering.

**Designing for high availability**
The pull server role can be deployed in a highly available architecture. The web service role can be load balanced and the files and folders that include DSC modules and DSC configurations can be located on highly available storage.

Bear in mind that once configurations and modules are delivered to a target node all data required to perform tests and to set configurations is stored locally on each node. Only changes are delivered from the pull server. A service outage for a pull server would not be an interruption unless deployments are active.  Typically, high availability is only warranted for the largest of environments.

Configuring a highly available pull server environment requires decisions about how to distribute client requests across multiple server nodes, and how to share the required server files across those nodes.

**Load balancing**
Clients interacting with the web service make a request for information that is returned in a single response. No sequential requests are required, so it is not necessary for the load balancing platform to ensure sessions are maintained on a single server at any point in time.

|Planning task|
|-|
|What solution will be used for load balancing traffic across servers?|
|If using a hardware load balancer, who will take a request to add a new configuration to the device?|
|What is the average turnaround for a request to configure a new load balanced web service?|
|What information will be required for the request?|
|Will you need to request an additional IP or will the team responsible for load balancing handle that?|
|Do you have the DNS records needed, and will this be required by the team responsible for configuring the load balancing solution?|
|Does the load balancing solution require that PKI be handled by the device or can it load balance HTTPS traffic as long as there are no session requirements?|

Shared storage
--------------
In a highly available scenario where multiple servers are configured as pull servers and connections are load balanced across them, it is critical that the resources and configurations available from those servers be identical. The best way to accomplish this is to store this content on a highly available location such as a clustered file share. The location of the share can be specified in the configuration for each server.For more information about shared storage options, see Scale-Out File Server for Application Data Overview .

|Planning task|
|-|
|What solution will be used to host the highly available share?|
|Who will handle the request for a new highly available share?|
|What is the average turnaround time for a highly available share to be available?|
|What information will the teams responsible for storage and/or clustering need?|
