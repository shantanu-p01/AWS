# Host Multiple Websites in `/var/www/html` Using Apache2

This guide shows how to host multiple websites under the `/var/www/html` directory on an AWS EC2 instance running Apache2. You'll be able to access these sites using paths like `ipaddr/site1`, `ipaddr/site2`, and `ipaddr/site3`.

---

## Prerequisites

- **AWS EC2 Instance**: An Ubuntu-based EC2 instance.
- **SSH Key**: To securely connect to the instance.
- **Basic knowledge of Linux and Apache**.

---

## Step-by-Step Guide

### 1. SSH into Your EC2 Instance

Connect to your EC2 instance using SSH:

```bash
ssh -i "path/to/your-key.pem" ubuntu@your-ec2-public-ip
```

### 2. Update and Upgrade the Server

First, update the server and install any available updates:

```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Install Apache2 Web Server

Install the Apache web server to handle HTTP requests:

```bash
sudo apt install apache2 -y
```

### 4. Download Website Templates

You can download website templates using any of the following methods: `wget`, `curl`, or `git`.

#### Using `git`:

```bash
sudo apt install git -y
git clone https://github.com/learning-zone/website-templates
```

#### Using `wget`:

```bash
sudo apt install wget -y
wget https://github.com/learning-zone/website-templates/archive/refs/heads/master.zip
sudo apt install unzip -y
unzip master.zip
mv website-templates-master website-templates
```

#### Using `curl`:

```bash
sudo apt install curl -y
curl -L https://github.com/learning-zone/website-templates/archive/refs/heads/master.zip -o website-templates.zip
sudo apt install unzip -y
unzip website-templates.zip
mv website-templates-master website-templates
```

### 5. Create Subdirectories for Each Website

Create directories for each website inside the `/var/www/html` folder:

```bash
sudo mkdir /var/www/html/site1
sudo mkdir /var/www/html/site2
sudo mkdir /var/www/html/site3
```

### 6. Copy Template Files to Each Site Directory

Copy the pre-built website templates to their respective directories. Use the following commands:

```bash
sudo cp -r website-templates/above-educational-bootstrap-responsive-template/* /var/www/html/site1/
sudo cp -r website-templates/ace-responsive-coming-soon-template/* /var/www/html/site2/
sudo cp -r website-templates/add-life-health-fitness-free-bootstrap-html5-template/* /var/www/html/site3/
```

### 7. Restart Apache

Restart Apache for the changes to take effect:

```bash
sudo systemctl restart apache2
```

### 8. Access Your Websites

Now, you can access the sites in your browser by entering the following URLs:

- `http://your-ec2-public-ip/site1`
- `http://your-ec2-public-ip/site2`
- `http://your-ec2-public-ip/site3`

Each URL should display the content from the corresponding website template.

![Site 1](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/directoryBased/img/site1.png "Site 1")
![Site 2](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/directoryBased/img/site2.png "Site 2")
![Site 3](/hostingMultipleWebsites/Ubuntu+Apache2_multipleWebsites/directoryBased/img/site3.png "Site 3")

---

## Conclusion

You have successfully set up multiple websites using Apache2 and the directory method with pre-built templates. By placing different websites inside subdirectories under `/var/www/html`, you can easily host and manage multiple websites on a single EC2 instance.