#    **POC of Static Code Anaylysis in Java CI Checks**




**Table of Contents**






# Step-by-step installation on Ubuntu


## **Step 1. Update and Upgrade System Packages**

``` bash
sudo apt update
```
``` bash
sudo apt upgrade -y
```

## **Step 2. Java installation**

``` bash
sudo apt install -y openjdk-17-jdk
```


##  **Step 3. Verify the installed Java version**

``` bash
java -version
```
![image](https://github.com/user-attachments/assets/6ed17dd0-d0dd-42c0-a21e-407acb1cb2a9)
## **Step 4. Install and configure PostgreSQL**


#### 1. Adding the PostgreSQL repository.

``` bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" /etc/apt/sources.list.d/pgdg.list'

```
![image](https://github.com/user-attachments/assets/1f97e4ae-c98f-421b-8ccd-f5499142242f)
#### 2. Add the PostgreSQL signing key.

``` bash
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -

```

#### 3. Install PostgreSQL.

``` bash
sudo apt install postgresql postgresql-contrib -y

```
![image](https://github.com/user-attachments/assets/3abf0b88-1acf-4562-bcbc-ea33fa74dee8)


#### 4. Enable the database server to start automatically on reboot.

``` bash
sudo systemctl enable postgresql
```

#### 5. Start the database server.

``` bash
sudo systemctl start postgresql
```

#### 6. Check the status of the database server.

``` bash
sudo systemctl status postgresql
```
![image](https://github.com/user-attachments/assets/334afc41-a4e3-4fb9-a167-054cb3d17732)
#### 7. Switch to the Postgres user.
``` bash
sudo -i -u postgres
```

#### 8. Create a database user named ddsonar.
``` bash
createuser ddsonar
```


#### 9. Log in to PostgreSQL.

```bash
psql

```


 
#### 10. Set a strong password for the “sona” user. Use a password

``` bash
ALTER USER [Created_user_name] WITH ENCRYPTED password 'my_strong_password';

```
for example

``` bash
ALTER USER ddsonar WITH ENCRYPTED password 'nikita0919';
```

#### 11. Create a SonarQube database and set the owner to ddsonar.

``` bash
CREATE DATABASE [database_name] OWNER [Created_user_name];
```

for example
CREATE DATABASE ddsonarqube OWNER ddsonar;

##### 12. Grant all the privileges on the ddsonarqube database to the ddsonar user.
```
GRANT ALL PRIVILEGES ON DATABASE ddsonarqube to ddsonar;
```
![image](https://github.com/user-attachments/assets/95c503c1-c549-427f-b600-18398828cb76)

#### 13. To verify the creation of the database, use the following command
```
\l
```
![image](https://github.com/user-attachments/assets/cddffae4-66b0-45a1-940d-f837dfbb2830)
To verify the creation of the database user, use the following command:
```
\du
```
![image](https://github.com/user-attachments/assets/24861436-d547-4029-b764-9db830e576c2)

#### 14. To exit the PostgreSQL command-line interface, use the following command:
```
\q
```
#### 15.  To return to your non-root sudo user account, use the following command:
```
exit
```



## **Step 7.  Download and Install SonarQube**

Install the zip utility, which is needed to unzip the SonarQube files.
```
sudo apt install zip -y
```



## **Step 8. installing the latest version of SonarQube 10.4 Community Edition (free version)**

``` bash
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.0.0.68432.zip

```

## **Step 9. Move the unzipped files to the /opt/sonarqube directory**

``` bash
sudo unzip sonarqube-10.0.0.68432.zip
sudo mv sonarqube-10.0.0.68432 sonarqube
sudo mv sonarqube /opt/
```

## **Step 10. Add SonarQube Group and User**

Create a dedicated user and group for SonarQube, which can not run as the root user.

Note: You can give any name for the sonar user and group. I have here given the user and group name to be the same i.e ddsonar.

#### **1.Create a sonar group.**

```bash
sudo groupadd ddsonar
```
#### **2.Create a sonar user and set /opt/sonarqube as the home directory.**

``` bash
sudo useradd -d /opt/sonarqube -g sona sona
```

#### **3.Grant the sonar user access to the /opt/sonarqube directory.**
``` bash
sudo chown ddsonar:ddsonar /opt/sonarqube -R
```



## **Step 11. Configure SonarQube**

#### 1. Edit the SonarQube configuration file.

``` bash
sudo nano /opt/sonarqube/conf/sonar.properties
```


#### 2. Uncomment the following lines in the SonarQube configuration file**

``` bash
sonar.jdbc.username=ddsonar
sonar.jdbc.password=nikita0919
```

``` bash
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sqube
```
![image](https://github.com/user-attachments/assets/c5ba81d8-99c6-4a68-95c7-ff70df9c98e3)


## **Step 12. To edit the SonarQube script file**

``` bash
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh
```


## **Step 13. Select the branch you want to merge**

``` bash
RUN_AS_USER=ddsonar

```


## **Step 14. Setup Systemd service**

#### 1. **Create a new service file using a text editor**

``` bash
sudo nano /etc/systemd/system/sonar.service
```


#### 2. **Paste the following lines to the file.**

``` bash
[Unit]
Description=SonarQube service
After=syslog.target network.target
[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=ddsonar
Group=ddsonar
Restart=always
LimitNOFILE=65536
LimitNPROC=4096
[Install]
WantedBy=multi-user.target

```

**Note:** Here in the above script, make sure to change the User and Group section with the value that you have created. For me its:

User=**ddsonar**

Group=**ddsonar**

![image](https://github.com/user-attachments/assets/2c08a986-33c7-45e9-8c72-eee181c8cf4d)


#### 3. **Enable the SonarQube service to start at boot**

``` bash
sudo systemctl enable sonar
```


#### 4. **start the SonarQube service**

``` bash
sudo systemctl start sonar
```
#### 5. **Check the service status**
``` bash
sudo systemctl status sonar
```
![image](https://github.com/user-attachments/assets/b7849875-8656-456d-a13f-fdbe201129c5)



## **Step 13. Modify Kernel System Limits**

#### 1. **Edit the sysctl configuration file.**
``` bash
sudo nano /etc/sysctl.conf
```


#### 2. **Add the following lines to the sysctl configuration file (/etc/sysctl.conf)**

``` bash
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```



#### 3.**To apply the changes, reboot the system**
``` bash
sudo reboot
```

___

## **Step 15.Access SonarQube Web Interface**


#### 1. Access SonarQube in a web browser at your server’s IP address on port 9000.

``` bash
http://100.26.240.39:9000
```
**Note:** the default username and password are admin and admin respectively.

#### 2. Change the Old password with a New one.


Once logged in, SonarQube will prompt you to change your password. Enter the current password “admin” and then enter your new password twice as prompted.


## **Step 24. Log in to SonarQube using the username “admin” and password “admin”**
![image](https://github.com/user-attachments/assets/861bb0fb-5295-4fc2-9670-d6c1395a2fcd)

## **Step 25. Go to SonarQube and select the project**
![image](https://github.com/user-attachments/assets/732af797-d1a9-4ff7-b4ad-78dad343e861)


## **Step 26. Create a Local Project: Set up a new or existing project on your machine**


## **Step 27. Configure the Project: Prepare your project for analysis by configuring the necessary files**
![image](https://github.com/user-attachments/assets/038f90ae-92d1-44ff-9ee6-afa780561bbc)

## **Step 28. Analysis your project which you want**
![image](https://github.com/user-attachments/assets/71917427-1f21-47f5-baf4-5c67fe35dfc1)


## **Step 29. Generate Token: Create an authentication token in SonarQube**



## **Step 30. Copy the Token: Copy the generated SonarQube token to use for authentication when running the SonarScanner**

![image](https://github.com/user-attachments/assets/c90f31f1-7071-410f-94f1-cd384e6db8d8)





## **Step 32.Run Analyze on your project**
![image](https://github.com/user-attachments/assets/324e265d-aff7-494f-8033-82e1d646931b)




## **Step 35. Result**
![image](https://github.com/user-attachments/assets/31bfee29-a71b-42d7-bd2c-8ced47bf9992)


 ## **Steps 36. Report** 
| Link         | Description         |
|--------------|------------------------|
| [Artifact](https://github.com/avengers-p11/Documentation/blob/main/Application%20CI%20Design/Java%20CI%20Checks/Bugs%20Analysis/salary-0.1.0-RELEASE.jar.original)           |File |
| [SCA](https://github.com/avengers-p11/Documentation/blob/main/Application%20CI%20Design/Java%20CI%20Checks/Unit%20Testing%20/report)| Report |
#



# Conclusion

Static code analysis tools in Java help identify issues early, improving code quality, security, and maintainability, which reduces long-term costs. However, they should be paired with dynamic analysis and other testing methods for comprehensive code quality.

#  Contact Information





# References

| **Link** | **Description** |
|------------------------------------------------------|------------------|
| https://www.bairesdev.com/blog/java-static-code-analysis-tools/| Static Code Anlyasis |
| https://www.baeldung.com/java-static-code-analysis-tutorial| Static Code Analysis tool |
