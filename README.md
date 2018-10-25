## EC2 MySQL installation

### 1. Install
  1. MySQL must be installed from the community repository.
  2. Download and add the repository, then update.

      ```
      $ sudo wget http://repo.mysql.com/mysql57-community-release-el7-9.noarch.rpm
      $ sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm 
      $ sudo yum update
      ```
  3. Despite the fact you downloaded a MySQL 5.7. package you can choose from a repository file which version to install: 5.5, 5.6, 5.7      or 8:
  
     ```
     $ sudo cd /etc/yum.repos.d/
     $ sudo vim mysql-community.repo
     ```
     Change the value in the row "enabled" for version you want to install. Don't forget to disable another version (default is 5.7):
     
     ```
     # Enable to use MySQL 5.6
     [mysql56-community]
     name=MySQL 5.6 Community Server
     baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/7/$basearch/
     enabled=1
     gpgcheck=1
     gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

     [mysql57-community]
     name=MySQL 5.7 Community Server
     baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
     enabled=0
     gpgcheck=1
     gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
     ```
  
  4. Install MySQL as usual and start the service. During installation, you will be asked if you want to accept the results from the          .rpm file’s GPG verification. If no error or mismatch occurs, enter y.

      ```
      $ sudo yum install mysql-server
      $ sudo systemctl start mysqld
      ```
      
      MySQL will bind to localhost (127.0.0.1) by default. Allowing unrestricted access to MySQL on a public IP not advised but you may       change the address it listens on by modifying the bind-address parameter in /etc/my.cnf. If you decide to bind MySQL to your             public IP, you should implement firewall rules that only allow connections from specific IP addresses.

      MySQL 5.7 and later: for temporary password for the user root before mysql_secure_installation go here: 

      ```
      $ cat /var/log/mysqld.log | grep 'temporary password'
      ```
  5. Perform security installations by this command:
  
      ```
      $ mysql_secure_installation
      ```
      Go through these steps and hit Y or N:
     ```
      a. Set a password for root accounts: Y.
      b. Remove root accounts that are accessible from outside the local host: N.
      c. Remove anonymous-user accounts: Y.
      d. Remove the test database: Y.
      e. Reload privileges tables: Y.
      ```
      
  
### 2. Configure
- mysql engine configuration
- System variables changing

  In order to change system variables you have two options:
  * Changing online without restarting the MySQL instance (if a variable is dynamic)
  
    Connect to the MySQL shell (or any MySQL client such Workbench, SQLYog, dbForge and etc.):
    ```
    mysql -h <hostname or IP> -u <username> -p
    mysql> SET GLOBAL <variable name> = <variable value>;
    ```
    
  * Changing offline in /etc/my.cnf with restarting the MySQL instance
  
    Open a configuration file in any editor (vim, nano and etc.):
    ```
    vim /etc/my.cnf
    ```
    Change/add a line with a variable and save the file. Remeber to add a new variable in the appropriate section (Replication, InnoDB) and save the copy of the file here in GitHub. 
    I suggest to backup the file each time you're changing it:
    ```
    cp /etc/my.cnf /etc/my.cnf.backup
    ```
    Restart MySQL instance according the tutorial "Clean shutdown" below.

