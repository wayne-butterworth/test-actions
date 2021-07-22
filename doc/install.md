i.Pharmacy Installation Guide

<div style="page-break=after: always;"></div>

# PBS Online 7

# This Document
## Purpose
The purpose of this document is to describe the installation and configuration of Dedalus’ i.Pharmacy PBS Online services to enable single and multisite PBS online claiming and reporting.

The i.Pharmacy PBS Online Service has have been developed to use the Services Australia Web Services Adaptor for the Encoding and Decoding of secure messages 
sent and received from Medicare Australia for PBS Online. 

## Audience
Dedalus and Site technical staff responsible for installing interface software for i.Pharmacy will find this document of use. 

## Important Notes
* Medicare Australia must be contacted prior to go live to advise them that your hospital is intending to use the PBS Online functionality.   
* The folder paths for installation directories are included as examples only. Installation paths will vary depending on system architecture and operating systems. 
Please verify the existence and appropriateness of any folder paths included in this document. 
* There are different activities, depending on your upgrade path.  Please read each section carefully to ensure you are installing or upgrading using the appropriate steps.

## Prerequities
Ensure that the PBS Online server environment meets the following prerequisites 

* .NET Framework 5.0 or higher 
* OpenSSL or equivalent, for creating a client certificate to connect to PCMS
* HTTPS access to the Medicare claim submission URL 
* Dedalus Proda Certificate Management Service (PCMS) is accessable

# Overview
## Using This Document
If you are not comfortable with installing Windows applications or editing JSON files, it is recommended that you do not proceed with this installation. 
## Definitions
Throughout the document, several system dependant tokens are used as outlined in the table below: 

|Token|Definition|
|---|---|
|[ProgramsPath] |Substitute the Program Files path depending upon region and architecture and operating system. This path may vary between various components that make up the PBS online solution.  <br>Examples:  <br>C:\\Program Files (x86) <br>C:\\Program Files |
|[InstallationPath] |The path where the PBS Online Service has been installed. <br>Examples: <br>C:\\Program Files (x86)\\i.Pharmacy PBS Online\\ <br>E:\\Apps\\i.Pharmacy PBS Online\\ |
|[PackagePath] |Substitute this token with the path to the PBS Online installation folder. <br>Example: <br>C:\\installs\\PBSOnlineTemp |
|[PBSServer] |Substitute this token with the name of the PBS Online Server. <br>Examples: <br>PBSOnlineServer / 192.168.123.321 |
|[PBSDB] |Substitute for the name of the i.Pharmacv Database: <br>Example: <br>iPharmacyProd |
|[PBSDBPassword] |Substitute with a clear text password that has been used by i.Pharmacy applications, against the database being used. <br>Example: <br>stoccapwd |
|[x.x] <br>[x.x.x] <br>[x.x.x.x] |The version number of the product in various format. Shorter version number (i.e. In the [x.x] or [x.x.x] format) refer to any subsequent version commencing with these major and minor version numbers. <br>Examples: <br>6.0, 6.0.2, 6.0.2.1 |

## Components of Installation 

Full administration rights to the computer are essential to complete these processes: 

* MSI components: 
* i.Pharmacy PBS Online Service – DXC developed integration service to encrypt and decrypt PBS Online messages. 

## Version compatibility 

This installation is compatible with, or installs the following versions.   

|Component |Version |Notes |
|---|---|---|
|i.Pharmacy |8.5 and above ||
|PCMS|1.0 and above||

# Verify Internet access from server 

Open Internet Explorer and enter the following URL address: 

 <http://www2.medicareaustralia.gov.au/>

The result should show a page like the following – with a logo; 

 

If this page does not display, ensure that there is access from this server to that URL.  

# Upgrade Guide 

**IMPORTANT!** For this major release, *ALL* components require updating or removal. 

## Upgrade overview 

The following information provides a brief overview of the upgrade process: 

1. Stop PBS Online and PBS Reconciliation services, back up configuration files, and uninstall (see below) 

2. Stop Tomcat service, back up configuration files, and uninstall (see below) 

3. Backup and then delete the `[ProgramsPath]\CSC\i.Pharmacy PBS Online` folder 

