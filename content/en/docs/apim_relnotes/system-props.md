---
title: API Gateway and API Manager 7.7 January 2020 Release Notes
linkTitle: API Gateway and API Manager January 2020
weight: 90
date: 2019-09-20
---

## Summary

API Gateway is available as a software installation or a virtualized deployment in Docker containers. API Manager is a licensed product running on top of API Gateway, and has the same deployment options as API Gateway.

The software installation is available on Linux. For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).

Docker deployment is supported on Linux. For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/docker_scripts_prereqs/).

API R&D : JVM System Properties - WIP  

1.  [API R&D](index.html)

API R&D : JVM System Properties - WIP
=====================================

Created by David Shiel, last modified on Sep 08, 2020

[![](https://jira.axway.com/secure/viewavatar?size=xsmall&avatarId=14701&avatarType=issuetype)RDAPI-20971 ](https://jira.axway.com/browse/RDAPI-20971?src=confmacro) - List all of the system property changes made since 7.5.3 SP1 Dev in progress 
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

/\* <!\[CDATA\[\*/ div.rbtoc1599559088812 {padding: 0px;} div.rbtoc1599559088812 ul {list-style: disc;margin-left: 0px;} div.rbtoc1599559088812 li {margin-left: 0px;padding-left: 0px;} /\*\]\]>\*/

*   [7.5.3](#JVMSystemPropertiesWIP-7.5.3)
*   [7.5.3.SP1](#JVMSystemPropertiesWIP-7.5.3.SP1)
*   [7.5.3.SP2](#JVMSystemPropertiesWIP-7.5.3.SP2)
*   [7.5.3.SP3](#JVMSystemPropertiesWIP-7.5.3.SP3)
*   [7.5.3.SP4](#JVMSystemPropertiesWIP-7.5.3.SP4)
*   [7.5.3.SP5](#JVMSystemPropertiesWIP-7.5.3.SP5)
*   [7.5.3.SP6](#JVMSystemPropertiesWIP-7.5.3.SP6)
*   [7.5.3.SP7](#JVMSystemPropertiesWIP-7.5.3.SP7)
*   [7.5.3.SP8](#JVMSystemPropertiesWIP-7.5.3.SP8)
*   [7.5.3.SP9](#JVMSystemPropertiesWIP-7.5.3.SP9)
*   [7.5.3.SP10](#JVMSystemPropertiesWIP-7.5.3.SP10)
*   [7.5.3.SP11](#JVMSystemPropertiesWIP-7.5.3.SP11)
*   [7.5.3.SP12](#JVMSystemPropertiesWIP-7.5.3.SP12)
*   [7.5.3.SP13](#JVMSystemPropertiesWIP-7.5.3.SP13)
*   [Post 7.5.3.SP13 Patches](#JVMSystemPropertiesWIP-Post7.5.3.SP13Patches)
*   [7.6.2](#JVMSystemPropertiesWIP-7.6.2)
*   [7.6.2.SP1](#JVMSystemPropertiesWIP-7.6.2.SP1)
*   [7.6.2.SP2](#JVMSystemPropertiesWIP-7.6.2.SP2)
*   [7.6.2.SP3](#JVMSystemPropertiesWIP-7.6.2.SP3)
*   [7.6.2.SP4](#JVMSystemPropertiesWIP-7.6.2.SP4)
*   [7.6.2.SP5](#JVMSystemPropertiesWIP-7.6.2.SP5)
*   [7.7.0](#JVMSystemPropertiesWIP-7.7.0)
*   [7.7.0.SP1](#JVMSystemPropertiesWIP-7.7.0.SP1)
*   [7.7.0.SP2](#JVMSystemPropertiesWIP-7.7.0.SP2)
*   [7.7.0.SP20200131](#JVMSystemPropertiesWIP-7.7.0.SP20200131)
*   [7.7.0.SP20200330](#JVMSystemPropertiesWIP-7.7.0.SP20200330)
*   [7.7.0.SP20200530](#JVMSystemPropertiesWIP-7.7.0.SP20200530)
*   [7.7.0.SP20200730](#JVMSystemPropertiesWIP-7.7.0.SP20200730)




---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Overview
--------

Traditionally, the JVM.XML file has allowed customers to tweak the behaviour of the API Gateway by defining Java System Properties.

For example, the following snippet from the [system/conf/jvm.xml](https://git.ecd.axway.int/apigw/vordel/-/blob/master/install/system/conf/jvm.xml) file defines the factories used by the Gateway when processing XML documents

    <!-- Default XSLT engine - must be XSLT 1.0 compatible -->
    <SystemProperty name="javax.xml.transform.TransformerFactory" value="com.vordel.common.xml.secure.SecureTransformerFactory" />
    <SystemProperty name="com.vordel.common.xml.secure.SecureTransformerFactory.Impl" value="org.apache.xalan.xsltc.trax.TransformerFactoryImpl" />
    <SystemProperty name="javax.xml.parsers.SAXParserFactory" value="com.vordel.common.xml.secure.SecureSAXParserFactory" />
    <SystemProperty name="javax.xml.stream.XMLInputFactory" value="com.vordel.common.xml.secure.SecureXMLInputFactory" />

The codebase is peppered with calls to the following methods, where the Java System Properties being used can alter behaviour:

*   [System.getProperty()](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)
*   System.getProperties()
*   [Boolean.getBoolean()](https://docs.oracle.com/javase/7/docs/api/java/lang/Boolean.html#getBoolean(java.lang.String))
*   [Integer.getInteger()](https://docs.oracle.com/javase/7/docs/api/java/lang/Integer.html#getInteger(java.lang.String))
*   [Long.getLong()](https://docs.oracle.com/javase/7/docs/api/java/lang/Long.html#getLong(java.lang.String))

A list of all Java System Properties picked up by the JVM can be retrieved by running the following command:

java -XshowSettings:properties

This document aims to list all the instances where an Axway-defined property is used, to explain the purpose of those properties and the context in which they are employed.

Axway-defined Java System Properties
------------------------------------

The following tables list all Axway-defined Java System Properties that exist, per version, from 7.5.3 up to the present day (i.e. master as of 16th July 2020).

The first table listed will contain the bulk of the entries, with each subsequent table documenting newly added properties introduced in that specific version.

Moving down the list, the assumption is that properties listed in previous tables are inherited.

### 7.5.3

#### Synopsis

  

Distinct Java System Properties

100

Git tag

[v\_7\_5\_3](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3)

checkout command

git checkout tags/v\_7\_5\_3

#### Detail

The following tables lists all Java System Properties that exist in the code up to and including the 7.5.3 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

OAUTH\_DIGEST\_ALGORITHM

[com/vordel/circuit/oauth/AbstractOAuthStore.java; L20](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/persistence/AbstractOAuthStore.java#L20)

[com/vordel/circuit/oauth/common/OAuth2Utils.java; L141](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/common/OAuth2Utils.java#L141)

[com/vordel/circuit/oauth/common/OAuth2Utils.java; L157](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/common/OAuth2Utils.java#L157)

SHA1

Specifies the digest algorithm used to store and retrieve tokens from a datastore.  
When a token is generated it will be hashed with the specified algorithm for storage and when a token is received the token value is hashed to retrieve the token properties.  
It is also used to store OAuth request "state" during a three-legged flow. A state ID is generated and the state of a request is stored in a cache against the hash of the state ID. The state can be retrieved in a later request by hashing the state ID and looking it up in he cache.

API Gateway; OAuth

2

oam.compatibility.mode

[com/vordel/circuit/oracle/AccessManagerAuthNProcessor.java; L35](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oracle/AccessManagerAuthNProcessor.java#L35)

[com/vordel/circuit/oracle/AccessManagerProcessor.java; L42](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oracle/AccessManagerProcessor.java#L42)

OAM\_11G

Specifies the Oracle Access Manager server version to which the Oracle Access Manager filter connects. Possible values: OAM\_10G, OAM\_11G

API Gateway; Oracle Access Manager; Filter Runtime

3

oam.install.dir

[com/vordel/circuit/oracle/AccessManagerProcessor.java; L31](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oracle/AccessManagerProcessor.java#L31)

  

Specifies the path to the OAM Access SDK directory

API Gateway; Oracle Access Manager; Filter Runtime

4

apiconnector.http.proxyHost

[com/vordel/apiportal/api/connector/ApiConnectorHelperImpl.java; L67](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/connector/ApiConnectorHelperImpl.java#L67)

  

Specifies the HTTP proxy host to be used by the API Manager Connector

API Manager; Connectors

5

apiconnector.http.proxyPort

[com/vordel/apiportal/api/connector/ApiConnectorHelperImpl.java; L68](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/connector/ApiConnectorHelperImpl.java#L68)

  

Specifies the HTTP proxy port to be used by the API Manager Connector

API Manager; Connectors

6

ApiQuotaController.SystemName

[com/vordel/apiportal/api/portal/controller/ApiQuotaController.java; L50](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiQuotaController.java#L50)

System

Specifies the name of the global system-level quota in API Manager

API Manager; Quotas

7

ApiQuotaController.SystemDescription

[com/vordel/apiportal/api/portal/controller/ApiQuotaController.java; L52](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiQuotaController.java#L52)

Maximum message rates aggregated across all client applications

Specifies the description of the global system-level quota in API Manager

API Manager; Quotas

8

ApiQuotaController.ApplicationName

[com/vordel/apiportal/api/portal/controller/ApiQuotaController.java; L55](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiQuotaController.java#L55)

Application Default

Specifies the name of the default application-level quota in API Manager

API Manager; Quotas

9

ApiQuotaController.ApplicationDescription

[com/vordel/apiportal/api/portal/controller/ApiQuotaController.java; L56](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiQuotaController.java#L56)

Maximum message rates per application.

Applied to each application unless an Application-Specific quota is configured

Specifies the description of the default application-level quota in API Manager

API Manager; Quotas

10

cache.results

[com/vordel/circuit/attribute/AttributeExtractBaseProcessor.java; L123](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/attribute/AttributeExtractBaseProcessor.java#L123)

true

Specifies whether or not the 'Extract Attribute' filters (Database, LDAP) should cache their results.

API Gateway; Filter Runtime; Database; LDAP

11

auditLogOffloadCron

[com/vordel/audit/service/AuditLogOffloader.java; L172](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/audit/service/AuditLogOffloader.java#L172)

0 0/5 \* ? \* \* \*

Specifies the CRON expression indicating the schedule for the audit log upload. Audit settings, including host and port of the remote server to which the logs will be uploaded, are read from **`/system/conf/auditSettings.json`**

Node Manager; Logging; Audit

12

OAUTH\_AUTHZ\_CODE\_LENGTH

[com/vordel/circuit/oauth/token/AuthorizationCode.java; L46](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/token/AuthorizationCode.java#L46)

30

Specifies the length of the generated OAuth authorization code.

**Not used**

API Gateway; OAuth

13

OAUTH\_AUTHZ\_EXPIRATION\_SECS

[com/vordel/circuit/oauth/token/AuthorizationCode.java; L48](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/token/AuthorizationCode.java#L48)

20

Number of seconds in which an authorization code should expire

API Gateway; OAuth

14

PRETTY\_PRINT\_KPS

[com/vordel/kps/admin/BackupStore.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/kps/admin/BackupStore.java#L26)

[com/vordel/kps/json/Json.java; L301](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/kps/json/Json.java#L301)

true

Specifies whether or not to pretty-print JSON when backuping KPS tables

KPSAdmin

15

CASSANDRA\_RETRY\_MAX\_DELAY\_MS

[com/axway/apigw/cassandra/impl/datastax/ClusterConnectionPoolService.java; L38](https://git.ecd.axway.org/apigw/cassandra-module/-/blob/master/cassandra-datastax-impl/src/main/java/com/axway/apigw/cassandra/impl/datastax/ClusterConnectionPoolService.java#L38)

40000

Specifies the maximum delay in milliseconds between reconnection attempts to Cassandra

Cassandra

16

THROTTLING\_RECORT\_TTL

[com/axway/apigw/cassandra/api/throttling/ThrottlingConstants.java; L15](https://git.ecd.axway.org/apigw/cassandra-module/-/blob/master/cassandra-api/src/main/java/com/axway/apigw/cassandra/api/throttling/ThrottlingConstants.java#L15)

20

Specifies the amount of time to wait, in seconds, before garbage collecting tombstones (deletion markers). For a single-node cluster this value can be safely set to zero.

Cassandra

17

api.manager.querystring.passthrough

[com/vordel/coreapireg/runtime/CoreApiBroker.java; L244](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/coreapireg/runtime/CoreApiBroker.java#L244)

[com/vordel/coreapireg/runtime/broker/InvokableMethod.java; L362](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/coreapireg/runtime/broker/InvokableMethod.java#L362)

[com/vordel/apiportal/runtime/DefaultSecurityDevice.java; L90](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/runtime/DefaultSecurityDevice.java#L90)

[com/vordel/dwe/http/HTTPPlugin.java; L310](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/http/HTTPPlugin.java#L310)

[com/vordel/circuit/http/HTTPUtil.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/http/HTTPUtil.java#L26)

  

If set to true, query parameters are sent unmodified to the backend service, rather than performing any parameter mapping that may have been configured.

API Manager; HTTP

18

ALLOW\_NULL\_VALUES\_FROM\_DB

[com/vordel/common/db/DbConnection.java; L77](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/db/DbConnection.java#L77)

false

Allow NULL values to be included in Database query resultsets

Database

19

remoteTraceLimit

[com/vordel/api/configuration/DeployConsoleTraceDestination.java; L43](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/api/configuration/DeployConsoleTraceDestination.java#L43)

1000

Sets the maximum number of lines that can be present in the deployment trace (which is displayed in PolicyStudio post-deployment)

Trace; Logging

20

com.vordel.jaxtrace.realprovider

[com/vordel/jaxtrace/DocumentBuilderFactoryImpl.java; L18](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/jaxtrace/DocumentBuilderFactoryImpl.java#L18)

  

**Not sure if this is used**

  

21

DOMAIN\_STATUS\_INTERVAL

[com/vordel/api/domain/DomainStatusThread.java; L36](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/api/domain/DomainStatusThread.java#L36)

120

Specifies the interval, in seconds, that the domain status is checked. If the Topology or AdminUsers files are out of sync an attempt is made to repair the domain. Note, only nodes marked as 'alive' are taken into account when repairing.

  

22

OAUTH\_ERROR\_PROPERTIES\_FILE

[com/vordel/circuit/oauth/provider/grants/Errors.java; L53](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/provider/grants/Errors.java#L53)

com/vordel/circuit/oauth/provider/grants/errors.properties

Specifies the location of a property file that contains a list of error details pertaining to OAuth grants. This allows customers to override the error response strings. The file must contain the following property names:

*   invalid\_request
*   unauthorized\_client
*   access\_denied
*   unsupported\_response\_type
*   invalid\_scope
*   server\_error
*   temporarily\_unavailable
*   invalid\_client
*   invalid\_grant
*   unsupported\_grant\_type
*   invalid\_token
*   insufficient\_scope
*   interaction\_required
*   login\_required
*   account\_selection\_required
*   consent\_required
*   invalid\_request\_uri
*   invalid\_request\_object
*   request\_not\_supported
*   request\_uri\_not\_supported
*   registration\_not\_supported

API Gateway; OAuth

23

smreghost

[com/vordel/client/rcp/common/util/ExecuteSMReghost.java; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/ExecuteSMReghost.java#L40)

[com/vordel/client/rcp/common/util/ExecuteSMReghost.java; L41](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/ExecuteSMReghost.java#L41)

[com/vordel/client/rcp/common/util/ExecuteSMReghost.java; L54](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/ExecuteSMReghost.java#L54)

  

**Unused**

  

24

VINSTDIR

[com/vordel/client/rcp/common/util/ExecuteSMReghost.java; L138](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/ExecuteSMReghost.java#L138)

[com/vordel/circuit/siteminder/JavaTestClient.java; L187](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/filters/siteminder/rt/src/main/java/com/vordel/circuit/siteminder/JavaTestClient.java#L187)

[com/vordel/build/LoadJavaKeystore.java; L112](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/gwcomponents/config/src/main/java/com/vordel/build/LoadJavaKeystore.java#L112)

[com/vordel/security/antivirus/McAfeeScanner.java; L52](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/security/antivirus/McAfeeScanner.java#L52)

[com/vordel/common/util/ModuleClassLoader.java; L87](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/util/ModuleClassLoader.java#L87)

[com/vordel/common/util/ModuleClassLoader.java; L198](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/util/ModuleClassLoader.java#L198)

[com/vordel/common/util/ProcessExecutor.java; L22](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/util/ProcessExecutor.java#L22)

[com/vordel/circuit/tivoli/TivoliAuthorization.java; L105](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/tivoli/TivoliAuthorization.java#L105)

[com/vordel/common/util/VSConfigUtil.java; L166](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/util/VSConfigUtil.java#L166)

  

VINSTDIR, the intance directory.

Specifies the location of the running server instance. For an API Gateway this is the location where the server will run from, normally located in the **`/groups/<group-id>/<instance-id>`** directory. For a Node Manager this is the location where the product has been installed.

  

25

showclasses

[com/vordel/client/rcp/common/circuit/part/FilterPart.java; L68](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/circuit/part/FilterPart.java#L68)

[com/vordel/client/rcp/common/circuit/palette/PaletteManager.java; L221](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/circuit/palette/PaletteManager.java#L221)

  

In the policy view/pane, when enabled the internal Java class associated with a Filter is displayed on the filter tooltip.

Internal; PolicyStudio

26

ftpPassivePorts

[com/vordel/fileservice/ftp/FTPServer.java; L170](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/fileservice/ftp/FTPServer.java#L170)

  

Specifies the passive ports to be used for passive mode FTP data connections. Ports can be defined as single ports, closed or open ranges. Multiple definitions can be separated by commas, for example:

*   **2300** : only use port 2300 as the passive port
*   **2300-2399** : use all ports in the range
*   **2300-** : use all ports larger than 2300
*   **2300, 2305, 2400-** : use 2300 or 2305 or any port larger than 2400

API Gateway; FTP

27

ftpPassiveAddress

[com/vordel/fileservice/ftp/FTPServer.java; L171](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/fileservice/ftp/FTPServer.java#L171)

  

Specifies the address used for passive FTP connections

API Gateway; FTP

28

ftpPassiveExternalAddress

[com/vordel/fileservice/ftp/FTPServer.java; L172](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/fileservice/ftp/FTPServer.java#L172)

  

Specifies the passive address that will be returned to clients on the PASV command

API Gateway; FTP

29

com.vordel.strictUriSyntaxChecking

[com/vordel/dwe/http/HTTPPlugin.java; L67](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/http/HTTPPlugin.java#L67)

true

When set to true (the default), on receiving a HTTP request the request URI is parsed as per RFC 2396, primarily in order to validate against the list of excluded characters as listed here:  [https://tools.ietf.org/html/rfc2396#section-2.4.3](https://tools.ietf.org/html/rfc2396#section-2.4.3)

API Gateway; HTTP

30

upgraderef

[com/vordel/client/gui/policystudio/actions/ImportWizardAction.java; L149](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/actions/ImportWizardAction.java#L149)

  

Specifies the URL to an entity store used as a reference store for type information (i.e. type definitions) during the import of a fragment.

Internal; Policy Studio

31

sslContextProtocol

[com/vordel/jce/JCEProvider.java; L153](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/gwplatform/jce/jce-core/src/main/java/com/vordel/jce/JCEProvider.java#L153)

TLSv1.3 (in 7.5.3 this defaulted to TLSv1.2)

Specifies the SSL protocol used by any Node Manager-related clients

*   When API Manager interfaces with the NodeManager router API to glean topology and virtualized service information
*   Client scripts that use Node Manager APIs; e.g. KPSAdmin, deploy\_fragment, setup-apimanager
*   Policy Studio

API Gateway; API Manager; Node Manager; Policy Studio; SSL

32

JMS\_ROLLBACK\_ENABLED

[com/vordel/dwe/jms/JMSConsumer.java; L511](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSConsumer.java#L511)

true

Specifies whether or not to rollback/recover JMS messages read by the consumer.

API Gateway; JMS

33

JMS\_CONNECTION\_THREAD\_PATTERN

[com/vordel/dwe/jms/JMSService.java; L109](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSService.java#L109)

(?i)weblogic

Specifies the regex pattern to use to determine whether or not to launch a separate thread to start the JMS session

API Gateway; JMS

34

avoidLDAPQueryEscaping

[com/vordel/common/ldap/LdapSearchFilterEscaper.java; L27](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/ldap/LdapSearchFilterEscaper.java#L27)

true

Specifies whether or not the following characters are escaped when the API Gateway performs an LDAP query:

*   \* → \\2a
*   ( → \\28
*   ) → \\29
*   \\ → \\5c
*   NUL → \\00

API Gateway; LDAP

35

OAUTH\_NONCE\_LENGTH

[com/vordel/circuit/oauth/common/MacToken.java; L31](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/common/MacToken.java#L31)

15

Specifies the length of the nonce employed as part of the OAuth MAC (Message Authentication Code) Token.

**Not used**

API Gateway; OAuth

36

connectionFactoryNames

[samples/messaging/src/com/vordel/client/messaging/MessagingClient.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/install/samples/messaging/src/com/vordel/client/messaging/MessagingClient.java#L49)

connectionFactory, xAConnectionFactory, queueConnectionFactory, topicConnectionFactory

Specifies the connection factories that the sample client should use when creating a JMS connection

  

37

demo.hours

[com/vordel/reporting/demo/MetricsDemo.java; L35](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/reporting/demo/MetricsDemo.java#L35)

[com/vordel/reporting/demo/TrafficDataGenerator.java; L451](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/reporting/demo/TrafficDataGenerator.java#L451)

  

Number of hours of historical data to generate for the metrics demo. If this property is not present, the demo is disabled.

Analytics; Demo

38

demo.apiMgr

[com/vordel/reporting/demo/MetricsDemo.java; L38](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/reporting/demo/MetricsDemo.java#L38)

[com/vordel/reporting/demo/TrafficDataGenerator.java; L436](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/reporting/demo/TrafficDataGenerator.java#L436)

false

Indicates that metrics data should appear to come from API Manager. If absent or set to 'false', data will appear to come from a normal API Gateway.

API Manager; Analytics; Demo

39

kingsOfSOA

[com/vordel/client/rcp/navigator/tree/NetServiceTreeItem.java; L78](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-navigator/src/com/vordel/client/rcp/navigator/tree/NetServiceTreeItem.java#L78)

  

Displays an additional 'Testing' item on the context menu of the 'API Gateway' tree node; provides a shortcut to all the available filter dialogs for quick testing

Internal; Policy Studio; Testing

40

OAUTH\_ACCESS\_TOKEN\_EXPIRATION\_SECS

[com/vordel/circuit/oauth/token/OAuth2AccessToken.java; L60](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/token/OAuth2AccessToken.java#L60)

43200

Specifies the length of time, in seconds, that an OAuth access token is valid for.

Not currently used in the API Gateway, but exists if customers wish to generate a refresh token from scripts/policies using  **`OAuth2RefreshToken.generate(length)`**

API Gateway; OAuth

41

OAUTH\_REFRESH\_TOKEN\_EXPIRATION\_SECS

[com/vordel/circuit/oauth/token/OAuth2RefreshToken.java; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/token/OAuth2RefreshToken.java#L23)

2592000

Specifies the length of time, in seconds, that an OAuth refresh token is valid for.

Not currently used in the API Gateway, but exists if customers wish to generate an access token from scripts/policies using OAuth2AccessToken **`.generate(length)`**

API Gateway; OAuth

42

com.axway.oauth.acceptRequestScopes

[com/vordel/circuit/oauth/provider/authorize/OAuthAuthorizationRequestProcessor.java; L164](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/oauth/provider/authorize/OAuthAuthorizationRequestProcessor.java#L164)

false

Specifies whether or not scopes present in the request are automatically accepted, if valid for client.

**Note**: This setting has been incorporated into the OAuth 2.0 Authorization Code Flow filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OAuth

43

dochelper

[com/vordel/client/rcp/common/circuit/palette/PaletteManager.java; L238](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/circuit/palette/PaletteManager.java#L238)

  

For debugging purposes, stores the help IDs of the filter pages associated with an item in the filter palette.

Policy Studio; Internal

44

openTrafficSubdirGranularity

[com/vordel/opentraffic/PayloadStorageImplFilesystem.java; L64](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/opentraffic/PayloadStorageImplFilesystem.java#L64)

12 (i.e. Calendar.MINUTE)

Specifies...

API Gateway; Logging; Open Traffic

45

openTrafficMaxFilesPerDir

[com/vordel/opentraffic/PayloadStorageImplFilesystem.java; L65](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/opentraffic/PayloadStorageImplFilesystem.java#L65)

5000

Specifies the maximum number of files that can exist in the Open Traffic Event Log storage directory.

API Gateway; Logging; Open Traffic

46

openTrafficWriteMethod

[com/vordel/opentraffic/PayloadStorageImplFilesystem.java; L66](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/opentraffic/PayloadStorageImplFilesystem.java#L66)

ASYNC\_FILE\_CHANNEL

Specifies how Open Traffic Event Logs are written.

*   **ASYNC\_FILE\_CHANNEL**; the default; logs written asynchronously to disk
*   **FILE\_CHANNEL**; logs written synchronously to disk

API Gateway; Logging; Open Traffic

47

openTrafficMaxAsyncOps

[com/vordel/opentraffic/PayloadStorageImplFilesystem.java; L67](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/opentraffic/PayloadStorageImplFilesystem.java#L67)

16

Specifies the maximum number of asynchronous writes that can occur when persisting Open Traffic Event Logs to disk. This property is employed when  **ASYNC\_FILE\_CHANNEL** is enabled.

API Gateway; Logging; Open Traffic

48

openTrafficMaxAsyncCloseOps

[com/vordel/opentraffic/PayloadStorageImplFilesystem.java; L68](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/opentraffic/PayloadStorageImplFilesystem.java#L68)

32

Specifies the maximum number of close operations that can occur when completing the writing of an Open Traffic Event Log; i.e. the maximum number of files that can be closed asynchronously. This property is employed when  **ASYNC\_FILE\_CHANNEL** is enabled.

API Gateway; Logging; Open Traffic

49

com.vordel.tools.swagger.failed.injection.consumer

[com/vordel/tools/swagger/mojo/buddy/preload/Preloader.java; L108](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/tools/swagger/mojo/src/main/java/com/vordel/tools/swagger/mojo/buddy/preload/Preloader.java#L108)

  

**Don't know what this is used for**

Internal; Build; Swagger

50

projectroot

[com\\vordel\\client\\gui\\policystudio\\wizards\\project\\ProjectDetailsPage.java; L288](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/wizards/project/ProjectDetailsPage.java#L288)

apiprojects

Specifies the directory (relative to user.home) where PolicyStudio projects will be stored

Policy Studio

51

sendResponseInReflect

[com/vordel/circuit/net/QaReflectProcessor.java; L102](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/net/QaReflectProcessor.java#L102)

[com/vordel/circuit/net/ReflectProcessor.java; L59](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/net/ReflectProcessor.java#L59)

false

When set to true, the behavior is to send the response as part of the filter processor, as opposed to sending the response after the filter has completed.

API Gateway; Filter Runtime; Reflect

52

caseSensitiveQueryParams

[com/vordel/mime/QueryStringHeaderSet.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/gwplatform/mime/src/main/java/com/vordel/mime/QueryStringHeaderSet.java#L21)

false

Specifies whether or not case-sensitive query-string parameters are supported

Consider the following URL:

[http://localhost:8080/test?hello=world&Hello=world](http://localhost:8080/test?hello=world&Hello=world)

When this property is specified, the Gateway runtime will see two distinct parameters, `**hello**` and `**Hello**` , rather than just one that has two values.

API Gateway; HTTP

53

com.vordel.rbac.policy

[com/vordel/circuit/rbac/RbacUtil.java; L36](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/rbac/RbacUtil.java#L36)

[com/vordel/circuit/rbac/RbacUtil.java; L46](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/rbac/RbacUtil.java#L46)

[upgrade/scripts/rbac/RBAC0Exporter.py; L16](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/upgrade/scripts/rbac/RBAC0Exporter.py#L16)

  

Specifies the ACL file to be used by the RBAC processor. By default the API Gateway uses  **`$VINSTDIR/conf/acl.json`** and the Node Manager uses  `$VDISTDIR/conf/acl.json`, both of which are set in  `$VDISTDIR/system/conf/jvm.xml`.

API Gateway; Node Manager; RBAC

54

javascriptDelay

[com/vordel/reporting/reporter/ScheduledReport.java; L388](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/reporting/reporter/ScheduledReport.java#L388)

300000

Specifies the number of milliseconds to wait for the webpage to load prior to generating the PDF report

  

55

schemaFullChecking

[com/vordel/xsd/SchemaLoaderFactory.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/xsd/SchemaLoaderFactory.java#L49)

true

Specifies whether or not strict schema checking is performed when validating XML schemas

API Gateway; XML; Schema Validation

56

ExpandMissingAttributeInSelectorBehaviour

[com/vordel/el/Selector.java; L71](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/gwplatform/el/src/main/java/com/vordel/el/Selector.java#L71)

see ExpandMissingAttributeBehaviour

When evaluating a selector with string components other than a top-level " **`${..}`** " expansion, the expression evaluator coerces the constituent dynamic parts of the expression to strings, and subsequently returns a string. By default this uses Dictionary.toString() which results " **`[invalid field]`** " being returned.

When this property is specified, the behaviour is as follows:

*   **REPLACE\_LITERAL:foo** expression evaluates to "foo"
*   **EMPTY\_STRING** expression evaluates to ""
*   **THROW\_EXCEPTION** expression evaluates to "null"

API Gateway; Selectors; Message Attributes; Runtime

57

ExpandMissingAttributeBehaviour

[com/vordel/el/Selector.java; L72](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/gwplatform/el/src/main/java/com/vordel/el/Selector.java#L72)

[com/vordel/common/util/WildcardedString.java; L29](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/common/util/WildcardedString.java#L29)

LEAVE\_ATTR

The default value for the ExpandMissingAttributeInSelectorBehaviour system property.

API Gateway; Selectors; Message Attributes; Runtime

58

IGNORE\_ENCODING

[com/vordel/circuit/axway/sentinel/Sentinel.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/axway/sentinel/Sentinel.java#L21)

false

Specifies whether or not to ignore the configured encoding (default being **`utf-8`** ) when configuring the Sentinel server external connection.

  

59

urlEncodeInvalidQueryStringParamChars

[com/vordel/dwe/http/ServerTransaction.java; L57](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/http/ServerTransaction.java#L57)

[com/vordel/dwe/http/ServerTransaction.java; L115](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/http/ServerTransaction.java#L115)

  

Specifies the list of characters that may be deemed invalid for query-string parameters, as per [RFC 2396](https://tools.ietf.org/html/rfc2396); i.e. those characters that raise a URISyntaxException when creating a [java.net](http://java.net).URI. Any of those characters listed are subsequently earmarked for encoding as per [RFC 3986, Section 2.1](https://tools.ietf.org/html/rfc3986#section-2.1), so as to maximise the chances of the URI being processed successfully.

API Gateway; HTTP

60

API\_SERVER\_TRANSACTION\_COOKIE\_NAME

[com/vordel/circuit/session/SessionManager.java; L29](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/session/SessionManager.java#L29)

VIDTXN

Specifies the name of the API Gateway transaction cookie.

API Gateway; Session Cookies

61

API\_SERVER\_USER\_COOKIE\_NAME

[com/vordel/circuit/session/SessionManager.java; L30](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/session/SessionManager.java#L30)

VIDUSR

Specifies the name of the API Gateway user session cookie.

API Gateway; Session Cookies;

62

vordel.trace.level

[com/vordel/ant/SimpleTraceLogger.java; L25](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/ant/SimpleTraceLogger.java#L25)

DEBUG

Used to set the trace level for the logger.

The SimpleTraceLogger appears to be unused in the current codebase. Git blame comments seem to suggest that this logger was used within ant

API Gateway; Trace; Logging

63

vordel.trace.size.kb

[com/vordel/ant/SimpleTraceLogger.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/ant/SimpleTraceLogger.java#L26)

10000

Used to set the maximum trace file size for the logger.

The SimpleTraceLogger appears to be unused in the current codebase. Git blame comments seem to suggest that this logger was used within ant

API Gateway; Trace; Logging

64

vordel.relative.trace.dir

[com/vordel/ant/SimpleTraceLogger.java; L30](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/ant/SimpleTraceLogger.java#L30)

  

Specifies the path to the logger trace file. The path is relative to user.dir.

The SimpleTraceLogger appears to be unused in the current codebase. Git blame comments seem to suggest that this logger was used within ant

API Gateway; Trace; Logging

65

com.axway.sso.domain.name

[com/vordel/apiportal/api/portal/v1\_2/SingleSignOn.java; L323](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/v1_2/SingleSignOn.java#L323)

""; i.e. empty string

Specifies the cookie domain name (i.e. the name of the domain for which the cookie is valid) used for single sign-on. The default value is an empty string, which means the onus is on the browser to construct the domain.

API Manager; Single Sign-on

66

ADD\_SITEMINDER\_LEGACY\_ATTRIBUTES

[com/vordel/circuit/siteminder/SiteMinderUtil.java; L158](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/filters/siteminder/rt/src/main/java/com/vordel/circuit/siteminder/SiteMinderUtil.java#L158)

false

Specifies whether or not to add legacy Siteminder attributes to the message whiteboard. Only Siteminder attributes in the form of a name/value pair are added. The list of attributes is placed in the **`${attribute.lookup.list}`** message property. Individual attributes are added with the prefix **`${user.propertyName}`** where **propertyName** is the name of the Siteminder property.

API Gateway; Filter Runtime; Siteminder; Authentication; 

67

dont.expect.100.continue

[com/vordel/circuit/net/State.java; L38](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/net/State.java#L38)

false

Controls whether or not an **Expect** header, with a value of **100-continue**, is sent as part of a request to a backend service initiated by the API Gateway connection filters

API Gateway; HTTP; Connection; ConnectToUrl

68

version2.TransformerFactory

[com/vordel/circuit/conversion/StylesheetProcessor.java; L62](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/conversion/StylesheetProcessor.java#L62)

  

Specifies the fully qualified name of a provider class from a version 2+ XSLT transformer factory library to be used by the XSLT Transformation filter. This class _must_ be added to the API Gateway's classpath; the simplest way to achieve this is to drop the required JAR file into the **`INSTALL_DIR/apigateway/ext/lib directory`** , where INSTALL\_DIR refers to the root of the API Gateway installation.

The transformer factory is set using the following precedence rules

*   Use the factory specified at the filter-level
*   If not present (or blank), check for version 1 XSLT, in which case use  **`com.sun.org`** **`.apache.xalan.internal.xsltc.trax.TransformerFactoryImpl`**
*   Otherwise, use the factory specified by this property

API Gateway; XSLT Transformation; Stylesheet

69

vordel.policy.wspoliciesDir

[com/vordel/ws/SystemWSPolicies.java; L56](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/ws/SystemWSPolicies.java#L56)

wspolicy/policies

Specifies the directory, relative to $VDISTDIR/system/conf, from which to load WS-Policy constraint information (.properties files loaded and displayed in Policy Studio when configuring WS-Policy for a registered Web Service) and policies (WS-Policy XML documents) that are applied at runtime.

API Gateway; Web Services; WSPolicy

70

VDISTDIR

[com/vordel/circuit/tivoli/TivoliAuthorization.java; L106](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/tivoli/TivoliAuthorization.java#L106)

  

VDISTDIR the distribution directory.

Specifies the location where the product has been installed. For an API Gateway this is the location where the product has been installed.  
For a Node Manager this is the location where the product has been installed.

  

71

cloud.trial.duration

[com/vordel/apiportal/trial/TrialScheduler.java; L142](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/trial/TrialScheduler.java#L142)

  

API Manager Cloud Trial; Specifies the amount of time, in days, that the API Manager Trial will last

API Manager; Trial

72

ATTRIBUTE\_CACHE\_SIZE

[com/vordel/circuit/attribute/UserAttributeCache.java; L25](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/attribute/UserAttributeCache.java#L25)

100

Specifies the maximum cache size employed when reading user attributes (LDAP/Database)

**Does not appear to be used any more**

  

73

file.upload.ignore

[com/vordel/fileservice/integration/VordelFileService.java; L579](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/fileservice/integration/VordelFileService.java#L579)

.\*\\\\.part$

The regular expression that specifies the files to be ignored during an FTP transfer to the API Gateway. Ignores .part suffixes by default as file upload filter uses .part as a temporary filename before renaming.

API Gateway; FTP

74

internal.keep.temp.cert.files

[jython/src/bin/cert.py; L464](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/cert.py#L464)

[jython/src/bin/cert.py; L597](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/cert.py#L597)

[jython/src/bin/cert.py; L833](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/cert.py#L833)

[jython/src/bin/cert.py; L888](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/cert.py#L888)

[jython/src/bin/cert.py; L1054](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/cert.py#L1054)

[jython/src/bin/diskinstancemanager.py; L414](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/diskinstancemanager.py#L414)

[jython/src/bin/mdhost.py; L292](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/mdhost.py#L292)

[jython/src/bin/openssl.py; L77](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/jython/src/bin/openssl.py#L77)

  

Specifies whether or not temporary certificate files, generated as part of Node Manager and API Gateway registration, remain on disk post setup.

Internal; API Gateway; Node Manager; Domain; Certificates

75

showesx

[com/vordel/client/gui/policystudio/ApplicationActionBarAdvisor.java; L480](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/ApplicationActionBarAdvisor.java#L480)

[com/vordel/client/gui/policystudio/cs/CSActionBarAdvisor.java; L231](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/cs/CSActionBarAdvisor.java#L231)

false

When true, adds an additional item to the Window menu, allowing users to launch ESExplorer as an embedded view within PolicyStudio.

Internal; Policy Studio

76

auditLogOffloadNeverDuplicate

[com/vordel/audit/service/AuditLogOffloadJob.java; L232](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/audit/service/AuditLogOffloadJob.java#L232)

false

When set to _true_, specifies that if during an audit log rollover an error occurs (for example, the API Gateway instance is down), then the current audit log is not uploaded.

API Gateway; Logging; Audit

77

showWorkspaceView

[com/vordel/client/gui/policystudio/perspective/ConfigurationPerspective.java; L36](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/perspective/ConfigurationPerspective.java#L36)

  

When true, allows the Workspace view to be displayed in PolicyStudio, primarily to help with issues regarding the internal workings of Visual Mapper.

Internal; Policy Studio

78

showEnvDecoratorColor

[com/vordel/client/manager/attr/EnvironmentSettingControlDecoration.java; L62](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-filter-base/src/com/vordel/client/manager/attr/EnvironmentSettingControlDecoration.java#L62)

  

Changes the background color of dialog panels to identify/highlight margin overruns caused by environmentalization decorations

Internal; Policy Studio

79

jmsReConnectOnConnFailure

[com/vordel/dwe/jms/JMSService.java; L380](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSService.java#L380)

true

Specifies whether a reconnection to the JMS server is performed when the configured JMS provider raises a connection error.

JMS

80

jmsConnStartupAsync

[com/vordel/dwe/jms/JMSService.java; L385](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSService.java#L385)

false

Specifies whether the first JMS connection attempt is detached from the API Gateway startup sequence. When true, the API Gateway starts even if the JMS connection cannot be established

JMS

81

jersey.logging.on

[com/vordel/client/gui/policystudio/wizards/widgets/LoginPage.java; L418](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/wizards/widgets/LoginPage.java#L418)

[com/vordel/client/gui/policystudio/manager/ManagerRCP.java; L380](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/manager/ManagerRCP.java#L380)

  

Ask Clive

Policy Studio

82

perform.unresolved.check

[com/vordel/client/gui/policystudio/cs/actions/OpenFileAction.java; L87](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/cs/actions/OpenFileAction.java#L87)

false

When true, checks for unresolved references when opening an archive (Policy Archive + Environment Archive) and reports an error if any exist.

Configuration Studio

83

sleak

[com/vordel/client/rcp/common/util/RcpUtils.java; L67](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/RcpUtils.java#L67)

  

Enables SWT resource profiling; Launches the S-Leak application in tendem with Policy Studio

Internal; Policy Studio

84

showSoftwareUpdater

[com/vordel/client/rcp/common/util/RcpUtils.java; L263](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/RcpUtils.java#L263)

  

?

Policy Studio

85

xmleditorextras

[com/vordel/client/rcp/common/util/RcpUtils.java; L279](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-common/src/com/vordel/client/rcp/common/util/RcpUtils.java#L279)

  

Augments the application preferences with additional entries for XML editing; **XML** > **XML Catalog** and **XML** > **XML Files** > **Validation**

Policy Studio

86

VApiRuntime.addFullApiConfiguration

[com/vordel/apiportal/runtime/virtualized/VApiRuntime.java; L96](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/runtime/virtualized/VApiRuntime.java#L96)

false

When set to true, the entire Virtualized API model object is added to the message whiteboard for use in downstream policies.

By default, this object is not added to the whiteboard as it can contain sensitive data which may potentially be vulnerable to selector injections

API Manager; Runtime

87

ApiClientCacheTagsSize

[com/vordel/apiportal/api/portal/controller/ApiClientCache.java; L58](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiClientCache.java#L58)

2048

Specifies the maximum size (in bytes) for application and organization tags that are maintained by the API Manager cache.

API Manager; Cache

88

ApiClientCache.DiagEntry.MaxErrorLen

[com/vordel/apiportal/catalog/APIManagerConfigEventHandler.java; L111](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/catalog/APIManagerConfigEventHandler.java#L111)

500

?

API Manager

89

APIManagerConfigEventHandler.DefaultLogSize

[com/vordel/apiportal/catalog/APIManagerEventNotifier.java; L138](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/catalog/APIManagerEventNotifier.java#L138)

500

?

API Manager

90

PollerConfigEventHandler.DefaultLogSize

[com/vordel/apiportal/catalog/APIManagerEventNotifier.java; L296](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/apiportal/catalog/APIManagerEventNotifier.java#L296)

1000

?

API Manager

91

ConfigEventHandler.DefaultLogSize

[com/vordel/api/management/ConfigEventHandler.java; L12](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/api/management/ConfigEventHandler.java#L12)

200

  

  

92

auditLogRotateMaxFiles

[com/vordel/audit/Constants.java; L12](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/audit/Constants.java#L12)

50

Specifies the maximum number of audit log files to store on disk before the API Gateway starts to remove the oldest files

API Gateway; Logging; Audit

93

jmsConnStartupTimeoutMillis

[com/vordel/dwe/jms/JMSService.java; L83](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSService.java#L83)

10 \* 60 \* 1000

If JMS is configured not to reconnect on error, this property specifies the maximum amount of time to wait (in milliseconds) whilst attempting to establish the initial JMS connection. Establishing the connection is done using an exponential backoff algorithm starting at 50ms up to the maximum specified by this property.

API Gateway; JMS

94

jmsReConnectMaxInterval

[com/vordel/dwe/jms/JMSService.java; L456](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSService.java#L456)

60 \* 1000

If JMS is configured to reconnect on error, this property specifies the maximum amount of time to wait (in milliseconds) whilst attempting to reestablish the JMS connection. Reestablishing the connection is done using an exponential backoff algorithm starting at 100ms up to the maximum specified by this property

API Gateway; JMS

95

passportConnPrefillMaxRetryIntervalMillis

[com/vordel/circuit/passport/PassPortClientCache.java; L184](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/circuit/passport/PassPortClientCache.java#L184)

3 \* 60 \* 1000

Specifies the maximum amount of time to wait (in milliseconds) whilst attempting to establish an initial Axway Passport connection. Establishing the connection is done using an exponential backoff algorithm starting at 500ms up to the maximum specified by this property.

API Gateway; Axway Passport

96

PolicyStudio.MaxRecentProjectsCount

[com/vordel/client/gui/policystudio/project/RecentProjectHandler.java; L76](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/rcplatform/plugin-policystudio/src/com/vordel/client/gui/policystudio/project/RecentProjectHandler.java#L76)

10

Specifies the maximum number of recent projects to list on the 'Recent Projects' tab in PolicyStudio

Policy Studio

97

auditLogRotateFileSize

[com/vordel/audit/Constants.java; L11](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/audit/Constants.java#L11)

5L \* 1024L \* 1024L

Specifies the maximum size, in bytes, of the audit log file, before it is closed and rolled over to a new file.

Logging; Audit

98

jmsMsgRcvExceptionTimeoutMillis

[com/vordel/dwe/jms/JMSConsumer.java; L404](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSConsumer.java#L404)

10 \* 1000

Specifies the amount of time to wait (in milliseconds) prior to attempting to consume JMS messages from the configured queue, after a connection-related exception has occurred.

API Gateway; JMS

99

jmsMsgRcvInitialExceptionTimeoutMillis

[com/vordel/dwe/jms/JMSConsumer.java; L405](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSConsumer.java#L405)

10

Specifies the amount of time to wait (in milliseconds) prior to attempting to consume JMS messages from the configured queue after a non-connection-related exception has occurred. The initial value is calculated as follows:

**_`n * 2 * errorCount`_**

where n is the value of this property.

API Gateway; JMS

100

jmsMsgRcvMaxExceptionTimeoutMillis

[com/vordel/dwe/jms/JMSConsumer.java; L406](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3/src/java/src/com/vordel/dwe/jms/JMSConsumer.java#L406)

10 \* 1000

Specifies the maximum amount of time to wait (in milliseconds) prior to attempting to consume JMS messages from the configured queue after a non-connection-related exception has occurred.

API Gateway; JMS

### 7.5.3.SP1

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=65525&readmeId=33421&pdtId=394)

New Java System Properties

1

Git tag

[v\_7\_5\_3\_SP1](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP1/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP1

List of commits

git log --pretty=oneline v\_7\_5\_3...v\_7\_5\_3\_SP1

List of changes

git diff v\_7\_5\_3 v\_7\_5\_3\_SP1 > 7.5.3.SP1.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP1 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

vapiPollerEventRepeatPeriodMs

[com/vordel/apiportal/catalog/APIManagerEventNotifier.java; L139](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP1/src/java/src/com/vordel/apiportal/catalog/APIManagerEventNotifier.java#L139)

0

?

Not sure I fully understand what this is used for. See  [![](https://jira.axway.com/secure/viewavatar?size=xsmall&avatarId=13303&avatarType=issuetype)RDAPI-8389 ](https://jira.axway.com/browse/RDAPI-8389?src=confmacro) - API Manager cannot work with LOCAL\_QUORUM in multi-DC Closed  . Details are not clear.

API Manager

### 7.5.3.SP2

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=66303&readmeId=33898&pdtId=394)

New Java System Properties

2

Git tag

[v\_7\_5\_3\_SP2](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP2/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP2

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP1...v\_7\_5\_3\_SP2

List of changes

git diff v\_7\_5\_3\_SP1 v\_7\_5\_3\_SP2 > 7.5.3.SP2.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP2 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

SKIP\_KPS\_SECONDARY\_KEY\_CHECK\_FOR\_CREATE

[com/vordel/kps/storeImpl/AbstractTransaction.java; L342](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP2/src/java/src/com/vordel/kps/storeImpl/AbstractTransaction.java#L342)

false

Specifies whether or nor bad data can be imported via KPS. This involves the skipping of secondary key checks to allow data to be imported.

**Note**: This option must only be enabled with guidance from Axway support

API Gateway; API Manager; KPS

2

com.vordel.apimanager.uri.path.trailingSlash.preserve

[com/vordel/coreapireg/runtime/URIs.java; L61](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP2/src/java/src/com/vordel/coreapireg/runtime/URIs.java#L61)

  

Specifies whether or not the trailing slash in last component of the path of the URI must be preserved.

API Manager; Runtime

### 7.5.3.SP3

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=66867&readmeId=34191&pdtId=394)

New Java System Properties

2

Git tag

[v\_7\_5\_3\_SP3](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP3/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP3

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP2...v\_7\_5\_3\_SP3

List of changes

git diff v\_7\_5\_3\_SP2 v\_7\_5\_3\_SP2 > 7.5.3.SP3.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP3 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

wsdlImport.suppressSchemaValidationErrors

[com/vordel/xsd/LoadSchemaFromResolver.java; L42](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP3/src/java/src/com/vordel/xsd/LoadSchemaFromResolver.java#L42)

[com/vordel/client/swt/wsdl/RetrieveAndValidateWSDLPage.java; L34](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP3/rcplatform/plugin-filter-base/src/com/vordel/client/swt/wsdl/RetrieveAndValidateWSDLPage.java#L34)

false

Specifies whether or not schema validation errors, due to strict validation being performed, should be suppressed during the import/virtualization of semantically invalid WSDLs. If set to true, validation exceptions are logged, but not thrown.

API Gateway; API Manager; WSDL

2

ConnectToUrlFilter.removePreviousConnections

[com/vordel/circuit/net/ConnectionProcessor.java; L105](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP3/src/java/src/com/vordel/circuit/net/ConnectionProcessor.java#L105)

false

Specifies whether or not previous outbound HTTP connections (made using the ConnectToURL filter) are released.

By default the API Gateway releases all connections when policy execution has completed. This means that if a ConnectToURL filter is invoked numerous times, the number of connections in the CLOSE\_WAIT state can end up being very high. Configuring this property circumvents this issue.

API Gateway; Filter Runtime; HTTP; ConnectToURL

### 7.5.3.SP4

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=67207&readmeId=34311&pdtId=394)

New Java System Properties

0

Git tag

[v\_7\_5\_3\_SP4](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP4/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP4

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP3...v\_7\_5\_3\_SP4

List of changes

git diff v\_7\_5\_3\_SP3 v\_7\_5\_3\_SP4 > 7.5.3.SP4.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

No new Axway-defined Java System Properties were introduced in the 7.5.3.SP4 release

### 7.5.3.SP5

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=68215&readmeId=34904&pdtId=394)

New Java System Properties

1

Git tag

[v\_7\_5\_3\_SP5](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP5/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP5

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP4...v\_7\_5\_3\_SP5

List of changes

git diff v\_7\_5\_3\_SP4 v\_7\_5\_3\_SP5 > 7.5.3.SP5.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP5 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

fastCoverage

[com/vordel/client/circuit/model/Circuit.java; L43](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP5/rcplatform/plugin-filter-base/src/com/vordel/client/circuit/model/Circuit.java#L43)

false

Specifies whether or not optimized filter coverage is performed.

Loading a complex policy that contains a large number of filters (including a high number of filters with success/failure paths) can take a long time. Setting this property to **true** can speed up the policy loading, but at the cost of a potentially inaccurate list of filter attributes being displayed.

Policy Studio;

### 7.5.3.SP6

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=68637&readmeId=35129&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+6+Readme)

New Java System Properties

0

Git tag

[v\_7\_5\_3\_SP6](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP6/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP6

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP5...v\_7\_5\_3\_SP6

List of changes

git diff v\_7\_5\_3\_SP5 v\_7\_5\_3\_SP6 > 7.5.3.SP6.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

No new Axway-defined Java System Properties were introduced in the 7.5.3.SP6 release

### 7.5.3.SP7

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=69650&readmeId=35691&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+7+Readme)

New Java System Properties

8

Git tag

[v\_7\_5\_3\_SP7](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP7/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP7

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP6...v\_7\_5\_3\_SP7

List of changes

git diff v\_7\_5\_3\_SP6 v\_7\_5\_3\_SP7 > 7.5.3.SP7.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP7 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.fault.resetHeaders.http429

[com/vordel/apiportal/runtime/Broker.java; L403](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/apiportal/runtime/Broker.java#L403)

false

Specifies whether or not response headers are reset/cleared when a HTTP 429 (Too Many Requests; i.e. when a quota has been exceeded) error occurs.

API Manager; HTTP

2

com.axway.apimanager.fault.removeContentBody

[com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java; L20](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java#L20)

false

Specifies whether or not to remove the reflected request body in an API Manager-generated 400, 404, 405 or 429 HTTP response.

By default API Manager reflects the request body in generated responses for 400, 404, 405, and 429 HTTP errors that are not handled by Global Fault handlers. Setting this property to **true** circumvents this issue.

API Manager; HTTP; Fault Handlers

3

distributed.ehcache.cache.reload.pause.secs

[com/vordel/circuit/cache/CacheContainer.java; L128](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cache/CacheContainer.java#L128)

5

Specifies the duration, in seconds, to pause before recreating caches.

Configuration deploy can break distributed ehcache operations. Introducing a delay between re-creating the ehcache manager and the caches themselves resolves the issue.

API Gateway; EHCache

4

OCSP\_RSP\_VALID\_UNTIL\_EXPIRATION

[com/vordel/circuit/cert/ocsp/ResponseVerifier.java; L53](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cert/ocsp/ResponseVerifier.java#L53)

true

If set to true, specifies that the OCSP response is valid until the **`nextUpdate`** field in the response.

**Note**: This setting has been incorporated into the OCSP Client filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OCSP; Filter Runtime

5

OCSP\_RSP\_VALID\_UNTIL

[com/vordel/circuit/cert/ocsp/ResponseVerifier.java; L54](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cert/ocsp/ResponseVerifier.java#L54)

0

If **`OCSP_RSP_VALID_UNTIL_EXPIRATION`** property is set to false, this property specifies the expiry time for OCSP response.

**Note**: This setting has been incorporated into the OCSP Client filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OCSP; Filter Runtime

6

OCSP\_RSP\_VALID\_UNTIL\_UNITS

[com/vordel/circuit/cert/ocsp/ResponseVerifier.java; L55](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cert/ocsp/ResponseVerifier.java#L55)

days

If **`OCSP_RSP_VALID_UNTIL_EXPIRATION`** property is set to false, this property specifies the the units relating to  **`OCSP_RSP_VALID_UNTIL`** ; Possible values: 'days', 'hours', 'minutes', 'seconds'.

**Note**: This setting has been incorporated into the OCSP Client filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OCSP; Filter Runtime

7

OCSP\_RSP\_VALID\_FOR

[com/vordel/circuit/cert/ocsp/ResponseVerifier.java; L56](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cert/ocsp/ResponseVerifier.java#L56)

6

When an OCSP response with no validation date is encountered, this property specifies how long the OCSP response is valid for.

**Note**: This setting has been incorporated into the OCSP Client filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OCSP; Filter Runtime

8

OCSP\_RSP\_VALID\_FOR\_UNITS

[com/vordel/circuit/cert/ocsp/ResponseVerifier.java; L57](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP7/src/java/src/com/vordel/circuit/cert/ocsp/ResponseVerifier.java#L57)

hours

When an OCSP response with no validation date is encountered, this property specifies the units relating to  **OCSP\_RSP\_VALID\_FOR**; Possible values: 'days', 'hours', 'minutes', 'seconds'.

**Note**: This setting has been incorporated into the OCSP Client filter in the latest version of API Gateway, so this property no longer has any effect.

API Gateway; OCSP; Filter Runtime

### 7.5.3.SP8

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=70731&readmeId=36410&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+8+Readme)

New Java System Properties

4

Git tag

[v\_7\_5\_3\_SP8](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP8/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP8

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP7...v\_7\_5\_3\_SP8

List of changes

git diff v\_7\_5\_3\_SP7 v\_7\_5\_3\_SP8 > 7.5.3.SP8.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP8 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

pgpFailDecryptNoSignature

[com/vordel/circuit/pgp/DecryptProcessor.java; L172](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/circuit/pgp/DecryptProcessor.java#L172)

false

Specifies whether or not to raise a PGPException if `**${content.body}**` contains an unsigned OnePass signature.

API Gateway; PGP; Filter Runtime

2

**com.vordel.apimanager.uri.path.trailingSlash.preserve**

[com/vordel/circuit/rbac/policy/OperationAtom.java; L201](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/circuit/rbac/policy/OperationAtom.java#L201)

[com/vordel/coreapireg/runtime/PathResolverResult.java; L66](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/coreapireg/runtime/PathResolverResult.java#L66)

false

Specifies whether or not the trailing slash in last component of the path of the URI must be preserved.

**Existing property introduced in 7.5.3.SP2; code refactored**

API Manager; Runtime

3

com.axway.apimanager.fault.legacy

[com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java#L26)

[com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java; L31](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/coreapireg/runtime/broker/ApiFaultHandler.java#L31)

[com/vordel/coreapireg/runtime/broker/ApiShuntContext.java; L39](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/coreapireg/runtime/broker/ApiShuntContext.java#L39)

false

Specifies whether or not to invoke the API Gateway's generic fault handler when one of the following errors occurs during the processing of an API request:

Error

Description

###### _ApiShunt.invokableMethodFailure_

###### _API Operation Failure.  _Content-type mapping failure__

###### _ApiShunt.invokableMethodCircuitFailure_

###### _Circuit Error_

###### _ApiShunt.allAuthNDevicesFailed_

###### _Inbound authentication has failed_

###### _ApiShunt.noAuthNDevicesConfigured_

###### _No inbound authentication has been configured for the API_

###### _ApiShunt.unexpectedAPI_

###### _The configured resourcePath does not match the resource path presented in the request_

###### _ApiShunt.noMethodMatched_

###### _The inbound request doesn't match any configured virtualised API method. In this instance a match is found at the API level_

###### _ApiShunt.originNotAllowed_

###### _Javascript Origin specified for the Application credential does not match the supplied Origin_

###### _ApiShunt.applicationNotAuthorized_

###### _The client Application does not have access to the API being invoked. Inbound security is set to something **other than** PassThrough. Application credentials are sent with the request_

###### _ApiShunt.backendError_

###### _The backend service returned an error (non-2XX) response_

When set to true only API Manager's internal fault handler is invoked, otherwise both the API Gateway's generic fault handler and the API Manager internal fault handler get invoked (in that order).

**Note**: API Manager's internal fault handler is not to be confused with the fault handlers that are configurable via the API Manager UI.

API Manager; Runtime; Fault Handlers

4

com.axway.websocket.policy.onclose

[com/vordel/dwe/http/WebsocketPlugin.java; L78](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP8/src/java/src/com/vordel/dwe/http/WebsocketPlugin.java#L78)

  

Specifies the ShorthandKey to a policy that will get invoked when the Websocket connection has closed.

**Note**: This setting has been incorporated into the Websocket resolver in the latest version of API Gateway, so this property no longer has any effect

API Gateway; Websockets

### 7.5.3.SP9

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=71603&readmeId=37068&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+9+Readme)

New Java System Properties

3

Git tag

[v\_7\_5\_3\_SP9](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP9/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP9

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP8...v\_7\_5\_3\_SP9

List of changes

git diff v\_7\_5\_3\_SP8 v\_7\_5\_3\_SP9 > 7.5.3.SP9.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP9 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.apimanager.application.oauth.restrictScopes

[com/vordel/apiportal/api/portal/controller/ApplicationController.java; L1097](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP9/src/java/src/com/vordel/apiportal/api/portal/controller/ApplicationController.java#L1097)

  

When querying for application scopes, specifies whether or not to restrict scopes to those explicitly defined for the application (in API Manager, see _Enable application scopes_), as opposed to including all scopes defined for all APIs that the Application has been granted access to.

**Note**: This property has been removed from the product. An equivalent setting ( _Apply application scope restrictions_) has been added to API Manager Settings

API Manager; OAuth; Scopes

2

com.axway.oauth.scopes.check.all.legacy

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L58](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP9/src/java/src/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L58)

false

Specifies whether or not a request is rejected if additional scopes, not configured for OAuth inbound security, are present in the message.

When set to true, additional scopes are permitted. 

API Gateway; API Manager; OAuth

3

com.axway.apimanager.csrf

[com/vordel/common/apiserver/controller/AbstractStoreAccess.java; L262](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP9/src/java/src/com/vordel/common/apiserver/controller/AbstractStoreAccess.java#L262)

true

Specifies whether or not CSRF checks are performed in API Manager. This property allows CSRF to be switched off. If set to false, CSRF is disabled.

API Manager; CSRF

### 7.5.3.SP10

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=72293&readmeId=37499&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+10+Readme)

New Java System Properties

9

Git tag

[v\_7\_5\_3\_SP10](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP10/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP10

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP9...v\_7\_5\_3\_SP10

List of changes

git diff v\_7\_5\_3\_SP9 v\_7\_5\_3\_SP10 > 7.5.3.SP10.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP10 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.response.redirect.location.relative

[com/vordel/apiportal/api/PortalServletContainer.java; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/api/PortalServletContainer.java#L40)

true

Specifies whether or not the  `**Location**` header, as part of a 3030 response, contains a relative URI. If absolute URIs are required this property can be set to **true**.

API Manager; HTTP; Redirects

2

com.axway.apimanager.proxy.defaultPath

[com/vordel/apiportal/api/portal/controller/ApiProxyController.java; L216](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/api/portal/controller/ApiProxyController.java#L216)

/api

In API Manager, this property specifies the default resource path for a Frontend (virtualized) API.

API Manager; Virtualization

3

com.axway.apimanager.eventtimestamp.ignorettl

[com/vordel/apiportal/api/portal/controller/EventTimestampController.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/api/portal/controller/EventTimestampController.java#L21)

false

Specifies whether or not to ignore TTL (time-to-live) for timestamped events (API Manager's PortalTimestamp table). If set to false, events are purged solely by API Manager, rather than Cassandra using TTL to expire data.

In a multi-node setup, setting this property can circumvent synchronisation issues between Cassandra nodes, where TTL can prematurely expire data.

API Manager; Cassandra; KPS

4

com.axway.apimanager.api.data.cache

[com/vordel/apiportal/common/APIPortalConstants.java; L227](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/common/APIPortalConstants.java#L227)

false

Specifies whether or not to enable caching to improve general system performance and speed .

External clients, API keys, and OAuth credentials caches are optimized so that updates to the cache no longer block API Manager runtime traffic, resulting in performance improvements for corresponding API Manager APIs. As a result of the non-blocking cache updates, API Manager memory consumption will increase, particularly in systems with large numbers of external clients, API keys or OAuth credentials.

API Manager; Cache

5

com.axway.apimanager.apiImport.secure.serverCertificateVerification

[com/vordel/apiportal/common/ApiManagerHostnameVerifier.java; L18](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/common/ApiManagerHostnameVerifier.java#L18)

false

Specifies whether or not hostname verification is performed during the import of an API from an SSL-protected endpoint.

This setting was introduced to allow the import of APIs from an endpoint protected by a self-signed certificate.

**Note**: In the latest version of API Manager this setting has been incorporated into the API Manager UI, where the default is set to **true**.

API Manager; Import

6

com.axway.apimanager.apiImport.secure.strictCertificateChecking

[com/vordel/apiportal/common/ApiManagerTrustManager.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/apiportal/common/ApiManagerTrustManager.java#L26)

false

Specifies whether or not certificate validation is performed during the import of an API from an SSL-protected endpoint.

This setting was introduced to allow the import of APIs from an endpoint protected by a self-signed certificate.

**Note**: In the latest version of API Manager this setting has been incorporated into the API Manager UI, where the default is set to **true**.

API Manager; Import

7

com.axway.soap.faultnamespace

[com/vordel/circuit/soap/SOAPFaultDocumentCreator.java; L47](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/circuit/soap/SOAPFaultDocumentCreator.java#L47)

http://www.vordel.com/soapfaults

Specifies the namespace to be used when generating a SOAP Fault response.

By default the  fault namespace indicates that the SOAP fault originated from an Axway API Gateway. This property can be employed to circumvent any potential security risk.

API Gateway; WSDL; SOAP; Runtime

8

com.axway.apimanager.fault.global

[com/vordel/coreapireg/runtime/broker/ApiShuntContext.java; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/coreapireg/runtime/broker/ApiShuntContext.java#L40)

false

Specifies whether or not to fallback to the API Gateway's global fault handler when an error occurs during API request processing in API Manager

**Note**: This property has since been removed from the product and so will have no effect on API Manager runtime.

API Manager; Runtime; Fault Handlers

9

com.coreapireg.apimethod.contenttype.legacy

[com/vordel/coreapireg/runtime/broker/InvokableMethod.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP10/src/java/src/com/vordel/coreapireg/runtime/broker/InvokableMethod.java#L49)

false

Specifies whether or not to perform Content-Type validation on incoming API Manager requests.

API Manager generates a `No Match For Request` error when `Content-Type` is not equal to the API method MIME type. Setting this property to **true** disables this check.

API Manager; Runtime

### 7.5.3.SP11

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=73425&readmeId=38063&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+11+Readme)

New Java System Properties

2

Git tag

[v\_7\_5\_3\_SP11](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP11/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP11

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP10...v\_7\_5\_3\_SP11

List of changes

git diff v\_7\_5\_3\_SP10 v\_7\_5\_3\_SP11 > 7.5.3.SP11.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP11 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apigw.anm.http.proxyHost

[com/vordel/api/nm/ProxyPropertyConstants.java; L4](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP11/src/java/src/com/vordel/api/nm/ProxyPropertyConstants.java#L4)

  

Specifies the hostname of the proxy server to use when making a connection to the Node Manager.

API Gateway; Node Manager

2

com.axway.apigw.anm.http.proxyPort

[com/vordel/api/nm/ProxyPropertyConstants.java; L5](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP11/src/java/src/com/vordel/api/nm/ProxyPropertyConstants.java#L5)

  

Specifies the port of the proxy server to use when making a connection to the Node Manager.

API Gateway; Node Manager

### 7.5.3.SP12

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=74514&readmeId=38555&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+12+Readme)

New Java System Properties

6

Git tag

[v\_7\_5\_3\_SP12](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP12/)

Git checkout command

git checkout tags/v\_7\_5\_3\_SP12

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP11...v\_7\_5\_3\_SP12

List of changes

git diff v\_7\_5\_3\_SP11 v\_7\_5\_3\_SP12 > 7.5.3.SP12.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP12 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.queue.ttl

[com/vordel/apiportal/actionqueue/QueueProcessor.java; L65](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/apiportal/actionqueue/QueueProcessor.java#L65)

604800

Specifies the time to live for records pertaining to users, organizations or applications requiring verification or approval.

By setting this TTL property, Cassandra will automatically remove invalid or expired verification/approval requests from the database. This may be set in order to implement certain GDPR requirements. The default value is 1 week.

API Manager; KPS; Cassandra

2

com.axway.apimanager.apiclient.cache.response.legacy

[com/vordel/apiportal/api/portal/controller/ApiClientCache.java; L1062](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/apiportal/api/portal/controller/ApiClientCache.java#L1062)

false

Specifies whether or not to throw a HTTP 401 or 503 exception when the API Client cache is not available.

If set to true, the legacy behaviour is performed, and a 401 error is returned to the client when requesting items from the cache, otherwise a 503 error is returned.

401 errors may occur during cache initialisation, particularly if there is a large number of items being read from Cassandra on startup.

API Manager; Cache

3

**com.axway.apimanager.api.data.cache**

[com/vordel/apiportal/common/APIPortalConstants.java; L104](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/apiportal/common/APIPortalConstants.java#L104)

false

See 7.5.3.SP10 for more details

**Existing property introduced in 7.5.3.SP10; code refactored**

API Manager; Cache

4

com.vordel.apimanager.swagger.method.singleslash.ignore

[com/vordel/apiportal/runtime/virtualized/VApiRuntime.java; L70](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/apiportal/runtime/virtualized/VApiRuntime.java#L70)

false

Specifies whether or not to remove the '/' from the API method path so that it is not sent to the backend service.

**Note**: The removal will only occur when the method path is set to "/". This will happen for both WSDL and REST services.

API Manager; Swagger; Virtualization

5

com.axway.oauth.scopes.openid.allow

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L130](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L130)

true

Specifies whether or not the 'openid' scope is permitted when generating an access token.

By default "openid" is always accepted as a valid scope in all OAuth configurations.

API Gateway; API Manager; OAuth; OpenID Connect

6

com.axway.apigw.smime.sign.md

[com/vordel/circuit/smime/SignProcessor.java; L41](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_SP12/src/java/src/com/vordel/circuit/smime/SignProcessor.java#L41)

sha256

Specifies the digest algorithm to be used by the SMIME Sign filter.

The default SMIME digest algorithm can also be changed via `${com.axway.apigw.smime.sign.md}` message attribute. The policy message attribute supersedes the Java system property.

The following digest algorithms are supported:

*   sha1
*   sha224
*   sha256
*   sha384
*   sha512

The corresponding SMIME Content-Type header ' **micalg**' attribute is set accordingly.

API Gateway; SMIME; Filter Runtime

### 7.5.3.SP13

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?9hokpcui50l6fas9ecedrsvrd7&fileId=76137&readmeId=39449&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.5.3+SP+13+Readme)

New Java System Properties

2

Git tag

[v\_7\_5\_3\_SP13](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_5_3_SP13/)

Git checkout command

git checkout 70f846f394

List of commits

git log --pretty=oneline v\_7\_5\_3\_SP12...70f846f394

In the absence of a tag for SP13, a commit hash must be used

List of changes

git diff v\_7\_5\_3\_SP12 70f846f394 > 7.5.3.SP13.diff

In the absence of a tag for SP13, a commit hash must be used

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.5.3.SP13 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.coreapi.method.servicecontext.clientattr

[com/vordel/coreapireg/runtime/CoreApiBroker.java; L73](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_branch/src/java/src/com/vordel/coreapireg/runtime/CoreApiBroker.java#L73)

false

Controls the client attribute name to be written to the service context of the metrics/RTM event log.

If set to **true**, the value of **`${authentication.application.id}`**  will be written to the service context, otherwise the value of  **`${authentication.subject.id}`**  will be written.

**Note**: This property comes into effect for API Manager OAuth traffic.

API Manager; Runtime; Metrics; OAuth

2

sftp.close.timeout

[com/vordel/fileservice/sftp/SSHServer.java; L231](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_5_3_branch/src/java/src/com/vordel/fileservice/sftp/SSHServer.java#L231)

5

Specifies the amount of time, in seconds, to wait while gracefully closing an SFTP connection.

API Gateway; SFTP

### Post 7.5.3.SP13 Patches

A number of patches have been released on the v\_7\_5\_3\_branch code after the release of SP13.

#### Synopsis

  

New Java System Properties

N/A

Git checkout command

git checkout v\_7\_5\_3\_branch

List of commits

git log --pretty=oneline 70f846f394...HEAD

List of changes

git diff 70f846f394 HEAD > 7.5.3.SP13.Patches.diff

The Git commands above should be executed on v\_7\_5\_3\_branch

#### Detail

No new Axway-defined Java System Properties were introduced after the 7.5.3.SP13 release

### 7.6.2

#### Synopsis

  

New Java System Properties

19

Git tag

[v\_7\_6\_2](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2/)

Git checkout command

git checkout tags/v\_7\_6\_2

List of tags

git log --simplify-by-decoration --decorate --pretty=oneline "v\_7\_6\_2\_branch" | fgrep 'tag: '

List of commits

git log --pretty=oneline v\_7\_5\_3-8...v\_7\_6\_2-5

List of changes

git diff --no-renames v\_7\_5\_3-8 v\_7\_6\_2-5 > 7.6.2.diff

_\--no-renames option to get around the **renamelimit** warning_

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2 release, over and above those already defined for the [7.5.3 release](https://techweb.axway.com/confluence/display/RDAPI/JVM+System+Properties+-+WIP#JVMSystemProperties-WIP-Detail).

  

  

System Property

Occurrences

Default Value

Description

Context

1

xmlSaxParserMaxPoolSize

[com/vordel/common/xml/XmlSaxParserCache.java; L50](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/gwplatform/apigw-common/src/main/java/com/vordel/common/xml/XmlSaxParserCache.java#L50)

0

Specifies whether or not to create thread-local (i.e. 1 instance per thread) or pooled cache of SAX parsers. If the value of this property is greater than 0, a pooled cache is assumed.

The following filters directly employ this property, when set: **WS-Security Policy Layout**, **XML Complexity**

API Gateway; XML; Filter Runtime

2

schemaCacheSize

[com/vordel/circuit/schema/SchemaValidationProcessor.java; L160](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/circuit/schema/SchemaValidationProcessor.java#L160)

10

Specifies the size of the cache used by the Schema Validation filter.

Schemas are stored in the cache as an optimization, so as not to generate them each time the filter is invoked (which has a large overhead). Least recently used items are evicted from the cache when the cache reaches capacity.

API Gateway; XML; Schema Validation; Filter Runtime

3

lineFeed

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L35](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L35)

  

Specifies the the character to use as the line feed.

Under the hood, this actually uses the  **`line.separator`** System Property.

API Gateway; Logging; Transaction Event

4

groupId

[install/system/conf/log4j2.xml; L4](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L4)

[install/system/conf/log4j2.xml; L5](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L5)

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

[com/vordel/opentraffic/OpenTrafficLogger.java; L73](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L73)

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L22](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L22)

  

Specifies the identifier of the topology group, which is used to generate the name of the log file and also appears in the log header.

API Gateway; Logging; Transaction Event; Open Traffic

5

serviceId

[com/vordel/opentraffic/OpenTrafficLogger.java; L74](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L74)

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L23)

[install/system/conf/log4j2.xml; L4](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L4)

[install/system/conf/log4j2.xml; L5](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L5)

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

  

Specifies the identifier of the topology service, which is used to generate the name of the log file.

API Gateway; Logging; Transaction Event; Open Traffic

6

hostname

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L21)

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

  

Specifies the hostname on which the API Gateway process is running. Written to the log header

API Gateway; Logging; Transaction Event

7

domainId

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L20](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L20)

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

  

Specifies the domain identifier in the topology. Written to the log header.

API Gateway; Logging; Transaction Event

8

version

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L24](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L24)

  

Specifies the version of the API Gateway. Written to the log header.

API Gateway; Logging; Transaction Event

9

event.log.group.name

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

  

Specifies the name of group to which the API Gateway belongs. Written to the log header.

API Gateway; Logging; Transaction Event

10

event.log.service.name

[install/system/conf/log4j2.xml; L23](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L23)

  

Specifies the name of the API Gateway service. Written to the log header.

API Gateway; Logging; Transaction Event

11

APIGatewayTxnEventLog.dir

[com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java; L19](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/metrics/eventlog/logger/Log4j2EventLogger.java#L19)

[install/system/conf/log4j2.xml; L18](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L18)

[install/system/conf/log4j2.xml; L19](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L19)

[install/system/conf/log4j2.xml; L30](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L30)

  

Specifies the directory in which the transaction event log files will reside.

API Gateway; Logging; Transaction Event

12

APIGatewayOpenTrafficEventLog.dir

[com/vordel/opentraffic/OpenTrafficLogger.java; L75](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L75)

[install/system/conf/log4j2.xml; L39](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L39)

[install/system/conf/log4j2.xml; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L40)

[install/system/conf/log4j2.xml; L51](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L51)

  

Specifies the directory in which the open transaction event log files will reside.

API Gateway; Logging; Open Traffic

13

APIGatewayOpenTrafficEventLog.maxFileSize

[com/vordel/opentraffic/OpenTrafficLogger.java; L91](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L91)

[install/system/conf/log4j2.xml; L48](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L48)

  

Specifies the maximum size of an open traffic log file.

API Gateway; Logging; Open Traffic

14

APIGatewayOpenTrafficEventLog.maxDiskSize

[install/system/conf/log4j2.xml; L53](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/install/system/conf/log4j2.xml#L53)

[com/vordel/opentraffic/OpenTrafficLogger.java; L80](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L80)

[com/vordel/opentraffic/OpenTrafficLogger.java; L87](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2-5/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L87)

  

Specifies the maximum amount of space open traffic log files can occupy on disk

API Gateway; Logging; Open Traffic

15

wsdlImport.suppressSchemaValidationErrors

[com/vordel/client/swt/wsdl/RetrieveAndValidateWSDLPage.java; L34](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/rcplatform/plugin-filter-base/src/com/vordel/client/swt/wsdl/RetrieveAndValidateWSDLPage.java#L34)

[com/vordel/xsd/LoadSchemaFromResolver.java; L42](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/src/java/src/com/vordel/xsd/LoadSchemaFromResolver.java#L42)

false

This property was introduced as part of a fixport. See 7.5.3.SP3 for more details

API Gateway; API Manager; WSDL

16

vapiPollerEventRepeatPeriodMs

[com/vordel/apiportal/catalog/APIManagerEventPoller.java; L24](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/src/java/src/com/vordel/apiportal/catalog/APIManagerEventPoller.java#L24)

0

This property was introduced as part of a fixport. See 7.5.3.SP3 for more details

API Manager

17

entityStore.typeFactory

[com/vordel/es/EntityStoreFactory.java; L87](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/gwplatform/es/es-core/src/main/java/com/vordel/es/EntityStoreFactory.java#L87)

com.vordel.es.EntityTypeFactoryImpl

Specifies a factory for creating Entity Stores.

Internal; API Gateway; Entity Store

18

distributed.ehcache.cache.reload.pause.secs

[com/vordel/circuit/cache/CacheContainer.java; 128](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/src/java/src/com/vordel/circuit/cache/CacheContainer.java#L128)

5

This property was introduced as part of a fixport. See 7.5.3.SP7 for more details

API Gateway; EHCache

19

SKIP\_KPS\_SECONDARY\_KEY\_CHECK\_FOR\_CREATE

[com/vordel/kps/storeImpl/AbstractTransaction.java; L346](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2/gwplatform/kps-core/src/main/java/com/vordel/kps/storeImpl/AbstractTransaction.java#L346)

false

This property was introduced as part of a fixport. See 7.5.3.SP2 for more details

API Gateway; API Manager; KPS

### 7.6.2.SP1

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?rkatq7ajahfuelfk3ubk1r58j3&fileId=71453&readmeId=36940&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.6.2+SP+1+Readme)

New Java System Properties

4

Git tag

[v\_7\_6\_2\_SP1](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2_SP1/)

Git checkout command

git checkout tags/ v\_7\_6\_2\_SP1

List of commits

git log --pretty=oneline v\_7\_6\_2-5...v\_7\_6\_2\_SP1

List of changes

git diff v\_7\_6\_2-5 v\_7\_6\_2\_SP1 > 7.6.2.SP1.diff

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2.SP1 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

OpenTrafficLog.configurationFile

[com/vordel/opentraffic/OpenTrafficLogger.java; L80](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L80)

[com/vordel/opentraffic/OpenTrafficLogger.java; L81](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/opentraffic/OpenTrafficLogger.java#L81)

${VDISTDIR}/system/conf/loggers/openTrafficLog.yaml

Specifies the location of the Open Traffic Log configuration file

API Gateway; Logging; Open Traffic

2

com.apimanager.application.oauth.restrictScopes

[com/vordel/apiportal/api/portal/controller/ApplicationController.java; L1085](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/apiportal/api/portal/controller/ApplicationController.java#L1085)

false

This property was introduced as part of a fixport. See 7.5.3.SP9 for more details

API Gateway; API Manager; OAuth; Scopes

3

pgpFailDecryptNoSignature

[com/vordel/circuit/pgp/DecryptProcessor.java; L172](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/circuit/pgp/DecryptProcessor.java#L172)

false

This property was introduced as part of a fixport. See 7.5.3.SP8 for more details

API Gateway; PGP; Filter Runtime

4

com.vordel.apimanager.uri.path.trailingSlash.preserve

[com/vordel/coreapireg/runtime/PathResolverResult.java; L70](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/coreapireg/runtime/PathResolverResult.java#L70)

[com/vordel/circuit/rbac/policy/OperationAtom.java; L203](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP1/src/java/src/com/vordel/circuit/rbac/policy/OperationAtom.java#L203)

false

This property was introduced as part of a fixport. See 7.5.3.SP2 for more details

API Manager; Runtime

### 7.6.2.SP2

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?rkatq7ajahfuelfk3ubk1r58j3&fileId=71828&readmeId=37215&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.6.2+SP+2+Readme)

New Java System Properties

3

Git tag

[v\_7\_6\_2\_SP2](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2_SP2/)

Git checkout command

git checkout tags/ v\_7\_6\_2\_SP2

List of commits

git log --pretty=oneline v\_7\_6\_2\_SP1...v\_7\_6\_2\_SP2

List of changes

git diff v\_7\_6\_2\_SP1 v\_7\_6\_2\_SP2 > 7.6.2.SP2.diff

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2.SP2 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.response.redirect.location.relative

[com/vordel/apiportal/api/PortalServletContainer.java; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP2/src/java/src/com/vordel/apiportal/api/PortalServletContainer.java#L40)

true

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; HTTP; Redirects

2

com.axway.oauth.scopes.check.all.legacy

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L58](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP2/src/java/src/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L58)

false

This property was introduced as part of a fixport. See 7.5.3.SP9 for more details

API Gateway; API Manager; OAuth

3

com.axway.soap.faultnamespace

[com/vordel/circuit/soap/SOAPFaultDocumentCreator.java; L47](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP2/src/java/src/com/vordel/circuit/soap/SOAPFaultDocumentCreator.java#L47)

http://www.vordel.com/soapfaults

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Gateway; WSDL; SOAP; Runtime

### 7.6.2.SP3

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?rkatq7ajahfuelfk3ubk1r58j3&fileId=72882&readmeId=37699&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.6.2+SP+3+Readme)

New Java System Properties

7

Git tag

[v\_7\_6\_2\_SP3](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2_SP3)

Git checkout command

git checkout tags/ v\_7\_6\_2\_SP3

List of commits

git log --pretty=oneline v\_7\_6\_2\_SP2...v\_7\_6\_2\_SP3

List of changes

git diff --no-renames v\_7\_6\_2\_SP2 v\_7\_6\_2\_SP3 > 7.6.2.SP3.diff

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2.SP3 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.apiImport.secure.strictCertificateChecking

[com/vordel/apiportal/common/ApiManagerTrustManager.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/apiportal/common/ApiManagerTrustManager.java#L26)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Import

2

com.axway.apimanager.apiImport.secure.serverCertificateVerification

[com/vordel/apiportal/common/ApiManagerHostnameVerifier.java; L18](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/apiportal/common/ApiManagerHostnameVerifier.java#L18)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Import

3

com.axway.apigw.anm.http.proxyHost

[com/vordel/api/nm/ProxyPropertyConstants.java; L4](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/api/nm/ProxyPropertyConstants.java#L4)

  

This property was introduced as part of a fixport. See 7.5.3.SP11 for more details

API Gateway; Node Manager

4

com.axway.apigw.anm.http.proxyPort

[com/vordel/api/nm/ProxyPropertyConstants.java; L5](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/api/nm/ProxyPropertyConstants.java#L5)

  

This property was introduced as part of a fixport. See 7.5.3.SP11 for more details

API Gateway; Node Manager

5

com.axway.apimanager.proxy.defaultPath

[com/vordel/apiportal/api/portal/controller/ApiProxyController.java; L225](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/apiportal/api/portal/controller/ApiProxyController.java#L225)

/api

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Virtualization

6

com.axway.apimanager.api.data.cache

[com/vordel/apiportal/common/APIPortalConstants.java; L228](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/apiportal/common/APIPortalConstants.java#L228)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Cache

7

com.coreapireg.apimethod.contenttype.legacy

[com/vordel/coreapireg/runtime/broker/InvokableMethod.java; L48](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP3/src/java/src/com/vordel/coreapireg/runtime/broker/InvokableMethod.java#L48)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Runtime

### 7.6.2.SP4

#### Synopsis

  

Release Notes

Available on [WebLiv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?rkatq7ajahfuelfk3ubk1r58j3&fileId=73574&readmeId=38165&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.6.2+SP+4+Readme)

New Java System Properties

2

Git tag

[v\_7\_6\_2\_SP4](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2_SP4/)

Git checkout command

git checkout tags/ v\_7\_6\_2\_SP4

List of commits

git log --pretty=oneline v\_7\_6\_2\_SP3...v\_7\_6\_2\_SP4

List of changes

git diff v\_7\_6\_2\_SP3 v\_7\_6\_2\_SP4 > 7.6.2.SP4.diff

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2.SP4 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.eventtimestamp.ignorettl

[com/vordel/apiportal/api/portal/controller/EventTimestampController.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP4/src/java/src/com/vordel/apiportal/api/portal/controller/EventTimestampController.java#L21)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Cassandra; KPS

2

com.axway.apimanager.csrf

[com/vordel/common/apiserver/filter/CsrfProtectionFilter.java; L154](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_SP4/src/java/src/com/vordel/common/apiserver/filter/CsrfProtectionFilter.java#L154)

true

This property was introduced as part of a fixport. See 7.5.3.SP9 for more details

API Manager; CSRF

### 7.6.2.SP5

#### Synopsis

  

Release Notes

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.6.2+SP+5+Readme+WIP)

New Java System Properties

7

Git tag

[v\_7\_6\_2\_SP5](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_6_2_branch/)

Git checkout command

git checkout tags/ v\_7\_6\_2\_SP5

List of commits

git log --pretty=oneline v\_7\_6\_2\_SP4...HEAD

List of changes

git diff v\_7\_6\_2\_SP4 HEAD > 7.6.2.SP5.diff

_The Git commands above should be executed on v\_7\_6\_2\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.6.2.SP5 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.api.model.disable.confidential.fields

[com/vordel/apiportal/api/portal/model/serialization/annotations/Confidential.java; L29](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/apiportal/api/portal/model/serialization/annotations/Confidential.java#L29)

true

Specifies whether or not fields containing sensitive information (e.g. passwords) should be serialized.

Setting this property to **false** will result in the following fields being present in a serialised export of a Frontend API.

*   authenticationProfile.parameters\['password'\]

API Manager; Swagger; Export

2

com.axway.json.parser.legacy

[com/vordel/mime/JSONBody.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/mime/JSONBody.java#L49)

[com/vordel/coreapireg/runtime/broker/parameters/JSONBodyValidator.java; L76](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/coreapireg/runtime/broker/parameters/JSONBodyValidator.java#L76)

false

Specifies whether or not the DeserializationFeature. FAIL\_ON\_TRAILING\_TOKENS feature is configured for parsing JSON.

This feature applies more rigour when parsing JSON payloads, thus preventing malformed JSON from being processed by the Gateway, and potentially sent on to backend services.

Setting this property to **true** will relax the validation.

API Gateway; API Manager; JSON

3

com.axway.apimanager.queue.ttl

[com/vordel/apiportal/actionqueue/QueueProcessor.java; L66](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/apiportal/actionqueue/QueueProcessor.java#L66)

604800

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Manager; KPS; Cassandra

4

com.axway.apimanager.apiclient.cache.response.legacy

[com/vordel/apiportal/api/portal/controller/ApiClientCache.java; L1101](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/apiportal/api/portal/controller/ApiClientCache.java#L1101)

false

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Manager; Cache

5

com.vordel.apimanager.swagger.method.singleslash.ignore

[com/vordel/apiportal/runtime/virtualized/VApiRuntime.java; L79](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/apiportal/runtime/virtualized/VApiRuntime.java#L79)

false

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Manager; Swagger; Virtualization

6

com.axway.oauth.scopes.openid.allow

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L130](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L130)

true

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Gateway; API Manager; OAuth; OpenID Connect

7

com.axway.apigw.smime.sign.md

[com/vordel/circuit/smime/SignProcessor.java; L41](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_6_2_branch/src/java/src/com/vordel/circuit/smime/SignProcessor.java#L41)

sha256

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Gateway; SMIME; Filter Runtime

### 7.7.0

#### Synopsis

  

New Java System Properties

8

Git tag

[v\_7\_7\_0](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_0/)

Git checkout command

git checkout tags/v\_7\_7\_0

List of tags

git log --simplify-by-decoration --decorate --pretty=oneline "v\_7\_7\_0\_branch" | fgrep 'tag: '

List of commits

git log --pretty=oneline v\_7\_6\_2...v\_7\_7\_0

List of changes

git diff v\_7\_6\_2 v\_7\_7\_0 > 7.7.0.diff

#### _The Git commands above should be executed on v\_7\_7\_0\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.response.redirect.location.relative

[com/vordel/apiportal/api/PortalServletContainer.java; L40](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/apiportal/api/PortalServletContainer.java#L40)

true

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; HTTP; Redirects

2

com.axway.apimanager.proxy.defaultPath

[com/vordel/apiportal/api/portal/controller/ApiProxyController.java; L210](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/apiportal/api/portal/controller/ApiProxyController.java#L210)

/api

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Virtualization

3

com.axway.apimanager.eventtimestamp.ignorettl

[com/vordel/apiportal/api/portal/controller/EventTimestampController.java; L21](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/apiportal/api/portal/controller/EventTimestampController.java#L21)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Cassandra; KPS

4

com.axway.apimanager.api.data.cache

[com/vordel/apiportal/common/APIPortalConstants.java; L229](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/apiportal/common/APIPortalConstants.java#L229)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Cache

5

com.axway.oauth.scopes.check.all.legacy

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L58](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L58)

false

This property was introduced as part of a fixport. See 7.5.3.SP9 for more details

API Gateway; API Manager; OAuth

6

com.vordel.apimanager.uri.path.trailingSlash.preserve

[com/vordel/circuit/rbac/policy/OperationAtom.java; 203](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/circuit/rbac/policy/OperationAtom.java#L203)

[com/vordel/coreapireg/runtime/PathResolverResult.java; L70](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/coreapireg/runtime/PathResolverResult.java#L70)

false

This property was introduced as part of a fixport. See 7.5.3.SP8 for more details

API Manager; Runtime

7

com.axway.soap.faultnamespace

[com/vordel/circuit/soap/SOAPFaultDocumentCreator.java; L47](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/circuit/soap/SOAPFaultDocumentCreator.java#L47)

http://www.vordel.com/soapfaults

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Gateway; WSDL; SOAP; Runtime

8

com.coreapireg.apimethod.contenttype.legacy

[com/vordel/coreapireg/runtime/broker/InvokableMethod.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0/src/java/src/com/vordel/coreapireg/runtime/broker/InvokableMethod.java#L49)

false

This property was introduced as part of a fixport. See 7.5.3.SP10 for more details

API Manager; Runtime

### 7.7.0.SP1

#### Synopsis

  

Release Notes

Available on [Webliv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?45tbqhl2t4g2d7jiafsnk3d9e1&fileId=73780&readmeId=38290&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.7+SP+1+Readme)

New Java System Properties

4

Git tag

[v\_7\_7\_0\_SP1](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_0_SP1/)

Git checkout command

git checkout tags/ v\_7\_7\_0\_SP1

List of commits

git log --pretty=oneline v\_7\_7\_0...v\_7\_7\_0\_SP1

List of changes

git diff v\_7\_7\_0 v\_7\_7\_0\_SP1 > 7.7.0.SP1.diff

_The Git commands above should be executed on v\_7\_7\_0\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0.SP1 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.use404AuthSuccessNoMatch

[com/vordel/apiportal/runtime/Broker.java; L166](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP1/src/java/src/com/vordel/apiportal/runtime/Broker.java#L166)

false

Specifies whether or not a HTTP  **`404 Not Found`** error response is sent to the client when an authenticated, but unsuccessful API match occurs.

Setting this property to **false** (the default) will result in a HTTP `403 No match found for request` error being returned.

API Manager; HTTP; Runtime

2

com.axway.apigw.smime.sign.md

[com/vordel/circuit/smime/SignProcessor.java; L41](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP1/src/java/src/com/vordel/circuit/smime/SignProcessor.java#L41)

sha256

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Gateway; SMIME; Filter Runtime

3

com.axway.apimanager.csrf

[com/vordel/common/apiserver/filter/CsrfProtectionFilter.java; L154](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP1/src/java/src/com/vordel/common/apiserver/filter/CsrfProtectionFilter.java#L154)

true

This property was introduced as part of a fixport. See 7.5.3.SP9 for more details

API Manager; CSRF

4

failDecryptNoSignature

[com/vordel/circuit/pgp/DecryptProcessor.java; L182](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP1/src/java/src/com/vordel/circuit/pgp/DecryptProcessor.java#L182)

false

Specifies whether or not to raise a PGPException if `**${content.body}**` contains an unsigned OnePass signature.

**Note**: This property was renamed from pgpFailDecryptNoSignature.

API Gateway; PGP; Filter Runtime

### 7.7.0.SP2

#### Synopsis

  

Release Notes

Available on [Webliv](https://delivery.axway.int/GUI/file/GUI_file_readme.php?45tbqhl2t4g2d7jiafsnk3d9e1&fileId=74993&readmeId=38812&pdtId=394)

Available on [Confluence](https://techweb.axway.com/confluence/display/RDAPI/Axway+API+Gateway+and+API+Manager+7.7+SP+2+Readme)

New Java System Properties

8

Git tag

[v\_7\_7\_0\_SP2](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_0_SP2/)

Git checkout command

git checkout tags/ v\_7\_7\_0\_SP2

List of commits

git log --pretty=oneline v\_7\_7\_0\_SP1...v\_7\_7\_0\_SP2

List of changes

git diff --no-renames v\_7\_7\_0\_SP1 v\_7\_7\_0\_SP2 > 7.7.0.SP2.diff

_The Git commands above should be executed on v\_7\_7\_0\_branch_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0.SP2 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.kps.cache.ignorenull

[com/vordel/kps/TableInternal.java; L26](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/kps-core/src/main/java/com/vordel/kps/TableInternal.java#L26)

false

Specifies whether or not items queried from Cassandra that are 'not found' (i.e. null) are stored in the KPS table cache. When this property is set to **true** and a null item is encountered, it will force a subsequent read from Cassandra.

'Not found'/null items store an empty map in the cache.

API Gateway; API Manager; Cassandra; KPS

2

com.axway.apimanager.apiclient.cache.response.legacy

[com/vordel/apiportal/api/portal/controller/ApiClientCache.java; L1113](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/apiportal/api/portal/controller/ApiClientCache.java#L1113)

false

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Manager; Cache

3

com.vordel.apimanager.swagger.method.singleslash.ignore

[com/vordel/apiportal/runtime/virtualized/VApiRuntime.java; L73](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/apiportal/runtime/virtualized/VApiRuntime.java#L73)

false

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Manager; Swagger; Virtualization

4

distributed.ehcache.cache.reload.pause.secs

[com/vordel/circuit/cache/CacheContainer.java; L128](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/circuit/cache/CacheContainer.java#L128)

5

This property was introduced as part of a fixport. See 7.5.3.SP7 for more details

API Gateway; EHCache

5

com.axway.oauth.scopes.openid.allow

[com/vordel/circuit/oauth/common/OAuthScopeUtils.java; L102](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/circuit/oauth/common/OAuthScopeUtils.java#L102)

true

This property was introduced as part of a fixport. See 7.5.3.SP12 for more details

API Gateway; API Manager; OAuth; OpenID Connect

6

com.axway.json.parser.legacy

[com/vordel/mime/JSONBody.java; L49](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/mime/JSONBody.java#L49)

[com/vordel/coreapireg/runtime/broker/parameters/JSONBodyValidator.java; L76](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/coreapireg/runtime/broker/parameters/JSONBodyValidator.java#L76)

false

This property was introduced as part of a fixport. See 7.6.2.SP5 for more details

  

7

sftp.close.timeout

[com/vordel/fileservice/sftp/SSHServer.java; L231](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/fileservice/sftp/SSHServer.java#L231)

5

This property was introduced as part of a fixport. See 7.5.3.SP13 for more details

API Gateway; SFTP

8

OpenTrafficLog.configurationFile

[com/vordel/opentraffic/OpenTrafficLogger.java; L79](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/opentraffic/OpenTrafficLogger.java#L79)

[com/vordel/opentraffic/OpenTrafficLogger.java; L80](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_SP2/gwplatform/vordel-core/src/main/java/com/vordel/opentraffic/OpenTrafficLogger.java#L80)

  

This property was introduced as part of a fixport. See 7.6.2.SP1 for more details

API Gateway; Logging; Open Traffic

### 7.7.0.SP20200131

#### Synopsis

  

Release Notes

Available on [Open Docs](https://axway-open-docs.netlify.app/docs/apim_relnotes/20200130_apimgr_relnotes/)

New Java System Properties

2

Git tag

[v\_7\_7\_20200130](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_20200130/)

Git checkout command

git checkout tags/ v\_7\_7\_20200130

List of tags

git log --simplify-by-decoration --decorate --pretty=oneline "master" | fgrep 'tag: '

List of commits

git log --pretty=oneline v\_7\_7\_0...v\_7\_7\_20200130

List of changes

git diff --no-renames v\_7\_7\_0 v\_7\_7\_20200130 > 7.7.0.SP20200130.diff

_The Git commands above should be executed on master_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0.SP20200131 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.apimanager.backend.url.validation.hostname.allowunderscore

[com/vordel/common/regex/RegExPatternConstants.java; L47](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_20200130/gwplatform/vordel-api-model/src/main/java/com/vordel/common/regex/RegExPatternConstants.java#L47)

false

Specifies whether or not the underscore character, '\_', is permitted for URI hostnames.

**Note**: This system property has since been removed from the product as the functionality  contravenes [https://tools.ietf.org/html/rfc7230#section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4 "Follow link") and [https://tools.ietf.org/html/rfc3986#section-3.2.2](https://tools.ietf.org/html/rfc3986#section-3.2.2 "Follow link"), which clearly state that underscores in hostnames are permitted.

API Manager

2

com.axway.apimanager.api.model.disable.confidential.fields

[com/vordel/apiportal/api/portal/model/serialization/annotations/Confidential.java; L29](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_20200130/gwplatform/vordel-persistence/src/main/java/com/vordel/apiportal/api/portal/model/serialization/annotations/Confidential.java#L29)

true

This property was introduced as part of a fixport. See 7.6.2.SP5 for more details

API Manager; Swagger; Export

### 7.7.0.SP20200330

#### Synopsis

  

Release Notes

Available on [Open Docs](https://axway-open-docs.netlify.app/docs/apim_relnotes/20200330_apimgr_relnotes/)

New Java System Properties

1

Git tag

[v\_7\_7\_0\_20200331-3](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_0_20200331-3/)

Git checkout command

git checkout tags/ v\_7\_7\_0\_20200331-3

List of commits

git log --pretty=oneline v\_7\_7\_20200130...v\_7\_7\_0\_20200331-3

List of changes

git diff --no-renames v\_7\_7\_20200130 v\_7\_7\_0\_20200331-3 > 7.7.0.SP20200331.diff

_The Git commands above should be executed on master_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0.SP20200330 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.vordel.apiportal.Organization.RegistrationToken.tokenLength

[com/vordel/apiportal/api/portal/controller/OrganizationController.java; L105](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_20200331-3/gwplatform/vordel-core/src/main/java/com/vordel/apiportal/api/portal/controller/OrganizationController.java#L105)

32

Specifies the length of the organization registration token.

API Manager; User Registration

### 7.7.0.SP20200530

#### Synopsis

  

Release Notes

Available on [Open Docs](https://axway-open-docs.netlify.app/docs/apim_relnotes/20200530_apimgr_relnotes/)

New Java System Properties

1

Git tag

[v\_7\_7\_0\_20200530-2](https://git.ecd.axway.int/apigw/vordel/-/commits/v_7_7_0_20200530-2/)

Git checkout command

git checkout tags/ v\_7\_7\_0\_20200530-2

List of commits

git log --pretty=oneline v\_7\_7\_0\_20200331-3...v\_7\_7\_0\_20200530-2

List of changes

git diff --no-renames v\_7\_7\_0\_20200331-3 v\_7\_7\_0\_20200530-2 > 7.7.0.SP20200530.diff

_The Git commands above should be executed on master_

#### Detail

The following table lists the Axway-defined Java System Properties introduced in the 7.7.0.SP20200530 release

  

  

System Property

Occurrences

Default Value

Description

Context

1

com.axway.coreapi.method.servicecontext.clientattr

[com/vordel/coreapireg/runtime/CoreApiBroker.java; L74](https://git.ecd.axway.int/apigw/vordel/-/blob/v_7_7_0_20200530-2/gwplatform/vordel-apibroker/src/main/java/com/vordel/coreapireg/runtime/CoreApiBroker.java#L74)

false

This property was introduced as part of a fixport. See 7.5.3.SP13 for more details

API Manager

### 7.7.0.SP20200730

#### Synopsis

  

Release Notes

  

New Java System Properties

0

Git tag

[v\_7\_7\_0\_20200730](https://git.ecd.axway.int/apigw/vordel/-/commits/master)

Git checkout command

git checkout master

List of commits

git log --pretty=oneline v\_7\_7\_0\_20200530-2...HEAD

List of changes

git diff --no-renames v\_7\_7\_0\_20200530-2 HEAD > 7.7.0.SP20200730.diff

_The Git commands above should be executed on master_

#### Detail

No new Axway-defined Java System Properties were introduced in the 7.7.0.20200730 release

  

Comments:
---------

great doc  [David Shiel](https://techweb.axway.com/confluence/display/~dshiel)

![](images/icons/contenttypes/comment_16.png) Posted by cquinn at Aug 26, 2020 09:09

Document generated by Confluence on Sep 08, 2020 10:58

[Atlassian](http://www.atlassian.com/)