### 3. Manage
- Clean shutdown
  
  1. Run in MySQL shell or any client such as Workbench, SQLYog and etc.:
      On a slave stop the I/O thread which takes updates from the master and wait for the SQL thread to process all:
      
        ```
        mysql> STOP SLAVE IO_THREAD;
        mysql> SHOW SLAVE STATUS\G
        ```

    2. Validate that "Seconds_Behind_Master" is 0 and stop SQL thread too:
    
        ```
        mysql> STOP SLAVE SQL_THREAD;
        mysql> SHOW SLAVE STATUS\G
        ```

    3. Take a position from the slave anyway for the case you want to continue a replication if it will fail:
       In the output of the SHOW SLAVE STATUS you can see a values in:
       
      
       Also another source of these values is MySQL error log: /var/log/mysqld.log. Search with tail the row with master position and          log file.
      
    4. Run these statements in order to prevent any data corruption and promise data consistency:
    
        ```
        mysql> SET GLOBAL innodb_fast_shutdown = 0;
        mysql> SET GLOBAL innodb_max_dirty_pages_pct = 0;
        ```

    5. Monitor dirty pages with this command in an OS shell and wait until a number get zero:
    
        ```
        $ sudo mysqladmin -u root -p ext -i10 | grep dirty
        ```
      
    6. Check if you have long running transactions:
    
        ```
        mysql> SHOW PROCESSLIST;
        ```
      
    7. Kill long running transaction if it'd OK to interrupt them:
    
        ```
        mysql> KILL [thread id];
        ```
      
        If you're rocognizing any applicative process , stop the application to write into a instance.
      
    8. Shutdown the service by the following way:
    
        ```
        $ sudo mysqladmin -u root -p shutdown
        ```
      
        Validate that the MySQL service isn't running:
        
        ```
        $ sudo systemctl status mysqld
        ```

    9. Do what you wanted to do (change my.cnf, service and etc.).
    10. Start MySQL instance and check replication status.
    
        ``` 
        $ sudo systemctl status mysqld
        $ mysql -u root -p -e "SHOW SLAVE STATUS\G"
        ```
- Percona Toolkit

  As a part of maintenance you can use a wide range of utilities installed on the instance.
  Please refer to the following documentation page with utilities list:
  https://www.percona.com/doc/percona-toolkit/LATEST/index.html.

### 4. Monitor

http://ec2-52-9-147-155.us-west-1.compute.amazonaws.com

- Create Percona Monitoring and Management instance (https://www.percona.com/doc/percona-monitoring-and-management/deploy/server/ami.html)

  1. Go to the AWS Marketplace page: https://aws.amazon.com/marketplace.
  2. Search for "Percona Monitoring and Management Server".
  3. Click on "Continue to Subscribe".
  4. Click on "Continue to Configuration".
  5. Choose appropriate configuration (EC2 instance, VPC, region, subnet).
  6. Click on "Launch".
  7. Allocate andaAssign an elastic IP adress for the new instance.
  8. Connecto to the PMM web site: http://<public DNS address>.
  9. Enter an Instance ID.
  10. Create a new administrator, for example admin, and create a new password.
  11. Check the PMM web site again (you need to see a Home page without monitoring instances).
  12. Create firewall rules for the EC2 MySQL instance and EC2 PMM instance: inbound rules for PMM server (80, 42000,42002 or all ports)  and outbound rules for the EC2 MySQL instance (42000,42002 or all ports). Update a source in each rule: and internal IP of a counter instance.
  13. Create and configure a PMM agent on EC2 instance with MySQL installed:
    
      a. Install the Percona repository.

        ```
        yum -y install http://www.percona.com/downloads/percona-release/redhat/0.1-4/percona-release-0.1-4.noarch.rpm
        ```

      b. Check an installed package.

        ```
        yum list | grep percona
        ```

      c. Install the PMM client and Percona Toolkit.

        ```
        yum -y install pmm-client percona-toolkit
        ```

      d. Connect the PMM client to the PMM server on EC2 (with username and password you created for the PMM server in the paragraph #10          above).

        ```
        pmm-admin config --server ec2-52-9-147-155.us-west-1.compute.amazonaws.com --server-user admin --server-password  "j4X&54Ny#g#6AsmE"
        ```
      e. Add monitoring services for the PMM client.
      
        ```
        pmm-admin add mysql --user root --password "j4X&54Ny#g#6AsmE"
        ```
        
      f. Check the network connectivity between the client and the server and running services on the client.
      
        ```
        pmm-admin check-network
        pmm-admin list
        ```
      g. Maintanance the PMM client service with following commands:
      
        ```
        pmm-admin stop --all
        pmm-admin restart --all
        pmm-admin start --all
        pmm-admin remove --all
        ```
        Please remember: if you want to reinstall the PMM server, first stop the PMM client services and remove in order to prevent them         to become orphaned services.
        For any additional maintenance check the documentation page: https://www.percona.com/doc/percona-monitoring-and-management/pmm-admin.html.


