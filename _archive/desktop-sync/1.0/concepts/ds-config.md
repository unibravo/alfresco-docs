---
author: Alfresco Documentation
source: 
audience: 
category: [Alfresco Server, Administration, Configuration]
keyword: [solr, search, lucene]
---

# Configuring Desktop Sync

If you're an IT administrator, you can configure Desktop Sync for central installation purposes.

**Desktop Sync configuration**

You can configure Desktop Sync using the AlfrescoSync.conf file located at <userHome\>\\AppData\\Local\\Alfresco.

Using the configuration file, you can update:

-   timer values, such as polling and retry intervals
-   sync constraint patterns for files/folders to be ignored by Desktop Sync
-   disk space limits
-   custom content type mappings for particular file extensions
-   user interface defaults and customization
-   network access configuration
-   debug logging

The configuration properties values are case sensitive and use the format `name.subname=value`, for example, `syncmanager.deferFileSyncTimer=15`.

**Sync manager timers**

|Property|Default value|Description|
|--------|-------------|-----------|
|`alfrescosync.initialPollInterval`|15 seconds|Sets the number of seconds before the first server polling after application startup.|
|`alfrescosync.pollingInterval`|300 seconds \(5 minutes\)|Sets the number of seconds between polling of the sync server for server-side file change events. This does not affect the processing of desktop file change events; they are always processed immediately.

|
|`syncmanager.deferFileSyncTimer`|15 seconds|Sets the number of seconds between retries of a deferred update for a file, such as when an application still has the file open.|
|`syncmanager.deferDelayRetryTimer`|3600 seconds \(60 minutes\)|Sets the number of seconds to retry syncing a deferred update for a file. If you wish to override the default setting, add this property in the configuration file. Maximum allowed value is 10 hours.

|
|`syncmanager.deferOnlineCheckTimer`|60 seconds|Sets the number of seconds between retries when a server connection is offline. This may be due to server being down, no network connection, or no route to the server.|
|`syncmanager.consistencyCheckRetryInterval`|300 seconds \(5 minutes\)|Sets the number of seconds between consistency check retry attempts. A consistency check may be aborted when too many filesystem changes are received during the consistency check scan.

|
|`syncmanager.freeSpaceCheckTimer`|120 seconds \(2 minutes\)|Sets the number of seconds between free space checks when syncing has been paused due to the local free disk space threshold being reached.|

**Sync manager constraints**

Use the sync constraints configuration section to specify desktop files and folders that should not be synced to the server, such as temporary files or work files.

Constraint rules are specified as `syncmanager.constraint.<n>=<rule>`, where:

-   `<n>` is an ascending number starting at one
-   `<rule>` is speciifed as `<ruletype>|<parameter>[,<parameter>,...]`

The available constraint rule types are:

|Constraint rule type|Description|
|--------------------|-----------|
|`FileExt`|Ignores files with a particular file extension. Multiple extensions can be specified per rule.|
|`Name`|Ignores files with a particular name. Multiple names can be specified per rule.|
|`NamePattern`|Ignores files that match a regular expression. Multiple regular expressions can be specified per rule.|
|`FolderNamePattern`|Ignores folders that match a regular expression. Multiple regular expressions can be specified per rule.??|

Here is a sample??sync constraints configuration section:

```
# Sync constraints

syncmanager.constraint.1=FileExt|.tmp,.temp
syncmanager.constraint.2=Name|desktop.ini,thumbs.db
syncmanager.constraint.3=NamePattern|~\\$.*\\.doc,~\\$.*\\.docx,~\\$.*\\.xlsx,~\\$.*\\.pptx
syncmanager.constraint.4=NamePattern|\\._.*
syncmanager.constraint.5=NamePattern|^[0-9A-F]{8}
syncmanager.constraint.6=FolderNamePattern|^[0-9A-F]{8}
```

**Disk space limits**

You can configure Desktop Sync to only sync content when the available disk space on a computer is above a specific limit \(defined by `syncmanager.freeSpaceLimit`\). Below this value, Desktop Sync will pause syncing, until the available disk space reaches an upper limit \(`syncmanager.freeSpaceRestart`\). When the available disk space reaches this value, syncing will restart.

To configure the disk space limits, add these entries to your configuration file:

