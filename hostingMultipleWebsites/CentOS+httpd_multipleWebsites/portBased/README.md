# Multi-Website Hosting on a Single HTTPD Apache Server Using Different Ports

This project demonstrates how to host multiple websites on a single HTTPD Apache server by using different ports on an AWS EC2 instance. By following these steps, you will be able to set up a multi-website environment where each website is accessible via the same IP address but on different ports.

## Prerequisites

- **AWS EC2 Instance**: A CentOS-based EC2 instance running HTTPD Apache.
- **SSH Key**: An SSH key to securely connect to the server.
- **Basic Knowledge of Linux and HTTPD Apache Configuration**.
- **Security Group Configuration**: Ensure that your EC2 instance’s security group allows inbound traffic on the custom ports (81, 82, 83). You need to add inbound rules for these ports in your AWS security group settings.
  - Example security group rule:
    - Custom TCP Rule: Port Range `81-83`, Source: `0.0.0.0/0`
    - SSH: Port Range `22`, Source: Your IP or `0.0.0.0/0` for open access (not recommended for production).

## Step-by-Step Guide

### 1. SSH into the Server

Connect to your EC2 instance using SSH:

```bash
ssh -i "C:\Users\GawD\Downloads\WebServer.pem" ec2-user@15.206.84.57
```

![SSH Connection Screenshot](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/SSH-connection-screenshot.png)

### 2. Update and Upgrade the Server

Ensure the server is up-to-date with the latest packages:

```bash
sudo yum update -y
```

### 3. Install HTTPD Apache Web Server

Install HTTPD Apache on the server to handle HTTP requests:

```bash
sudo yum install httpd -y
```

### 4. Update the HTTPD Apache Configuration to Listen on Additional Ports

By default, HTTPD Apache listens on port 80. To host multiple websites, configure HTTPD Apache to listen on additional ports like 81, 82, etc.

Edit the HTTPD Apache ports configuration file:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Add the following lines to the file:

```apache
Listen 81
Listen 82
Listen 83
```

![HTTPD Apache Ports Configuration Screenshot](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/ports-conf-screenshot.png)

### 5. Create Directories for Each Website

Create a directory structure for each website in HTTPD Apache's root directory:

```bash
sudo mkdir -p /var/www/web1 && sudo mkdir -p /var/www/web2 && sudo mkdir -p /var/www/web3
```

### 6. Copy Website Files from Local Machine to EC2 Instance

Copy the required folder for each website from your local machine to their respective directories on the EC2 instance using `scp`:

```bash
scp -i "C:\Users\GawD\Downloads\WebServer.pem" -r web1 web2 web3 ec2-user@15.206.84.57:~
```

![File Transfer Screenshot](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/file-transfer-screenshot.png)

Copy the `index.html` files for each website to their respective directories:

```bash
sudo cp web1/index.html /var/www/web1/ && sudo cp web2/index.html /var/www/web2/ && sudo cp web3/index.html /var/www/web3/
```

### 7. Set Ownership and Permissions

Ensure that HTTPD Apache has the correct permissions to serve the files:

```bash
sudo chown -R $USER:$USER /var/www/web1 /var/www/web2 /var/www/web3
sudo chmod -R 755 /var/www
```

### 8. Create Custom Configuration Files for Each Website

Create a custom HTTPD Apache configuration file for each website to listen on different ports.

**Sample Configuration for `web1` on Port 81:**

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

```apache
<VirtualHost *:81>
    DocumentRoot "/var/www/web1"
</VirtualHost>
```

Repeat for `web2` and `web3` on different ports (e.g., 82 and 83):

**Sample Configuration for `web2` on Port 82:**

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

```apache
<VirtualHost *:82>
    DocumentRoot "/var/www/web2"
</VirtualHost>
```

**Sample Configuration for `web3` on Port 83:**

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

```apache
<VirtualHost *:83>
    DocumentRoot "/var/www/web3"
</VirtualHost>
```

![Web1 Configuration Screenshot](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/httpd-conf-screenshot.png)

### 9. Enable the Configuration Files

HTTPD Apache does not require enabling individual sites as in Apache2 on Ubuntu. Configurations are automatically read from the `/etc/httpd/conf/` directory.

### 10. Restart HTTPD Apache Web Server

Restart HTTPD Apache to apply the changes:

```bash
sudo systemctl restart httpd
```

### 11. Troubleshoot HTTPD Apache Start Failure Due to SELinux

If you encounter an error like the following when trying to start HTTPD Apache:

```
Job for httpd.service failed because the control process exited with error code.
See "systemctl status httpd.service" and "journalctl -xeu httpd.service" for details.
```

You might need to adjust the SELinux mode:

1. Check the current SELinux mode:

   ```bash
   getenforce
   ```

2. If it returns `Enforcing`, temporarily set SELinux to `Permissive` mode:

   ```bash
   sudo setenforce 0
   ```

   This command will make SELinux permissive until the next reboot. If this resolves the issue, you can make it permanent by editing the `/etc/selinux/config` file and setting `SELINUX=permissive`.

### 12. Test the Configuration

Run the following command to check if HTTPD Apache has successfully loaded the virtual hosts:

```bash
sudo httpd -S
```

You should see the virtual hosts listed for ports 81, 82, and 83.

### 13. Access the Websites

Open your browser and navigate to the following URLs:

- `http://15.206.84.57:81` for `web1`
  ![Web1 Loaded in Browser](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/web1-loaded-screenshot.png)

- `http://15.206.84.57:82` for `web2`
  ![Web2 Loaded in Browser](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/web2-loaded-screenshot.png)

- `http://15.206.84.57:83` for `web3`
  ![Web3 Loaded in Browser](/hostingMultipleWebsites/CentOS+httpd_multipleWebsites/portBased/img/web3-loaded-screenshot.png)

Replace `15.206.84.57` with your EC2 instance's public IP address. You should see the `index.html` content for each respective website.

### 14. Troubleshooting

- **Firewall Issues**: Ensure that your EC2 instance’s security group allows inbound traffic on the custom ports (81, 82, 83).
- **HTTPD Apache Errors**: Check HTTPD Apache logs for any errors or misconfigurations:

  ```bash
  sudo tail -f /var/log/httpd/error_log
  ```

## Conclusion

By following these steps, you've successfully configured multiple websites on a single HTTPD Apache server using different ports. This method is useful when you want to host multiple sites on the same server without needing separate domain names.