4. Review Java usage, and uninstall if Java is not longer required. 

5. Install the new PBSOnline  

6. Configure the Service

7. Start the new PBS Online service

# Uninstall previous versions of PBS Online

Configuration files from previous versions are not compatible with version 7 of PBSOnline. However, having the previous configuration available 
for reference will be helpful.

1. Stop the Tomcat Service using the Windows Services feature. This step is necessary to undeply the Server Adapter and Medicare Logic Packs. 

2. Make a backup of any of the previously installed i.Pharmacy installations. Examples paths are provided below, however the exact name of the folders may vary depending on the environment: 

`[ProgramsPath]\iSOFT\iPharmService `

`[ProgramsPath]\CSC\i.Pharmacy PBS Online `

3. Uninstall the existing services and remove the old folders: 

* For version 2.x 

i. Open Command Prompt (with elevated privileges as necessary) and execute the following command: 

`sc delete iPharmService `

ii. Verify the output below: 

![output from sc delete iPharmService](doc/DeleteService.png)

* For versions 3.x – 5.x: 

Uninstall any existing PBS services using the Windows Uninstaller feature. Example program names are provided below, however the exact name may vary depending on the environment: 

PBSOnlineService 

PBSReconciliationService 

4. Delete the entire `[ProgramsPath]\CSC\i.Pharmacy PBS Online folder `


##	Uninstall previous versions of Tomcat and the JDK

For this major release, the JDK, JRE and Tomcat are no longer required, and may be un-installed if they are not used by other programmes.

# Installing PBS Online 7

Following the removal of previous version of PBS Online Services and other components the following section explains how to install the latest version of PBS Online.

Depending on the Operating System you may require elevated permissions, such as selecting the “Run as Administrator” option to perform any of the steps below.

##	Dependencies Overview

These installation instructions are intended for 64-bit Windows Operating Systems.
Please ensure that the required components are installed before continuing: 

*	.NET Framework 5.0

##	Install the PBS Online Solution
1.	Execute the installation file `[PackagePath]\PBSOnlineService.msi`.
2.	Click Next to continue
3.	Optionally, change the install location.
4.  Click Next to continue
4.  Click Install to complete the installation

# Add an endpoint for PBS Online to the PCMS
Refer to PCMS documentation for more details.

## Add a Service Provider URL for the PBSOnline Url

In the `ServiceProciderUrls` section of the PCMS configuration, add an entry for the url https://medicareaustralia.gov.au/PBS

eg

```
 "ServiceProviderUrls": [
    {
      "Code": "PBSOL",
      "Url": "https://medicareaustralia.gov.au/PBS"
    }
  ],
  ```

## Create Local Certificate Store

1. Create Local Certificates

Create Root Key:

`openssl.exe genrsa -des3 -out rootCA.key 4096`

Create and self sign the Root Certificate:

`openssl.exe req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt`

Create the certificate key:

`openssl.exe genrsa -out mydomain.com.key 2048`

Create the signing (csr):

`openssl.exe req -new -key mydomain.com.key -out mydomain.com.csr`

Generate the certificate using the mydomain csr and key along with the CA Root key:

`openssl.exe x509 -req -in mydomain.com.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out mydomain.com.crt -days 500 -sha256`

Convert CA cert and key to pfx file

`openssl.exe pkcs12 -export -in rootCA.crt -inkey rootCA.key -out CA.pfx`

Convert client cert and key to pfx file

`openssl.exe pkcs12 -export -in mydomain.com.crt -inkey mydomain.com.key -out mydomain.com.pfx -certfile rootCA.crt`

2. Install “CA.pfx" into “Current User” and “Trusted Root Certification Authorities"

3. Install “mydomain.com.pfx" into “Current User" and “Personal”

