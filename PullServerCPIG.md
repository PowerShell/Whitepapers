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

Windows PowerShell Desired State Configuration
----------------------------------------------

Desired State Configuration (DSC) is a management platform that enables deploying and managing configuration data by using an industry syntax named the Managed Object Format (MOF) to describe the Common Information Model (CIM). An open source project, Open Management Infrastructure (OMI), exists to further development of these standards across platforms including Linux and network hardware operating systems. For more information, see the [DMTF page linking to MOF specifications](http://dmtf.org/standards/cim) , and [OMI Documents and Source](https://collaboration.opengroup.org/omi/documents.php).

Windows PowerShell provides a set of language extensions for Desired State Configuration that you can use to create and manage declarative configurations.

Pull server role
----------------

A pull server provides a centralized service to store configurations that will be accessible to target nodes.
 
The pull server role can be deployed as either a Web Server instance or an SMB file share. The web server capability includes an OData interface and can optionally include capabilities for target nodes to report back confirmation of success or failure as configurations are applied. This functionality is useful in environments where there are a large number of target nodes. After configuring a target node (also referred to as a client) to point to the pull server the latest configuration data and any required scripts are downloaded and applied. This can happen as a one-time deployment or as a re-occurring job which also makes the pull server an important asset for managing change at scale. For more information, see [Windows PowerShell Desired State Configuration Pull Servers](https://technet.microsoft.com/en-us/library/dn249913.aspx) and [Push and Pull Configuration Modes](https://technet.microsoft.com/en-us/library/dn249913.aspx).

Configuration planning
----------------------

For any enterprise software deployment there is information that can be collected in advance to help plan for the correct architecture and to be prepared for the steps required to complete the deployment. The following sections provide information regarding how to prepare and the organizational connections that will likely need to happen in advance.

Software requirements
---------------------

Deployment of a pull server requires the DSC Service feature of Windows Server. This feature was introduced in Windows Server 2012, and has been updated through ongoing releases of Windows Management Framework (WMF).

Software downloads
------------------

In addition to installing the latest content from Windows Update, there are two downloads considered best practice to deploy a DSC pull server: The latest version of Windows Management Framework, and a DSC module to automate pull server provisioning.

WMF
---

Windows Server 2012 R2 includes a feature named the DSC Service. The DSC Service feature provides the pull server functionality, including the binaries that support the OData endpoint. WMF is included in Windows Server and is updated on an agile cadence between Windows Server releases. [New versions of WMF 5.0](http://aka.ms/wmf5latest)  can include updates to the DSC Service feature. For this reason, it is a best practice to download the latest release of WMF and to review the release notes to determine if the release includes an update to the DSC service feature. You should also review the section of the release notes that indicates whether the design status for an update or scenario is listed as stable or experimental . To allow for an agile release cycle, individual features can be declared stable, which indicates the feature is ready to be used in a production environment even while WMF is released in preview.
Other features that have historically been updated by WMF releases (see the WMF Release Notes for further detail):

 - Windows PowerShell Windows PowerShell Integrated Scripting
 - Environment (ISE) Windows PowerShell Web Services (Management OData
 - IIS Extension)  Windows PowerShell Desired State Configuration (DSC)
 - Windows Remote Management (WinRM) Windows Management Instrumentation (WMI)

DSC resource
------------

A pull server deployment can be simplified by provisioning the service using a DSC configuration script. This document includes configuration scripts that can be used to deploy a production ready server node. To use the configuration scripts, a DSC module is required that is not included in Windows Server. The required module name is **xPSDesiredStateConfiguration**, which includes the DSC resource **xDscWebService**. The xPSDesiredStateConfiguration module can be downloaded [here](https://gallery.technet.microsoft.com/xPSDesiredStateConfiguratio-417dc71d).

Use the **Install-Module** cmdlet from the **PowerShellGet** module.

    Install-Module xPSDesiredStateConfiguration

The **PowerShellGet** module will download the module to 

> C:\Program Files\Windows PowerShell\Modules.



