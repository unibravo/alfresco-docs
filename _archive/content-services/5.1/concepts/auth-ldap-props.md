---
author: [Alfresco Documentation, Alfresco Documentation]
audience: 
category: [Authentication and Security, Authentication, Administration]
keyword: [configuration, LDAP, authentication]
---

# LDAP configuration properties

Both the `ldap` and `ldap-ad` subsystem types support the following configurable properties.

**Note:** The defaults for `ldap` are typical for OpenLDAP and Oracle Directory Server, and the defaults for `ldap-ad` are typical for Active Directory.

**LDAP authentication properties**

-   **ldap.authentication.active**

    This Boolean flag, when true enables use of this LDAP subsystem for authentication. It might be that this subsystem should only be used for user registry export, in which case this flag should be set to false and you would have to chain an additional subsystem such as passthru or kerberos to provide authentication functions.

-   **ldap.authentication.java.naming.referral**

    This property specifies how the referrals sent by AD in the search results are handled by Alfresco. The recommended values are:

    -   **ignore**

        If `ldap.authentication.java.naming.referral=ignore`, the following exception will be thrown when a referral is encountered:

        ```
        javax.naming.PartialResultException: Unprocessed Continuation Reference(s); remaining name 'dc=alfness,dc=com'
        ```

    -   **follow**

        If `ldap.authentication.java.naming.referral=follow`, Alfresco will automatically follow the referral. To be successful, make sure Alfresco can access the referred server. If this property has not been set, then the default is to follow the referrals.

    -   **throw**

        If `ldap.authentication.java.naming.referral=throw`, the following exception will be thrown:

        ```
        com.sun.jndi.ldap.LdapReferralException: Continuation Reference; remaining name 'dc=alfness,dc=com'
        ```

    For more information, see [Referrals in the JNDI](http://docs.oracle.com/javase/jndi/tutorial/ldap/referral/jndi.html).

-   **ldap.authentication.java.naming.security.authentication**

    The mechanism to use to authenticate with the LDAP server. This should be set to one of the standard values listed here or one of the values supported by the LDAP provider. Oracle's LDAP provider supports the following SASL mechanisms. The recommended values are:

    -   **simple**

        The basic LDAP authentication mechanism requiring the user name and password to be passed over the wire unencrypted. You might be able to add SSL for secure access, otherwise this should only be used for testing.

    -   **DIGEST-MD5**

        More secure RFC 2831 Digest Authentication. Note that with Active Directory, this requires your user accounts to be set up with reversible encryption, not the default setting.

-   **ldap.authentication.java.naming.read.timeout**

    Specifies the read timeout in milliseconds for LDAP operations. If Alfresco cannot get a LDAP response within that period, it aborts the read attempt. The integer should be greater than zero. If the integer is less than or equal to zero, no read timeout is specified, which is equivalent to waiting for the response infinitely until it is received.

-   **ldap.authentication.userNameFormat**

    Specifies how to map the user identifier entered by the user to that passed through to LDAP.

    If set to an empty string \(the default for the ldap subsystem\), an LDAP query involving `ldap.synchronization.personQuery` and `ldap.synchronization.userIdAttributeName` will be performed to resolve the DN from the user ID dynamically. This allows directories to be structured and does not require the user ID to appear in the DN.

    If set to a non-empty value, the substring %s in this value will be replaced with the entered user ID to produce the ID passed to LDAP. This restricts LDAP user names to a fixed format. The recommended format of this value depends on your LDAP server.

    -   **Active Directory**

        There are two alternatives:

        -   **User Principal Name \(UPN\)**

            These are generally in the format of `<sAMAccountName>@<UPN Suffix>`. If you are unsure of the correct suffix to use, use an LDAP browser, such as Softerra, to browse to a user account and find its `userPrincipalName` attribute. For example:

            ```
            %s@domain
            ```

        -   **DN**

            This requires the user to authenticate with part of their DN, so might require use of their common name \(CN\) rather than their login ID. It also might not work with structured directory layouts containing multiple organization units \(OUs\). For example:

            ```
            cn=%s,ou=xyz,dc=domain
            ```

    -   **OpenLDAP**

        The format used depends on the value chosen for `ldap.authentication.java.naming.security.authentication`.

        -   **simple**

            This must be a DN and would be something like the following:

            ```
            uid=%s,ou=People,dc=company,dc=com
            ```

        -   **DIGEST-MD5**

            Use this value to pass through the entered value as-is:

            ```
            %s
            ```

    When authenticating against LDAP, users are not always in the same subtree of LDAP. In this situation, it is necessary to support authentication against multiple branches of LDAP. For example, some users who can authenticate using `cn=%s,ou=myCity,ou=myState,o=myCompany` but others can authenticate using `cn=%s,ou=ANOTHERCity,ou=myState,o=myCompany`. Set `ldap.authentication.userNameFormat` to be empty \(the default\), and then it will derive a query from your personQuery to look up a user by UID. This ensures that you can support users in any branch structure.

-   **ldap.authentication.allowGuestLogin**

    Identifies whether to allow unauthenticated users to log in to Alfresco as the 'guest' user.

-   **ldap.authentication.java.naming.factory.initial**

    The LDAP context factory to use. There is no need to change this unless you do not want to use the default Oracle LDAP context factory.

-   **ldap.authentication.java.naming.provider.url**

    The URL to connect to the LDAP server, containing its name and port. The standard ports for LDAP are 389 \(and 636 for SSL\). For example: `ldap://openldap.domain.com:389`

-   **ldap.authentication.escapeCommasInBind**

    Escape commas in the entered user ID when authenticating with the LDAP server? Useful when using simple authentication and the CN is part of the DN and contains commas.

-   **ldap.authentication.escapeCommasInUid**

    Escape commas in the entered user ID when deriving an Alfresco internal user ID? Useful when using simple authentication and the CN is part of the DN and contains commas, and the escaped \\, is pulled in as part of a synchronize operation. If this option is set to true it will break the default home folder provider as space names cannot contain \\ \(backslash character\).

-   **ldap.authentication.defaultAdministratorUserNames**

    A comma separated list of user names to be considered administrators by default. If you are using LDAP for all your users, this maps an LDAP user to be an administrator user. This administrator user can then configure the other admin users or groups by add users and/or groups to the `ALFRESCO_ADMINISTRATORS` group using the Share Admin Tools.

    If you already have a group of administrators in LDAP, you can add the required LDAP group\(s\)to the `ALFRESCO_ADMINISTRATORS` group. This can be set without a server restart.

-   **ldap.synchronization.active**

    This flag enables use of the LDAP subsystem for user registry export functions and decides whether the subsystem will contribute data to the synchronization subsystem. It might be that this subsystem should only be used for authentication, in which case this flag should be set to false.

-   **ldap.synchronization.java.naming.security.authentication**

    The authentication mechanism used to connect to the LDAP server when performing user registry exports. In versions earlier than 3.4 versions, this property was the same as `ldap.authentication.java.naming.security.authentication`. The property should use one of the standard values covered in the Oracle documentation [http://java.sun.com/javase/6/docs/technotes/guides/jndi/spec/jndi/properties.html\#pgfId=999247](http://java.sun.com/javase/6/docs/technotes/guides/jndi/spec/jndi/properties.html#pgfId=999247) or one of the values supported by the LDAP provider. Oracle's LDAP provider supports the SASL mechanisms documented in [http://java.sun.com/javase/6/docs/technotes/guides/jndi/jndi-ldap.html\#SASL](http://java.sun.com/javase/6/docs/technotes/guides/jndi/jndi-ldap.html#SASL). Recommended values are:

    -   **none**

        Use this option if your LDAP server supports connection without a password. Set to none to allow synchronization by using anonymous bind \(note that you will not also need to set the following two properties\).??

    -   **simple**

        This option is the basic LDAP authentication mechanism requiring the user name and password to be passed over the wire unencrypted. You might be able to add SSL for secure access; otherwise, use this option for testing only.

    -   **DIGEST-MD5**

        This option provides a more secure \([RFC 2831](ftp://ftp.isi.edu/in-notes/rfc2831.txt)\) digest authentication. With Active Directory, this requires your user accounts to be set up with reversible encryption, not the default setting.

-   **ldap.synchronization.java.naming.security.principal**

    The LDAP user to connect as for the export operation, if one is required by the `ldap.synchronization.java.naming.security.authentication` authentication mechanism. This should be in the same format as `ldap.authentication.userNameFormat` but with a real user ID instead of `%s`.

    This is the default principal to use \(only used for LDAP sync when `ldap.synchronization.java.naming.security.authentication=simple`\): `ldap.synchronization.java.naming.security.principal=cn\=Manager,dc\=company,dc\=com`??

-   **ldap.synchronization.java.naming.security.credentials**

    The password for this user, if required. The password for the default principal \(only used for LDAP sync when `ldap.synchronization.java.naming.security.authentication=simple`\)?? ldap.synchronization.java.naming.security.credentials=secret??

-   **ldap.synchronization.queryBatchSize**

    If set to a positive integer, this property indicates that RFC 2696 paged results should be used to split query results into batches of the specified size. This overcomes any size limits imposed by the LDAP server. The default value of 1000 matches the default result limitation imposed by Active Directory. If set to zero or less, paged results will not be used.

-   **ldap.synchronization.groupQuery**

    The query to select all objects that represent the groups to export. This query is used in full synchronization mode, which by default is scheduled every 24 hours.

-   **ldap.synchronization.groupDifferentialQuery**

    The query to select objects that represent the groups to export that have changed since a certain time. Should use the placeholder `{0}` in place of a timestamp in the format specified by `ldap.synchronization.timestampFormat`. The timestamp substituted will be the maximum value of the attribute named by `ldap.synchronization.modifyTimestampAttributeName` the last time groups were queried. This query is used in differential synchronization mode, which by default is triggered whenever a user is successfully authenticated that does not yet exist in Alfresco.

-   **ldap.synchronization.personQuery**

    The query to select all objects that represent the users to export. This query is used in full synchronization mode, which by default is scheduled every 24 hours.

-   **ldap.synchronization.personDifferentialQuery**

    The query to select objects that represent the users to export that have changed since a certain time. Should use the placeholder `{0}` in place of a timestamp in the format specified by `ldap.synchronization.timestampFormat`. The timestamp substituted will be the maximum value of the attribute named by `ldap.synchronization.modifyTimestampAttributeName` the last time users were queried. This query is used in differential synchronization mode, which by default is triggered whenever a user is successfully authenticated that does not yet exist in Alfresco.

-   **ldap.synchronization.groupSearchBase**

    The DN below which to run the group queries.

-   **ldap.synchronization.userSearchBase**

    The DN below which to run the user queries.

-   **ldap.synchronization.modifyTimestampAttributeName**

    The name of the operational attribute recording the last update time for a group or user.

-   **ldap.synchronization.timestampFormat**

    The timestamp format. This varies between directory servers.

    -   **Active Directory**

        `yyyyMMddHHmmss'.0Z'`

    -   **OpenLDAP**

        `yyyyMMddHHmmss'Z'`

-   **ldap.synchronization.userIdAttributeName**

    The attribute name on people objects found in LDAP to use as the uid in Alfresco.

-   **ldap.synchronization.userFirstNameAttributeName**

    The attribute on person objects in LDAP to map to the first name property in Alfresco.

-   **ldap.synchronization.userLastNameAttributeName**

    The attribute on person objects in LDAP to map to the last name property in Alfresco.

-   **ldap.synchronization.userEmailAttributeName**

    The attribute on person objects in LDAP to map to the email property in Alfresco.

-   **ldap.synchronization.userOrganizationalIdAttributeName**

    The attribute on person objects in LDAP to map to the organizational ID property in Alfresco.

-   **ldap.synchronization.defaultHomeFolderProvider**

    The default home folder provider to use for people created using LDAP import.

-   **ldap.synchronization.groupIdAttributeName**

    The attribute on LDAP group objects to map to the group name in Alfresco.

-   **ldap.synchronization.groupType**

    The group type in LDAP. The default value is `groupOfNames`.

-   **ldap.synchronization.personType**

    The person type in LDAP. The default value is `inetOrgPerson`. This value only need changing if the??`objectclass`??for mapping groups and users differs from the default provided by Alfresco. Check the documentation of the LDAP server being accessed.

-   **ldap.synchronization.groupMemberAttributeName**

    The attribute in LDAP on group objects that defines the DN for its members.

-   **ldap.authentication.java.naming.security.protocol**

    This sets the security protocol to use for connecting with the LDAP server. This property has a single value of `ssl`. Set this property to `ssl` if the configuration of truststore is required. Leave this property unused if the truststore configuration is not required \(the connection is not secured\).

    **Note:** In order to enable SSL, import the SSL certificate from the LDAP provider into a Java truststore and the `truststore.path`, `truststore.passphrase`, and `truststore.type`properties. To download the certificate, use the following command:

    ```
    openssl s_client -connect ldap.foxpass.com:636 > my-ldap.crt 
    ```

    To import the certificate into a new keystore, use the following command:

    ```
    keytool -importcert -alias LDAP.HOST.FQDN -file my-ldap.crt -keystore my-keystore-filename -storepass my-pass -storetype JCEKS
    ```

-   **ldap.authentication.truststore.path**

    The path to the truststore file on the file system.

-   **ldap.authentication.truststore.passphrase**

    The password for the truststore.

-   **ldap.authentication.truststore.type**

    The type of the truststore, as specified when generating with keytool or another keystore manager. Must be a standard Java Cryptography Keystore.


**LDAP connection pooling configuration properties**

-   **ldap.synchronization.com.sun.jndi.ldap.connect.pool**

    Use this property to enable or disable connection pooling for synchronization.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.authentication**

    A list of space-separated authentication types of connections that may be pooled. Valid types are `none`, `simple`, and `DIGEST-MD5`.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.debug**

    A string that indicates the level of debug output to produce. Valid values are `fine` \(trace connection creation and removal\) and `all` \(all debugging information\).

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.initsize**

    The string representation of an integer that represents the number of connections per connection identity to create when initially creating a connection for the identity.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.maxsize**

    The string representation of an integer that represents the maximum number of connections per connection identity that can be maintained concurrently. An empty value means no maximum size.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.prefsize**

    The string representation of an integer that represents the preferred number of connections per connection identity that should be maintained concurrently. An empty value means no preferred size.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.protocol**

    A list of space-separated protocol types of connections that may be pooled. Valid types are `plain` and `ssl`.

-   **ldap.pooling.com.sun.jndi.ldap.connect.pool.timeout**

    The string representation of an integer that represents the number of milliseconds that an idle connection may remain in the pool without being closed and removed from the pool. \# Empty value means no timeout, connection stays in pool forever. Bad connections are automatically detected and removed from the pool by the LDAP provider

-   **ldap.pooling.com.sun.jndi.ldap.connect.timeout**

    The string representation of an integer that represents the number of milliseconds to specify how long to wait for a pooled connection. An empty value means the application will wait indefinitely.


**Parent topic:**[Configuring LDAP](../concepts/auth-ldap-intro.md)

