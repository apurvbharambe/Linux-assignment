# 1.Configure smtp in localhost.
 
#### Step 1:  Update ubuntu -  
```bash
sudo apt-get update && sudo apt-get upgrade
```
#### Step 2: Install postfix - 
``` bash
sudo apt-get install postfix
```
###### The first part of the Postfix installation is a text-based user interface. Use  keyboard to select "Internet Site" for the type of mail configuration.

#### Step 3:  Set app password in gmail

#### Step 4: Create file /etc/postfix/sasl/sasl_passwd - :  
``` bash
sudo touch /etc/postfix/sasl/sasl_passwd
```
#### Step 5: Add Gmail address and app password to file:
``` bash 
sudo nano /etc/postfix/sasl/sasl_passwd
```
##### [smtp.gmail.com]:587 my@gmail.com:password 
###### ( smtp.gmail.com is the address of the Gmail SMTP server and 587 is the SMTP port.)

#### Step 6 : Create hash database file of password
```bash
sudo postmap /etc/postfix/sasl/sasl_passwd
```
##### (now we will have a database file at /etc/postfix/sasl/sasl_passwd.db.)

#### Step 7: In order to protect the plain-text password, change the owner and permission for the SASL files as follows:
``` bash
sudo chown root:root /etc/postfix/sasl/sasl_passwd /etc/postfix/sasl/sasl_passwd.db
sudo chmod 0600 /etc/postfix/sasl/sasl_passwd /etc/postfix/sasl/sasl_passwd.db
```

#### Step 8: In order to tell Postfix to use Gmail servers to send mail, set the relay value in the /etc/postfix/main.cf file.
 
Set relay value to : [smtp.gmail.com]:587

##### Also, add the following to the end of /etc/postfix/main.cf to enable SASL authentication for Postfix.
####  Enable SASL (Simple Authentication and Security Layer) authentication
```bash
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

#### Step 9: restart Postfix to apply our configuration changes.
``` bash
sudo systemctl restart postfix
```
#### Step 10: Send a mail: 
``` bash
send mail user@gmail.com
To: user@gmail.com
Subject: Test SMTP
HELLO
.
```

# 2. Create a user in your localhost, which should not be able to execute the sudo command.

#### Step 1:  
``` bash
sudo adduser user1
```
#### Step 2:  
``` bash 
sudo deluser user1 sudo   
```
##### (The user `user1' is not a member of group `sudo')

# 3. Configure your system in such a way that when a user type and executes a describe  command from anywhere of the system it must list all the files and folders of the user's current directory.

Ex:- $ describe
$  content1 content2
Content3 content 4

#### Step 1: 
``` bash
sudo nano /.bashrc
```
##### (add alias describe='ls -l' at end in .bashrc)

#### Step 2: 
``` bash
source ~/.bashrc
```
#### Step 3: 
``` dash
describe
```
(The .bashrc file is a script that Bash (Bourne Again SHell) reads each time it starts a new interactive shell session. It's typically located in the user's home directory (~/), and it contains commands and settings that customize the shell environment for the user.)






# 4. Users can put a compressed file at any path of the linux file system. The name of the file will be research and the extension will be of compression type, example for gzip type extension will be .gz. You have to find the file and check the compression type and uncompress it.

#### Method1 (any one code out of below 3): 
``` bash
gunzip -k $(find /home -type f -name "*.gz" -print -quit 2>/dev/null)
gunzip -k $(find / -type f -name "research.gz" -print -quit 2>/dev/null)
find / -name "research.gz" -exec gunzip {} \;



```
# 5. Configure your system in such a way that any user of your system creates a file then there should not be permission to do any activity in that file.
##### Note:- Don’t use the chmod command.

#### Step1:  
``` bash
nano ~/.bashrc
```
#### Add umask 077 at end of  ~/.bashrc

#### Step2: Reload shell configuration file to apply changes - 
``` bash
source ~/.bashrc
```
(The first digit (0) represents the permissions for the owner of the file.The second digit (7) represents the permissions for the group associated with the file.The third digit (7) represents the permissions for all other users.umask of "077" effectively removes all permissions for group and other users, while allowing the owner full read, write, and execute permissions on newly created files.)

# 6. Create a service with the name showtime , after starting the service, every minute it should print the current time in a file in the user home directory.

###### Ex:-
###### sudo service showtime start   -> It should start writing in file.
###### sudo service showtime stop   -> It should stop writing in file.
###### sudo service showtime status -> It should show status.

#### Step 1:  Create file showtime.txt and create shell script showtime.sh
``` bash
touch showtime.txt
```
###### (created showtime.txt file and check rwx permissions )

##### Create shell script showtime.sh
``` bash 
nano ~/showtime.sh
```
``` bash
#!/bin/bash

while true; do
    date "+%Y-%m-%d %H:%M:%S" >> /home/hii/showtime.txt
    sleep 60  # execute again after 60 seconds (1 minute)
done
```

#### Step 2: Give execution  permission
``` bash
 chmod a+x ~/showtime.sh
```
#### Step 3: Create systemd service unit file named showtime.service
``` bash
sudo nano /etc/systemd/system/showtime.service
```

#### Add following in showtime.service file:
``` bash
[Unit]
Description=Print current time every minute

[Service]
Type=simple
ExecStart=/bin/bash /home/hii<username>/showtime.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

#### Step 4: Reload the systemd daemon and start the "showtime" service
``` bash
sudo systemctl daemon-reload
sudo systemctl start showtime
```

#### Step 5: Enable the "showtime" service to start service automatically at time of boot
``` bash
sudo systemctl enable showtime
```
``` bash
sudo service showtime start: Start the service.
sudo service showtime stop: Stop the service.
sudo service showtime status: Check the status of the service.
```
##### To check running service (It displays the logs related to the execution and status of the "showtime" service.):
(to view logs for specific unit here service is unit)
``` bash
journalctl -u showtime
```
(-u, it allows you to specify a systemd unit name (such as a service name) to filter logs related to that specific unit)

##### another way:
``` bash
cat  showtime.txt
```