4. Connect to PCMS ( eg go to https://localhost:5001/Admin/Devices in your browser) and login with User "Admin""

5. Setup and register endpoints and certificates in PCMS.

The url of the Endpoint created is entered into the `appsettings.json` file under the PBS Online directory. Refer to next section for details.


# Configuring PBS Online 7

The PBS Online Service configuration is in the `appconfig.json` file, which is located in `[InstallationPath]\Dedalus\i.Pharmacy PBS Online\PBSOnlinePackage` folder. 
The various sections and configurable settings of the PBS Online Service configuration file are itemised below.
The configuration file can be edited with Notepad or another text editor.

## Service settings
These settings control the service.

|Key|Description|Value|Config Req.|
|---|---|---|---|
|ServiceInterval|Number of seconds to wait before querying the database for the next group of claims.|DXC recommended value: 15|Required|
|RecServiceInterval|Number of seconds to wait before querying the database for the next group of reconciliation report requests.|DXC recommended value: 60|Required|
|AllowableTimeDiff|Number of minutes|DXC recommended value: 15|Required|
|ProductId|Product Identifier for connecting to PBS Online|DXC specified value: "i.PharmacyPBSOnlineV7", <br>do not change unless advised to by Dedalus|Required|
|APIKey|API Key for connecting to PBS Online|DXC specified value: "7c9b6e66273d65c98bfa66b05f22a5d9", <br>do not change unless advised to by Dedalus|Required|
|BasePath|Base url of PBS Online web servies|DXC specified value: "https://test.healthclaiming.api.humanservices.gov.au/claiming/ext-vnd/pbsportal/rest/b2b", <br>do not change unless advised to by Dedalus|Required|
|ProdaTokenUrl|Url of PCMS Endpoint, to request tokens from| eg "https://localhost:5001/Token/Endpoint/Iph001/ServiceProvider/pbsol"|Required|
|PrivateKeyLocation|Location of private key used to connect to PCMS| eg "path\\to\\my.pfx"|Required|
|PrivatekeyPassword|Password of private key used to connect to PCMS||Optional|
|LogMessages|Set to `true` to log request and response messages from the Api|`true` or `false`|Required|
|LogMessagePath|Path to directory to store the Log Messages in||Required if `LogMessages` is `true`|


## Database settings
A single PBS Online instance can now process messages from more than 1 database. At least 1 database must be specified.

|Key|Description|Value|Config Req.|
|---|---|---|---|
|ServerName|Specifies the name of the server|[Server Name]|Required|
|Database|Name of the database|[Database Name]|Required|
|User|Login ID to the database|[stoccauser]|Conditional|
|Password|Password to the database|[stoccapwd] (Unencrypted)|Required|


Multiple pharmacy approval numbers can be configured by repeating Site sections with site details. At least 1 Site section must be supplied for each database.
See code sample below for an example.
Refer to Additional help for configuration file set up for more information about these settings.

|Key|Description|Value|Config Req.|
|---|---|---|---|
|SiteSid|The Site ID in the database||Required|
|ApprovalNum|PBS Online Approval Number||Required|
|LocationID|This value would have been issued by DXC to the site when enabling PBS Online. Contact Dedalus support if uncertain.||Required|

    {
        "ServerName": "vmaptm-dbs03\\SQL2012_B",
        "Database": "iPharmacy_100_sup",
        "User": "stoccauser",
        "Password": "15186281",
        "Sites": [
            {
                "SiteSid": 45,
                "ApprovalNum": "HQ178G",
                "LocationID": "ISP00003"
            },
            {
                "SiteSid": 8,
                "ApprovalNum": "HW014H",
                "LocationID": "ISP00002"
            }
        ]
    }

    
## Logging settings

By default, log files are created in the `C:/PBSOnlineLogs` directory (which will be created, if possible). 
The location and rolling frequency are controlled by the `fileName` element in the `NLog` section. 
The default filename contains the special code `${shortdate}`, which means to place the current date into the filename, and generate a new file each day.

An alternate log file location can be specified by appending a full or relative path name in the value of the `fileName` element, eg:

    "fileName": "C:\\Logs\\PBSOnline-${shortdate}.log",

The service uses the `Nlog` framework for logging, which is highly configurable with regard to logging targets, log rolling, and log formats. 
See https://nlog-project.org/config/ for more details.

    Note: Dedalus cannot provide support for Nlog, and does not recommend changing its configuration other than the log file location.

Log level can be controlled by the `minLevel` element in the `rules` section. Allowable values are:

* "Trace" - Very detailed log messages, potentially of a high frequency and volume. Not recommended, unless specifically asked to by Dedalus support.

* "Debug" -Less detailed and/or less frequent debugging messages. Not recommended, unless specifically asked to by Dedalus support.

* "Info" - Informational messages. Suitable for production use.

* "Warn" - Warnings messages only. Suitable for production use, if "Info" generates too many messages.

* "Error" - Error messages only.

* "Fatal" - Fatal error messages. After a fatal error, the application usually terminates.

# Troubleshooting

## Losing connection to database
If the PBS Online Service is unable to contact a database at start up, or loses connection with a database while it is running, it will continue to process.
The PBS Online Service will attempt to connect to the database regularly, and once connection is established, it will process messages from that database as normal.

When the service is not able to connect to a database, it will generate Error log messages similar to:

```
2021-07-14 10:34:22.3258|ERROR|PBSOnlineFramework.Worker[8]|Unable to open database Data Source=vmaptm-dbs03\SQL2012_B;Initial Catalog=iPharmacy_ANZ_950_sup;User ID=stoccauser;Password=XXXXX:  System.Data.SqlClient.SqlException (0x80131904): A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)
   at System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString connectionOptions, SqlCredential credential, Object providerInfo, String newPassword, SecureString newSecurePassword, Boolean redirectedUserInstance, SqlConnectionString userConnectionOptions, SessionData reconnectSessionData, Boolean applyTransientFaultHandling, String accessToken)
   at System.Data.SqlClient.SqlConnectionFactory.CreateConnection(DbConnectionOptions options, DbConnectionPoolKey poolKey, Object poolGroupProviderInfo, DbConnectionPool pool, DbConnection owningConnection, DbConnectionOptions userOptions)
   at System.Data.ProviderBase.DbConnectionFactory.CreatePooledConnection(DbConnectionPool pool, DbConnection owningObject, DbConnectionOptions options, DbConnectionPoolKey poolKey, DbConnectionOptions userOptions)
   at System.Data.ProviderBase.DbConnectionPool.CreateObject(DbConnection owningObject, DbConnectionOptions userOptions, DbConnectionInternal oldConnection)
   at System.Data.ProviderBase.DbConnectionPool.UserCreateRequest(DbConnection owningObject, DbConnectionOptions userOptions, DbConnectionInternal oldConnection)
   at System.Data.ProviderBase.DbConnectionPool.TryGetConnection(DbConnection owningObject, UInt32 waitForMultipleObjectsTimeout, Boolean allowCreate, Boolean onlyOneCheckConnection, DbConnectionOptions userOptions, DbConnectionInternal& connection)
   at System.Data.ProviderBase.DbConnectionPool.TryGetConnection(DbConnection owningObject, TaskCompletionSource`1 retry, DbConnectionOptions userOptions, DbConnectionInternal& connection)
   at System.Data.ProviderBase.DbConnectionFactory.TryGetConnection(DbConnection owningConnection, TaskCompletionSource`1 retry, DbConnectionOptions userOptions, DbConnectionInternal oldConnection, DbConnectionInternal& connection)
   at System.Data.ProviderBase.DbConnectionInternal.TryOpenConnectionInternal(DbConnection outerConnection, DbConnectionFactory connectionFactory, TaskCompletionSource`1 retry, DbConnectionOptions userOptions)
   at System.Data.ProviderBase.DbConnectionClosed.TryOpenConnection(DbConnection outerConnection, DbConnectionFactory connectionFactory, TaskCompletionSource`1 retry, DbConnectionOptions userOptions)
   at System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
   at System.Data.SqlClient.SqlConnection.Open()
   at PBSOnlineFramework.iPharmacyDataObjects.ExternalMessage.GetExtMsgQ(SqlConnection dbConn, ILogger logger) in C:\Users\wbutterworth\source\github-hcs\TestPBSOnlineMk2\PBSOnlineFramework\iPharmacyDataObjects\ExternalMessage.cs:line 64
ClientConnectionId:00000000-0000-0000-0000-000000000000
Error Number:-1,State:0,Class:20
```

The message will include the connection string of the database that is having issues ( password redacted ) and an explanation of the issue.
