Multiple pharmacy approval numbers can be configured by repeating Site sections with site details. At least 1 Site section must be supplied for each database.
See code sample below for an example.
Refer to Additional help for configuration file set up for more information about these settings.

|Key|Description|Value|Config Req.|
|---|---|---|---|
|SiteSid|The Site ID in the database||Required|
|ApprovalNum|PBS Online Approval Number||Required|
|LocationID|This value would have been issued by DXC to the site when enabling PBS Online. Contact DXC support if uncertain.||Required|

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
    
