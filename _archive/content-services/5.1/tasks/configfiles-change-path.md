---
author: [Alfresco Documentation, Alfresco Documentation]
audience: 
---

# Installing the Tomcat application server

Install an instance of Tomcat 7 manually and modify it to use the correct directory structure and files for Alfresco.

These instructions recommend that you name the required directories as shared/classes and shared/lib because these are the path names used within full Alfresco installations. You can substitute alternative names for these directories. The installation directory for Tomcat is represented as <TOMCAT\_HOME\>.

1.  Download and install Tomcat version 7 following the instructions from [http://tomcat.apache.org](http://tomcat.apache.org).

2.  Create the directories required for an Alfresco installation:

    1.  Create the shared/classes directory.

    2.  Create the shared/lib directory.

3.  Open the <TOMCAT\_HOME\>/conf/catalina.properties file.

4.  Change the value of the `shared.loader=` property to the following:

    `shared.loader=${catalina.base}/shared/classes,${catalina.base}/shared/lib/*.jar`

    **Note:** If you have used alternative names for the directories, you must specify these names in the `shared.loader` property.

5.  Copy the JDBC drivers for the database you are using to the lib/ directory.

6.  Edit the <TOMCAT\_HOME\>/conf/server.xml file.

7.  Set attributes of HTTP connectors.

    Tomcat uses ISO-8859-1 character encoding when decoding URLs that are received from a browser. This can cause problems when creating, uploading, and renaming files with international characters.

    By default, Tomcat uses an 8 KB header buffer size, which might not be large enough for Kerberos and NTLM authentication protocols.

    Locate the `Connector` sections, and then add the `URIEncoding`, `scheme`, `secure`, and `maxHttpHeaderSize` properties.

    ```
    <Connector port="8080" 
    protocol="HTTP/1.1" 
    URIEncoding="UTF-8" 
    connectionTimeout="20000" 
    scheme="https" 
    secure="true"
    redirectPort="8443" 
    maxHttpHeaderSize="32768"/> 
    ```

8.  Save the server.xml file.


When using Internet Explorer versions 7 and 8, if you try to download a document from Alfresco Share running in Tomcat with https \(SSL\) enabled, you might see an error message.??To resolve this issue, add the following line to the `context` element in the <TOMCAT\_HOME\>/conf/context.xml file:

```
<Valve className="org.apache.catalina.authenticator.SSLAuthenticator" securePagesWithPragma="false" />
```

**Parent topic:**[Installing Alfresco on Tomcat](../tasks/alf-tomcat-install.md)