```
# Free space limits
    
syncmanager.freeSpaceLimit=3G
syncmanager.freeSpaceRestart=3500M
```

**Note:** Since floating point values aren't valid, you can set a property value to `3500M` \(that's 3500MB\) to represent 3.5GB. Also, `syncmanager.freeSpaceRestart` should have a higher value than `syncmanager.freeSpaceLimit`.

The free space properties are specified as `syncmanager.freeSpace<Property>=<value>[<scale>]` where:

-   `<value>` is an integer
-   `<scale>` provides optional scaling using `K|M|G|T` for kilobyte, megabyte, gigabyte, and terabyte values
-   add `+/-` in front of the `<value>` to specify values relative to the currently available free space: '+' specifies the limit above the currently available disk space; '-' specifies the limit below the currently available disk space.

To specify a simple limit, without using any scaling, use this example:

```
syncmanager.freeSpaceLimit=1073741824
```

In this example, the minimum amount of disk space required for content syncing is set to 1GB.

To specify relative values above the current available disk space, use this example:

```
syncmanager.freeSpaceLimit=+2G
syncmanager.freeSpaceRestart=+2500M
```

In this example, the `freeSpaceLimit` is set 2GB above the available space and the `freeSpaceRestart` is set 2.5GB above the current available space. So if you have 3GB of free disk space and your `freeSpaceLimit` is set 2GB, when the free space on your hard drive reaches 5GB, sync will pause. Sync only resumes once your hard drive has 5.5GB of free space.

You can also specify relative values below the current available disk space, use this example:

```
syncmanager.freeSpaceLimit=-3G
syncmanager.freeSpaceRestart=-3500M
```

In this example, the `freeSpaceLimit` is set 3GB below the available space and the `freeSpaceRestart` is set 3.5GB below the current available space.

**CMIS transfers**

Use the CMIS transfer properties to control problems with slow or stalled data transfers when the Desktop Sync is downloading or uploading content to/from the Alfresco CMIS server.

|Property|Default value|Description|
|--------|-------------|-----------|
|`cmis.lowSpeedTime`|15 seconds|Specifies the number of seconds of low speed transfer that are required for a data transfer to be aborted.|
|`cmis.lowSpeedLimit`|1 bytes per second|Specifies the number of bytes per second that is considered to be a slow data transfer.|

**CMIS Content Type Mappings**

When files are uploaded to the Alfresco CMIS server they should have a mimetype associated with them. Desktop Sync contains a list of built-in file extension to mimetype mappings, but in some cases it may be necessary to add new mappings or override existing mappings.

The CMIS content type mappings are specified as `cmis.contentType.<n>=<extension>,<mimetype>`, where `<n>` is an ascending number starting at one.

Here is a sample CMIS content type mapping configuration section:

```
# CMIS content type mappings

cmis.contentType.1=pptx,application/vnd.ms-powerpoint
cmis.contentType.2=test,application/test
```

**User interface**

Use the user interface properties to configure Desktop Sync UI.

|Property|Default value|Description|
|--------|-------------|-----------|
|`syncui.defaultServer`|??|Specifies a default server URL to be entered in the login screen when Desktop Sync is run for the first time \(usually at the end of the installation process\).The user can change the URL on the login screen, if required.

|
|`syncui.showConsistencyCheck`|`true`|Specifies whether the **Consistency Check** option is available in Desktop Sync.As an Administrator, you can disable this option as consistency check scans all the folders that are synced to Desktop Sync, and this may effect your server's performance.

|
|`syncui.showRepositoryTab`|`true`|Controls whether the content selection tree view of Alfresco is shown.|

**Networking**

|Property|Default value|Description|
|--------|-------------|-----------|
|`net.SSLVerifyPeer`|`true`|Specifies whether an SSL connection to the Alfresco server does full SSL verification. If the Alfresco server is using a self-signed certificate for SSL, the value must be set to `false`.|
|`net.syncServer.SSLVerifyPeer`|`true`|Specifies whether an SSL connection to the sync server does full SSL verification. If the sync server is using a self-signed certificate for SSL, the value must be set to `false`.|

**Debug logging**

Desktop Sync can generate a lot of debug information or error level logging information.

The log output configuration contains two sections.

Use the first section to setup the log location, default logging level, formatting, rotation, and compression.

Here is a sample logging configuration section:

