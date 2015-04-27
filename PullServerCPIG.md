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



