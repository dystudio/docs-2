# Connecting to a Database Locally<a name="EN-US_TOPIC_0241704254"></a>

**gsql**  is an openGauss-provided database connection tool running in the CLI.  **gsql**  provides basic and advanced functions of databases to facilitate user operations. This section describes how to use  **gsql**  to connect to a database. For details about its usage, see the related chapter in the  _Tool Reference_.

## Precautions<a name="en-us_topic_0241234230_en-us_topic_0085434651_en-us_topic_0059781996_en-us_topic_0062050379_s8dfe50d001084ac9ad79a79a8f471e8a"></a>

By default, if a client is idle state after connecting to a database, the client automatically disconnects from the database in the duration specified by  **[session\_timeout](en-us_topic_0242371486.md#en-us_topic_0237124696_en-us_topic_0059778664_see4820fb6c024e0aa4c56882aeae204a)**. To disable the timeout setting, set  **[session\_timeout](en-us_topic_0242371486.md#en-us_topic_0237124696_en-us_topic_0059778664_see4820fb6c024e0aa4c56882aeae204a)**  to  **0**.

## Prerequisites<a name="en-us_topic_0241234230_en-us_topic_0085434651_en-us_topic_0059781996_en-us_topic_0062050379_section2863008216400"></a>

Connection information has been confirmed. For details, see  [Confirming Connection Information](en-us_topic_0242370176.md).

## Procedure<a name="en-us_topic_0241234230_en-us_topic_0085434651_en-us_topic_0059781996_en-us_topic_0062050379_sedb32189b5a4410a9a8ac8586f1766a0"></a>

1.  Log in as the OS user  **omm**  to the primary node of the database.
2.  Connect to a database.

    After the database is installed, a database named  **postgres**  is generated by default. When connecting to a database for the first time, you can connect to this database.

    Run the following command to connect to the  **postgres**  database:

    ```
    gsql -d postgres -p 8000
    ```

    **postgres**  is the name of the database to be connected, and  **8000**  is the port number of the database primary node. Replace the values as required.

    If information similar to the following is displayed, the connection succeeds:

    ```
    gsql ((openGauss 1.0.0 build 290d125f) compiled at 2020-05-08 02:59:43 commit 2143 last mr 131
    Non-SSL connection (SSL connection is recommended when requiring high-security)
    Type "help" for help.
    
    postgres=# 
    ```

    User  **omm**  is the administrator, and  **DBNAME=\#**  is displayed. If you log in to and connect to the database as a common user,  **DBNAME=\>**  is displayed.

    **Non-SSL connection**  indicates that the database is not connected in SSL mode. If high security is required,  [connect to the database in SSL mode](en-us_topic_0246507951.md).

3.  Change the password after your first login. The initial password is set manually during openGauss database installation. For details, see the  _Installation Guide_. You need to change the initial password. Suppose you want to change the initial password to  **Mypwd123**. You can use the following command:

    ```
    postgres=# ALTER ROLE omm IDENTIFIED BY 'Mypwd123' REPLACE 'XuanYuan@2012';
    ```

4.  Exit the database.

    ```
    postgres=# \q
    ```


