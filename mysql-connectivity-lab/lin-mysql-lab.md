# Troubleshoot Network Connectivity on Linux

## Introduction
There are a variety of tools available to test connections between servers, including `nc`, `ping`, `dig`, and others.
In this lab, we'll use these tools to find what is preventing the connection between our MySQL (MariaDB) server and client.

> **Note:** This course is not approved or sponsored by Red Hat.

---

## Lab Setup
### Credentials

| Server | Username       | Password      | Private IP     | Public IP      |
|--------|---------------|---------------|----------------|----------------|
| App    | app_user      | app_pass123   | 10.0.1.101     | <APP_PUBLIC_IP>|
| DB     | db_user       | db_pass123    | 10.0.1.105     | <DB_PUBLIC_IP> |

> **Note:** Replace `<APP_PUBLIC_IP>` and `<DB_PUBLIC_IP>` with your actual public IP addresses.

---

## Step 1: Connect to Servers
On both **App** and **DB** servers:
```bash
ssh <username>@<PUBLIC_IP_ADDRESS>
sudo -i


**Step 2: Reproduce the Issue**

From the App server, try to connect to the DB server:

mysql -u db_user -h 10.0.1.105 -pdb_pass123


You should see an error if connectivity is blocked.

**Step 3: Verify Network Connectivity
**
From the App server:

ping 10.0.1.105


Press CTRL + C to stop ping.

If ping works, the servers can reach each other at the network level.

**Step 4: Check MySQL Port Connectivity
**
On DB server, stop MariaDB service temporarily:

systemctl stop mariadb


Open port 3306 using netcat:

nc -l 3306


From App server, test connectivity:

nc 10.0.1.105 3306


If it connects, port 3306 is reachable.

Press CTRL + C on DB server to stop netcat.

Restart MariaDB:

systemctl enable --now mariadb

**Step 5: Check Firewall
**
On DB server, list firewall rules:

sudo firewall-cmd --list-all


If mysql or port 3306 is not allowed, add the rule:

sudo firewall-cmd --permanent --zone=public --add-service=mysql
sudo firewall-cmd --reload

**Step 6: Test Connection Again
**
From App server:

mysql -u db_user -h 10.0.1.105 -pdb_pass123


If successful, you are connected to MariaDB.

**Step 7: Verify MariaDB
**
Inside MariaDB monitor:

SHOW DATABASES;
CREATE DATABASE purchases;
SHOW DATABASES;


From DB server, you can also check:

mysql -u db_user -h 10.0.1.105 -pdb_pass123
SHOW DATABASES;

**Conclusion
**
You have successfully diagnosed and fixed the network connectivity issue between App and DB servers.

Firewall and MySQL service configuration were the main causes of the issue.

**Tools Used
**
ping → Check network connectivity

nc (netcat) → Check port availability

firewall-cmd → Manage firewall rules

mysql → Connect to MariaDB/MySQL
