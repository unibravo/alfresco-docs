---
author: [Alfresco Documentation, Alfresco Documentation]
audience: 
---

# Configuring the MySQL database

This section describes how to configure a MySQL database for use with Alfresco.

1.  Install the MySQL database connector.

    The MySQL database connector is required when installing Alfresco with MySQL. The database connector allows MySQL database to talk to the Alfresco server.

    1.  Download mysql-connector-java-5.1.32 from the MySQL download site: [http://dev.mysql.com/](http://dev.mysql.com).

    2.  Copy the JAR file into the /lib directory.

        For example, for Tomcat, copy the JAR file into the <TOMCAT\_HOME\>/lib directory.

2.  Create a database named alfresco.

    If you are using MySQL and require the use of non-US-ASCII characters, you need to set the encoding for internationalization. This allows you to store content with accents in the repository. The database must be created with the UTF-8 character set and the utf8\_bin collation. Although MySQL is a unicode database, and Unicode strings in Java, the JDBC driver might corrupt your non-English data. Ensure that you keep the `?useUnicode=yes&characterEncoding=UTF-8` parameters at the end of the JDBC URL.

    **Note:** You also must ensure that the MySQL database is set to use UTF-8 and InnoDB. Refer to [Configuration settings for using MySQL with Alfresco](../concepts/mysql-config-settings.md).

3.  Increase the maximum connections setting in the MySQL configuration file.

    1.  Locate the configuration file:

        -   Linux: /etc/my.cnf
        -   Windows: c:\\Users\\All Users\\MySQL\\MySQL Server 5.6\\my.ini
    2.  In the mysqld section, add or edit the max\_connections property:

        ```
        max_connections = 275
        ```

    3.  Restart the database.

4.  Create a user named alfresco.

5.  Set the new user's password to alfresco.

6.  Navigate to the <ALFRESCO\_HOME\>/alf\_data/ directory and empty the <contentstore\> directory.

    This is because the contentstore must be consistent with the database. Step 2 created an empty database, and so the contentstore must also be empty.

7.  Open the <classpathRoot\>/alfresco-global.properties.sample file.

8.  Edit the following line with an absolute path to point to the directory in which you want to store??Alfresco??data.

    For example: `dir.root=C:/Alfresco/alf_data`

9.  Uncomment the following properties:

    ```
    db.driver=org.gjt.mm.mysql.Driver
    db.url=jdbc:mysql://${db.host}:${db.port}/${db.name}?useUnicode=yes&characterEncoding=UTF-8 
    ```

10. Set the other database connection properties.

    ```
    db.name=alfresco
    db.username=alfresco
    db.password=alfresco
    db.host=localhost
    db.port=3306
    db.pool.max=275
    ```

    **Note:** Ensure that these database connection properties are not commented out.

11. Copy the keystore directory from the alf\_data directory at the old location to the alf\_data directory at the new location, which is specified in Step 7.

12. \(Optional\) Enable case sensitivity.

    The default, and ideal, database setting for Alfresco is to be case-insensitive. For example, the user name properties in the <configRoot\>\\classes\\alfresco\\repository.properties file are:

    ```
    # Are user names case sensitive?
    user.name.caseSensitive=false
    domain.name.caseSensitive=false
    domain.separator=
    ```

    If your preference is to set the database to be case-sensitive, add the following line to the alfresco-global.properties file:

    `user.name.caseSensitive=true`

13. Save the file without the .sample extension.

14. Restart the Alfresco server.

    If you receive JDBC errors, ensure the location of the MySQL JDBC drivers are on the system path, or add them to the relevant lib directory of the application server.


-   **[Optimizing MySQL to work with Alfresco](../concepts/mysql-config-settings.md)**  
There are some settings that are required for MySQL to work with Alfresco.

**Parent topic:**[Configuring databases](../concepts/intro-db-setup.md)

