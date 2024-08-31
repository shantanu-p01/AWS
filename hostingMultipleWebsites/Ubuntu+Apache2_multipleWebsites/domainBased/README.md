# Multi-Domain Web Hosting on a Single Apache Server

This project demonstrates how to host multiple websites on a single Apache server using an AWS EC2 instance. By following these steps, you will be able to set up a multi-domain environment where each website has its own domain and custom configuration.

## Prerequisites

- **Knowledge of Difference between WebPages and Website**
- **AWS EC2 Instance**: An Ubuntu-based EC2 instance running in AWS.
- **Domain Names**: Make sure you have access to domain names or use local domain settings for testing.
- **SSH Key**: An SSH key to securely connect to the server.

## Step-by-Step Guide

### 1. SSH into the Server

Connect to your EC2 instance using SSH:

```bash
ssh -i "C:\Users\GawD\Downloads\WebServer.pem" ubuntu@13.201.22.142
```

![SSH Connection to AWS EC2 Instance using MobaXterm](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/SSH%20Connection%20MobaXterm.png)

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

### 4. Copy Website Files to the Server

Use `scp` to securely copy the website files (`web1`, `web2`, `web3`) from your local machine to the server's home directory:

```bash
scp -i "C:\Users\GawD\Downloads\WebServer.pem" -r web1 web2 web3 ubuntu@13.201.22.142:~
```

![Transferring files from Local Machine to AWS EC2](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Transferring%20files%20from%20Local%20Machine%20to%20AWS%20EC2.png)

### 5. Create Directories for Each Website

Create a directory structure for each website in Apache's root directory:

```bash
sudo mkdir -p /var/www/web1.kubez.cloud && sudo mkdir -p /var/www/web2.kubez.cloud && sudo mkdir -p /var/www/web3.kubez.cloud
```

### 6. Copy Files to Apache Directories

Copy the `index.html` files for each website to their respective Apache directories:

```bash
sudo cp web1/index.html /var/www/web1.kubez.cloud
sudo cp web2/index.html /var/www/web2.kubez.cloud
sudo cp web3/index.html /var/www/web3.kubez.cloud
```

### 7. Set Ownership and Permissions

Ensure that Apache has the correct permissions to serve the files:

```bash
sudo chown -R $USER:$USER /var/www/web1.kubez.cloud /var/www/web2.kubez.cloud /var/www/web3.kubez.cloud
sudo chmod -R 755 /var/www
```

### 8. Create Custom Configuration Files for Each Website

Create a custom Apache configuration file for each website:

```bash
sudo nano /etc/apache2/sites-available/web1.kubez.cloud.conf
sudo nano /etc/apache2/sites-available/web2.kubez.cloud.conf
sudo nano /etc/apache2/sites-available/web3.kubez.cloud.conf
```

**Sample Configuration (`web1.kubez.cloud.conf`):**
```apache
<VirtualHost *:80>
    ServerAdmin shantanu.verulkar.01@gmail.com
    DocumentRoot /var/www/web1.kubez.cloud
    ServerName web1.kubez.cloud
    ServerAlias www.web1.kubez.cloud
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Repeat the above steps for `web2.kubez.cloud.conf` and `web3.kubez.cloud.conf` with the appropriate changes.

![Custom Conf](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Custom%20Conf.png)

### 9. Enable the Configuration Files

Enable each website's configuration file in Apache:

```bash
sudo a2ensite web1.kubez.cloud.conf web2.kubez.cloud.conf web3.kubez.cloud.conf
```

### 10. Restart Apache Web Server

Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

### 11. Add Domains to Hosts File (For Local Testing)

Add entries to your local `hosts` file to map the domains to the server's IP address:

```bash
sudo nano /etc/hosts
```

Add the following lines:

```
13.201.22.142 web1.kubez.cloud
13.201.22.142 web2.kubez.cloud
13.201.22.142 web3.kubez.cloud
```

![Adding Domains to hosts file](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Adding%20Domains%20to%20hosts%20file.png)

### 12. Set Up DNS Configuration

For the domains to be publicly accessible, configure your DNS settings to point to the public IP address of the EC2 instance as an `A` record. 

> **Note:** The exact steps to set up DNS records depend on your domain registrar. Generally, you need to add an `A` record for each domain (`web1.kubez.cloud`, `web2.kubez.cloud`, `web3.kubez.cloud`) pointing to your EC2 instance's public IP address.

![DNS Configuration Screenshot](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/CloudFlare%20DNS%20Records.png)

## Testing and Verification

1. **Check Apache Configuration:**

   Run `apache2ctl -S` to check if Apache has successfully loaded the virtual hosts:

   ```bash
   sudo apache2ctl -S
   ```

   ![Check Apache Config](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Check%20Apache%20Config.png)

2. **Open Each Website in Browser:**

   Open your browser and navigate to the following URLs:

   - `http://web1.kubez.cloud`
   - `http://web2.kubez.cloud`
   - `http://web3.kubez.cloud`

   You should see the `index.html` content for each respective website.

   ![Website 1 Loaded in Browser](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Web1%20over%20Custom%20Domain%20routed%20to%20EC2%20Public%20IP.png)
   ![Website 2 Loaded in Browser](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Web2%20over%20Custom%20Domain%20routed%20to%20EC2%20Public%20IP.png)
   ![Website 3 Loaded in Browser](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/domainBased/img/Web3%20over%20Custom%20Domain%20routed%20to%20EC2%20Public%20IP.png)

## Troubleshooting

- **Permission Denied Error:** Ensure that the SSH key is correctly configured and has the right permissions.
- **DNS Issues:** Verify that your domain names are correctly set up and pointing to the server's IP address.
- **Apache Errors:** Check Apache logs for any errors or misconfigurations.

## Conclusion

By following these steps, you have successfully set up multiple websites on a single Apache server hosted on an AWS EC2 instance. This is a cost-effective solution for hosting multiple domains on a single server.