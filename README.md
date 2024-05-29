<h2> Install Zabbix NMS with Telegram bot Alert </h2>

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

**ADD HOST**

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

**MAKE TELEGRAM BOT**

- Find user `@BotFather` or visit link https://t.me/BotFather
- Follow the instruction
- After the bot has been successfully created, take our Telegram user/group ID which will receive messages from Zabbix
    - If you're using personal user for alert, find `@myidbot` or visit https://t.me/myidbot
        
    - if you're using group for alert, open your telegram web and invite @ZabbixAlertBotAdmin_Bot that was created earlier, go to the group then look in the address bar, that's the group ID. Or you can add bot `@myidbot` to group and send the message with format `/getgroupid@myidbot`

- Testing Send Bot Messages to Our Telegram

    - Login Zabbix, then **Administration > Media Types**, then go to **Telegram**        
    - Customized according to what was set earlier
    - Apart from that, set it as default, then click add
    - Do testing to check whether the bot message was successfully entered or not, if successful then you can continue to the next step, if not then check again, there must be something wrong
        
- Setting User Zabbix Admin

    - To send a message to the Zabbix admin and then forward it to the bot that we created earlier to display it to us
    - Go to **Administration>Users**, there is the username **Admin** click then adjust
    - Then in the media section, click add then adjust it (Fill "Send to" with Your Telegram user ID or Group ID)
    - Click add

- Make Trigger

    - For showing bot messages to our telegram
    - Open **Configuration>Action>TriggerAction**
    - Adjust according to what was previously set
        
- Test

    - Shutdown the machine monitored by Zabbix or turn off the Zabbix agent to test the bot. If successful, there will be a notification like this, then the Zabbix telegram webhook was successful.
