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
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5843bea4-b1ec-4314-8060-972979966a13/Untitled.png)
- After the bot has been successfully created, take our Telegram user/group ID which will receive messages from Zabbix
    - If you're using personal user for alert, find `@myidbot` or visit https://t.me/myidbot
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ac78c12-86a8-4e4b-9f32-011efd148b32/Untitled.png)
        
    - if you're using group for alert, open your telegram web and invite @ZabbixAlertBotAdmin_Bot that was created earlier, go to the group then look in the address bar, that's the group ID
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/471bc2e1-268d-4f70-82c2-39acf4f496c2/Untitled.png)
        
        Or you can add bot `@myidbot` to group and send the message with format `/getgroupid@myidbot`
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ff6d21b-091d-4efc-a64a-913f4e50bb7d/Untitled.png)

- Testing Send Bot Messages to Our Telegram
    - Login Zabbix, then **Administration > Media Types**, then go to **Telegram**
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/55ce6606-2315-4897-bb05-e23cd0189f77/Untitled.png)
        
    - Customized like that
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/94578970-3355-43cf-ae5f-227d34a17ba0/Untitled.png)
        
    - Apart from that, set it as default, then click add
    - Do testing to check whether the bot message was successfully entered or not, if successful then you can continue to the next step, if not then check again, there must be something wrong
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdc4ff41-b64d-4ff8-869a-8ea125b36cb5/Untitled.png)
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b1a9ff73-6679-498c-b797-bab5214d696f/Untitled.png)
        
- Setting User Zabbix Admin
    - To send a message to the Zabbix admin and then forward it to the bot that we created earlier to display it to us
    - Go to **Administration>Users**, there is the username **Admin** click then adjust as shown in the picture
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/858501d5-66fa-496c-a813-11f43c78019f/Untitled.png)
        
    - Then in the media section, click add then adjust it as shown in the picture
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6c3c2436-ce41-488a-a142-167a21b3cdd4/Untitled.png)
        
    - Click add

- Make Trigger
    - For showing bot messages to our telegram
    - Open **Configuration>Action>TriggerAction**
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7f74d30-ffbc-486f-b75e-ab3efa94395e/Untitled.png)
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2501c6b5-1426-4243-b43a-f65079cbedb8/Untitled.png)
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ac0737b6-b404-469b-82d4-fda4cc94a60c/Untitled.png)
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/192d60b8-8d80-4677-9180-8c32b807df5c/Untitled.png)
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7bc19f83-6319-46ee-ad2b-7e9aac073524/Untitled.png)
        
- Test
    - Shutdown the machine monitored by Zabbix or turn off the Zabbix agent to test the bot. If successful, there will be a notification like this, then the Zabbix telegram webhook was successful.
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8dea94d-78e9-487d-953e-ed2c8fa53282/Untitled.png)