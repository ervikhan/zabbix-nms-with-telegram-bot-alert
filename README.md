***Install Zabbix NMS with Telegram bot Alert***

**BASE OS Ubuntu 20.04**
- Zabbix Version : 6.0 LTS
- Zabbix Component : Server, Agent, Frontend
- Database : MYSQL
- Web Server : Apache

Sources File
https://www.zabbix.com/download?zabbix=5.0&os_distribution=ubuntu&os_versi

**INSTALLATION**

- Download package installation
    
    ```bash
    wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu20.04_all.deb
    ```
    
- Add package
    
    ```bash
    dpkg -i zabbix-release_6.0-4+ubuntu20.04_all.deb
    ```
    
- Update repos
    
    ```bash
    apt update
    ```
    
- Install all services needed
    
    ```bash
    apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent mysql-server
    ```
    
- MySQL login
    
    ```bash
    mysql -u root -p
    ```
    
- Database and user init
    
    ```sql
    mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
    mysql> create user zabbix@% identified by 'zabbix';
    mysql> grant all privileges on zabbix.* to zabbix@%;
    ```
    
- Import Schema and data table
    - Before import do this
        
        ```sql
        mysql> set global log_bin_trust_function_creators = 1;
        ```
        
    - Then import
        
        ```bash
        zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
        ```
        
    - Then set default log_bin_trust_function_creators
        
        ```bash
        mysql> set global log_bin_trust_function_creators = 0;
        ```

    - File configuration
        
        ```bash
        nano /etc/zabbix/zabbix_server.conf
        ```
        
        ***addjust db host, db name, db user, and db password***
        
    - Set timezone apache
        
        ```jsx
        nano /etc/zabbix/apache.conf
        ```
        
    - Restart and enable zabbix and apache
        
        ```bash
        service zabbix-server restart
        ```
        
        ```bash
        
        ***service zabbix-server enable***
        ```
        
        ```bash
        service apache2 restart
        ```
        
    - Then open http://ip-zabbix-server/zabbix

    - Default username “***Admin”*** password “***zabbix”***

**Add Host**

- Download Zabbix Agent
    https://www.zabbix.com/download?zabbix=5.0&os_distribution=ubuntu&os_versi
- Example for ubuntu host
    - Download package
        
        ```bash
        wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu20.04_all.deb
        ```
        
    - Add package
        
        ```bash
        dpkg -i zabbix-release_6.0-4+ubuntu20.04_all.deb
        ```
        
    - Update repos
        
        ```bash
        apt update
        ```
        
    - Install service
        
        ```bash
        apt install zabbix-agent
        ```

    - Konfigurasi file
        
        ```bash
        nano /etc/zabbix/zabbix_agentd.conf
        ```
        
        *** Change “Server” dan “ServerActive” become your zabbix server IP, hostname must same with hostname thet will add to zabbix-server***
        
    - Restart and enable zabbix agent
        
        ```bash
        service zabbix-agent restart
        ```
        
        ```bash
        systemctl enable zabbix-agent
        ``` 
- Add host to zabbix nms frontend
    - Login zabbix
    - Configuration > host
    - Click Create Host (top right corner)
    - Type : agent and fill with Client (In Ubuntu in our case)