```
# Logging

logging.loggers.root.channel = c1
logging.loggers.root.level = error
logging.channels.c1.class = FileChannel
logging.channels.c1.path = ${system.homeDir}\\AppData\\Local\\Alfresco\\AlfrescoDesktopSync.log
logging.channels.c1.formatter.class = PatternFormatter
logging.channels.c1.formatter.pattern = %Y-%m-%d %H:%M:%S.%c %N[%P]:%s:%q:%t
logging.channels.c1.rotation=daily
logging.channels.c1.compress=true
logging.channels.c1.purgeAge=7 days
logging.channels.c1.archive=timestamp
logging.formatters.f1.class = PatternFormatter
logging.formatters.f1.times = UTC
```

This logging configuration will create a log file called??AlfrescoDesktopSync.log??in the??<userHome\>\\AppData\\Local\\Alfresco??folder. The log files are rotated daily and the old log files are compressed. Only the last 7 days of log files are kept.

Use the later section of the log output configuration to enable debug output from different components of Desktop Sync. This is done by configuring individual logging levels as shows below:

```
logging.loggers.l<n>.name = <logging level name>
logging.loggers.l<n>.level = <debug|error>
```

where `<n>` is an ascending index number starting at one. Both the configuration lines must use the same index number.

The available debug logging levels are:

|Debug logging level|Description|
|-------------------|-----------|
|SyncManager|The top level sync component|
|SyncTarget|Handles the connection between a single local/remote folder|
|DirRouter|Routing of local filesystem events|
|Win32DirWatcher|Low level local filesystem event handling|
|LocalFileStore|Local file I/O|
|Win32FileStoreMonitor|Local filesystem monitoring|
|CMISFileStore|Remote file I/O|
|AlfrescoFileStoreMonitor|Remote filesystem event polling|
|PocoSQLiteDB|Sync database access|
|CMISAPI|CMIS API calls|
|SyncServiceAPI|Sync service API calls|
|AlfrescoAPI|Alfresco API calls|
|SyncClientAPI|Shell extension to sync client application calls|
|RESTAPI|Base REST API processing|
|LibCURLAPI|LibCurl low level HTTP network I/O processing|
|LocalSyncScanner|Sync manager startup local sync area scanner|
|WaterMark|File watermarking \(not currently used\)|
|AccountSetup|Account setup, create/remove sync targets|
|DeleteAddSyncPattern|Delete/add \(drag/drop cut/paste\) local event special handling|
|DeleteRenameSyncPattern|Delete/rename local event special handling|
|RenameShuffleSyncPattern|MS Office rename/shuffle local event special handling|
|TargetMoveToFrom|Move files/folders between sync targets special handling|
|TargetRename|Sync target parent folder rename special handling|
|TargetDelete|Sync target delete parent folder special handling|
|SyncServiceRouter|Shared synchronization service polling/event routing|

Here's a sample Desktop Sync logging configuration:

```
# Logging

logging.loggers.root.channel = c1
logging.loggers.root.level = error
logging.channels.c1.class = FileChannel
logging.channels.c1.path = ${system.homeDir}\\AppData\\Local\\Alfresco\\AlfrescoDesktopSync.log
logging.channels.c1.formatter.class = PatternFormatter
logging.channels.c1.formatter.pattern = %Y-%m-%d %H:%M:%S.%c %N[%P]:%s:%q:%t
logging.channels.c1.rotation=daily
logging.channels.c1.compress=true
logging.channels.c1.purgeAge=7 days
logging.channels.c1.archive=timestamp
logging.formatters.f1.class = PatternFormatter
logging.formatters.f1.times = UTC

logging.loggers.l1.name = SyncManager
logging.loggers.l1.level = debug

logging.loggers.l2.name = SyncTarget
logging.loggers.l2.level = debug

logging.loggers.l3.name = Win32DirWatcher
logging.loggers.l3.level = error

logging.loggers.l4.name = Win32FileStoreMonitor
logging.loggers.l4.level = error

logging.loggers.l5.name = CMISAPI
logging.loggers.l5.level = error

logging.loggers.l6.name = PocoSQLiteDB
logging.loggers.l6.level = error
```

For more details on the logging configuration, see the [PocoProject](http://www.pocoproject.org) documentation.

**Parent topic:**[Using Desktop Sync](../concepts/desktopsync-using.md)

