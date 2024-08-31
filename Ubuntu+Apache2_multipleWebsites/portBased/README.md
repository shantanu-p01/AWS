# Multi-Website Hosting on a Single Apache Server Using Different Ports

This project demonstrates how to host multiple websites on a single Apache server by using different ports on an AWS EC2 instance. By following these steps, you will be able to set up a multi-website environment where each website is accessible via the same IP address but on different ports.

## Prerequisites

- **AWS EC2 Instance**: An Ubuntu-based EC2 instance running Apache.
- **SSH Key**: An SSH key to securely connect to the server.
- **Basic Knowledge of Linux and Apache Configuration**.
- **Security Group Configuration**: Ensure that your EC2 instance’s security group allows inbound traffic on the custom ports (8081, 8082, 8083). You need to add inbound rules for these ports in your AWS security group settings.

## Step-by-Step Guide

### 1. SSH into the Server

Connect to your EC2 instance using SSH:

```bash
ssh -i "C:\Users\GawD\Downloads\WebServer.pem" ubuntu@13.201.100.252
```

![SSH Connection Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/SSH-connection-screenshot.png)

### 2. Update and Upgrade the Server

Ensure the server is up-to-date with the latest packages:

```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Install Apache Web Server

Install Apache2 on the server to handle HTTP requests:

```bash
sudo apt install apache2 -y
```

### 4. Update the Apache Configuration to Listen on Additional Ports

By default, Apache listens on port 80. To host multiple websites, configure Apache to listen on additional ports like 8081, 8082, etc.

Edit the Apache ports configuration file:

```bash
sudo nano /etc/apache2/ports.conf
```

Add the following lines to the file:

```apache
Listen 8081
Listen 8082
Listen 8083
```

![Apache Ports Configuration Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/ports-conf-screenshot.png)

### 5. Create Directories for Each Website

Create a directory structure for each website in Apache's root directory:

```bash
sudo mkdir -p /var/www/web1 && sudo mkdir -p /var/www/web2 && sudo mkdir -p /var/www/web3
```

### 6. Copy Website Files from Local Machine to EC2 Instance

Copy the required folder for each website from your local machine to their respective directories on the EC2 instance using `scp`:

```bash
scp -i "C:\Users\GawD\Downloads\WebServer.pem" -r web1 web2 web3 ubuntu@13.201.100.252:~
```

![File Transfer Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/file-transfer-screenshot.png)

Copy the `index.html` files for each website to their respective directories:

```bash
sudo cp web1/index.html /var/www/web1/ && sudo cp web2/index.html /var/www/web2/ && sudo cp web3/index.html /var/www/web3/
```

### 7. Set Ownership and Permissions

Ensure that Apache has the correct permissions to serve the files:

```bash
sudo chown -R $USER:$USER /var/www/web1 /var/www/web2 /var/www/web3
sudo chmod -R 755 /var/www
```

### 8. Create Custom Configuration Files for Each Website

Create a custom Apache configuration file for each website to listen on different ports.

```bash
sudo nano /etc/apache2/sites-available/web1.conf
```

**Sample Configuration for `web1` on Port 8081:**

```apache
<VirtualHost *:8081>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/web1
    ErrorLog ${APACHE_LOG_DIR}/web1-error.log
    CustomLog ${APACHE_LOG_DIR}/web1-access.log combined
</VirtualHost>
```

![Web1 Configuration Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/web1-conf-screenshot.png)

Repeat for `web2` and `web3` on different ports (e.g., 8082 and 8083):

```bash
sudo nano /etc/apache2/sites-available/web2.conf
```

**Sample Configuration for `web2` on Port 8082:**

```apache
<VirtualHost *:8082>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/web2
    ErrorLog ${APACHE_LOG_DIR}/web2-error.log
    CustomLog ${APACHE_LOG_DIR}/web2-access.log combined
</VirtualHost>
```

![Web2 Configuration Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/web2-conf-screenshot.png)

```bash
sudo nano /etc/apache2/sites-available/web3.conf
```

**Sample Configuration for `web3` on Port 8083:**

```apache
<VirtualHost *:8083>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/web3
    ErrorLog ${APACHE_LOG_DIR}/web3-error.log
    CustomLog ${APACHE_LOG_DIR}/web3-access.log combined
</VirtualHost>
```

![Web3 Configuration Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/web3-conf-screenshot.png)

### 9. Enable the Configuration Files

Enable each website's configuration file in Apache:

```bash
sudo a2ensite web1.conf web2.conf web3.conf
```

![Enable Sites Screenshot](/Ubuntu+Apache2_multipleWebsites/portBased/img/enable-sites-screenshot.png)

### 10. Restart Apache Web Server

Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

### 11. Test the Configuration

Run the following command to check if Apache has successfully loaded the virtual hosts:

```bash
sudo apache2ctl -S
```

You should see the virtual hosts listed for ports 8081, 8082, and 8083.

### 12. Access the Websites

Open your browser and navigate to the following URLs:

- `http://13.201.100.252:8081` for `web1`
![Web1 Loaded in Browser](/Ubuntu+Apache2_multipleWebsites/portBased/img/web1-loaded-screenshot.png)

- `http://13.201.100.252:8082` for `web2`
![Web2 Loaded in Browser](/Ubuntu+Apache2_multipleWebsites/portBased/img/web2-loaded-screenshot.png)

- `http://13.201.100.252:8083` for `web3`
![Web3 Loaded in Browser](/Ubuntu+Apache2_multipleWebsites/portBased/img/web3-loaded-screenshot.png)

Replace `13.201.100.252` with your EC2 instance's public IP address. You should see the `index.html` content for each respective website.

### 13. Troubleshooting

- **Firewall Issues**: Ensure that your EC2 instance’s security group allows inbound traffic on the custom ports (8081, 8082, 8083).
- **Apache Errors**: Check Apache logs for any errors or misconfigurations:

  ```bash
  sudo tail -f /var/log/apache2/error.log
  ```

## Conclusion

By following these steps, you've successfully configured multiple websites on a single Apache server using different ports. This method is useful when you want to host multiple sites on the same server without needing separate domain names.