# Apache Load Balancer Configuration with RHEL8 Web Servers and MySQL DB

## Overview
This project involves setting up and configuring a load balancer using Apache on an Ubuntu 20.04 EC2 instance. The load balancer distributes traffic across two RHEL8 Web Servers. Additionally, we'll configure local DNS resolution for easier management. This guide will walk you through the entire process.

### Architecture
- **Two RHEL8 Web Servers**
- **One MySQL Database Server (Ubuntu 20.04)**
- **One RHEL8 NFS Server**
- **One Ubuntu 20.04 EC2 instance running Apache as the Load Balancer**

---

## Prerequisites
1. Ensure you have already created the following servers:
   - **Two RHEL8 Web Servers**
   - **One MySQL DB Server (Ubuntu 20.04)**
   - **One RHEL8 NFS Server**
   
2. Apache2 must be installed on both RHEL8 Web Servers, and PHP configured to serve web content.

3. Update Security Groups for all instances to allow necessary inbound traffic:
   - **TCP Port 80** for HTTP traffic.
   - **TCP Port 22** for SSH access.

---

## Step 1: Create Ubuntu Server for Load Balancer

1. Launch an **Ubuntu Server 20.04 EC2 instance** and name it `Project-8-apache-lb`.
2. Ensure **TCP port 80** is open in the instance’s Security Group by adding an inbound rule.

### Screenshot: [Add Security Group Rule for TCP Port 80](#) <!-- Attach screenshot -->

---

## Step 2: Install and Configure Apache Load Balancer

1. **Update the package list** and install Apache2 and required modules:
    ```bash
    sudo apt update
    sudo apt install apache2 -y
    sudo apt-get install libxml2-dev
    ```

2. **Enable required Apache modules**:
    ```bash
    sudo a2enmod rewrite
    sudo a2enmod proxy
    sudo a2enmod proxy_balancer
    sudo a2enmod proxy_http
    sudo a2enmod headers
    sudo a2enmod lbmethod_bytraffic
    ```

3. **Restart the Apache2 service**:
    ```bash
    sudo systemctl restart apache2
    ```

4. **Check Apache service status** to ensure it is running:
    ```bash
    sudo systemctl status apache2
    ```

### Screenshot: [Apache Service Status](#) <!-- Attach screenshot -->

---

## Step 3: Configure Load Balancing

1. Open the Apache virtual host configuration file:
    ```bash
    sudo vi /etc/apache2/sites-available/000-default.conf
    ```

2. Add the following configuration under the `<VirtualHost *:80>` block:
    ```apache
    <Proxy "balancer://mycluster">
        BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
        BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
        ProxySet lbmethod=bytraffic
    </Proxy>

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/
    ```

   Replace `<WebServer1-Private-IP-Address>` and `<WebServer2-Private-IP-Address>` with your web servers’ private IPs.

3. Restart Apache to apply the changes:
    ```bash
    sudo systemctl restart apache2
    ```

### Screenshot: [Apache Configuration with Proxy](#) <!-- Attach screenshot -->

---

## Step 4: Verify Load Balancer Configuration

1. Access the load balancer's **public IP address** or **public DNS name**:
   ```
   http://<Load-Balancer-Public-IP-Address>/index.php
   ```

2. Open two SSH/Putty consoles for both web servers and run the following command to view the HTTP GET requests:
    ```bash
    sudo tail -f /var/log/httpd/access_log
    ```

3. Refresh the browser several times to see the load being distributed between the two servers.

### Screenshot: [Load Balancer Working - Web Server Logs](#) <!-- Attach screenshot -->

---

## Step 5: Configure Local DNS Names Resolution

1. Open the **/etc/hosts** file on the load balancer server:
    ```bash
    sudo vi /etc/hosts
    ```

2. Add the following entries to resolve your web servers by name:
    ```bash
    <WebServer1-Private-IP-Address> Web1
    <WebServer2-Private-IP-Address> Web2
    ```

3. Modify the load balancer configuration to use the new names:
    ```apache
    BalancerMember http://Web1:80 loadfactor=5 timeout=1
    BalancerMember http://Web2:80 loadfactor=5 timeout=1
    ```

4. Test the name resolution by running the following commands on the load balancer:
    ```bash
    curl http://Web1
    curl http://Web2
    ```

### Screenshot: [Updated /etc/hosts File](#) <!-- Attach screenshot -->

---

## Troubleshooting

- **Check Apache logs**: 
    ```bash
    sudo tail -f /var/log/apache2/error.log
    ```

- **Ensure Web Servers are reachable** by pinging from the Load Balancer:
    ```bash
    ping <WebServer1-Private-IP-Address>
    ping <WebServer2-Private-IP-Address>
    ```

### Screenshot: [Troubleshooting Apache](#) <!-- Attach screenshot -->

---

## Conclusion

By completing this guide, you have successfully set up an Apache Load Balancer that distributes traffic across two RHEL8 web servers, with additional configurations for local DNS name resolution. Your users will experience a smooth browsing experience, and traffic is evenly distributed across servers.

For further improvements, you can explore different load balancing methods such as `bybusyness`, `byrequests`, and `heartbeat`.

---
