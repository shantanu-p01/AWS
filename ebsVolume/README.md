## Setting Up an EC2 Instance with EBS Volume

### 1. Launching an EC2 Instance

1. **Go to EC2 Service**  
   Click on **Launch Instance**.

2. **Enter a name for the EC2 instance:** `BCA-CCSA-TY-1113-EC2`  
   ![EC2 Name](/ebsVolume/img/ec2Name.png)

3. **Select AMI:** Choose Ubuntu.  
   ![EC2 AMI](/ebsVolume/img/ec2AMI.png)

4. **Choose Instance Type:** Select `t2.micro`.  
   ![EC2 Instance Type](/ebsVolume/img/ec2InstanceType.png)

5. **Create a New Key Pair:**  
   - Click on **Create New Key Pair**.
   - Enter a name for the key pair, for example, `BCA-CCSA-TY-1113-KeyPair`.  
     ![EC2 Key Pair Creation](/ebsVolume/img/ec2KeyPairCreation.png)  
     ![EC2 Key Pair Name](/ebsVolume/img/ec2KeyPairName.png)

   - **Save the key pair** on your local machine securely, as you’ll need it for SSH access.

6. **Configure Network Settings:**  
   - Ensure **Auto-Assign Public IP** is enabled. If not, click on **Edit** and enable it.
   - **Allow SSH Traffic from Anywhere** and **Allow HTTP Traffic from the internet**.  
     ![EC2 Network Settings](/ebsVolume/img/ec2NetworkSettings.png)

7. **Launch the Instance:** Click on **Launch Instance**.  
   ![EC2 Instance Created](/ebsVolume/img/ec2InstanceCreated.png)

---

### 2. Connecting to the EC2 Instance via SSH

1. Open your local terminal and run the following command:

   ```bash
   ssh -i "path-of-your-key-pair" ubuntu@public-ip-address-of-your-instance
   ```

   ![Terminal SSH Connection](/ebsVolume/img/terminalSSHConnection.png)

2. Enter **Yes** when prompted to confirm the connection.

3. Once connected, install Git and Apache Web Server by running:

   ```bash
   sudo apt install git apache2 -y
   ```

   ![Terminal Git, Apache2 Installation](/ebsVolume/img/terminalGitApache2Installation.png)

4. **Verify the installations:**  
   - Check Git version: `git -v`  
     ![Terminal Git Installation Verify](/ebsVolume/img/terminalGitInstallationVerify.png)
   - Check Apache2 status: `sudo systemctl status apache2`  
     ![Terminal Apache2 Installation Verify](/ebsVolume/img/terminalApache2InstallationVerify.png)

5. **Enable Apache2 to start on boot:**

   ```bash
   sudo systemctl enable apache2
   ```

   ![Terminal Apache2 Enable](/ebsVolume/img/terminalApache2Enable.png)

---

### 3. Cloning a Website Template from GitHub

1. **Clone the website template repository:**

   ```bash
   git clone https://github.com/learning-zone/website-templates
   ```

   ![Terminal Git Clone Website Template Repo](/ebsVolume/img/terminalGitCloneWebsiteTemplateRepo.png)

2. **Navigate to a specific template:**  
   - List directories: `ls`
   - Change to the template directory (e.g., `zenlike`):  
     
     ```bash
     cd web*
     cd zenlike
     ls
     ```

     ![Terminal Commands 1](/ebsVolume/img/terminalCommandsLSandCD.png)  
     ![Terminal Commands 2](/ebsVolume/img/terminalCommandsCDzenlikeandLS.png)

3. **Copy the template files to Apache’s root directory:**

   ```bash
   sudo cp -r * /var/www/html
   ```

   ![Terminal Commands 3](/ebsVolume/img/terminalCPtoapache2Directory.png)

4. **Test the website:**  
   Open your browser and enter the **public IP address** of your EC2 instance to check if the web server is working.  
   ![WebServer Working 1](/ebsVolume/img/webServerWorking1.png)

---

### 4. Attaching an EBS Volume

1. **Go to EC2 > Elastic Block Store > Volumes:**  
   ![EC2 Volume Section](/ebsVolume/img/ec2VolumeSection.png)

2. **View the default attached volume** and create a new volume:  
   - Click on **Create Volume**.  
     ![EC2 Volume Existing One](/ebsVolume/img/ec2VolumeExisitngOne.png)

3. **Configure the new volume:**  
   - Select Volume Type as **General Purpose SSD (gp2)**.
   - Set Volume Size to **5GB**.
   - Ensure the **Availability Zone** matches your EC2 instance.  
     ![EC2 Volume Settings](/ebsVolume/img/ec2VolumeSettingsVolTypeVolSizeVolAV.png)

4. **Create the Volume:** Click **Create Volume**.

5. **Name the Volumes:** Rename the existing and new volumes as **old volume** and **new volume** respectively.  
   ![EC2 Volume Created and Renamed](/ebsVolume/img/ec2VolumeCreatedAndRenamed.png)

6. **Attach the New Volume:**  
   - Select the new volume, click on **Actions > Attach Volume**.
   - Choose your EC2 instance and an available device name (e.g., `/dev/sdd`).  
     ![EC2 Volume Attach Volume](/ebsVolume/img/ec2VolumeAttachVolume.png)  
     ![EC2 Volume Attach Volume Basic Details](/ebsVolume/img/ec2VolumeAttachVolBasicDetails.png)

7. **Confirm Attachment:** Click **Attach Volume**.  
   ![EC2 Volume Attached Successfully](/ebsVolume/img/ec2VolumeNewVolAttached.png)

---

### 5. Configuring the New Volume on the EC2 Instance

1. **Switch to root user:**

   ```bash
   sudo -i
   ```

   ![Terminal Switch to Root User](/ebsVolume/img/tereminalSwitchToRootUser.png)

2. **List volumes to confirm attachment:**

   ```bash
   fdisk -l
   ```

   - Here, you’ll see `/dev/xvda` (8GB) as the old volume and `/dev/xvdd` (5GB) as the new volume.  
     ![Terminal List Volumes](/ebsVolume/img/terminalListVolumes.png)

3. **Partition the new volume:**

   ```bash
   fdisk /dev/xvdd
   ```

   - **Commands to enter for partitioning:**
     - `n` - New partition  
       ![Terminal Command n](/ebsVolume/img/terminalCommandn.png)
     - `p` - Primary partition  
       - Press **Enter** three times for default values.  
       ![Terminal Command p](/ebsVolume/img/terminalCommandp.png)
     - `p` - View partition table  
       ![Terminal Command p Verify](/ebsVolume/img/terminalCommandpVerify.png)
     - `w` - Write changes  
       ![Terminal Command w](/ebsVolume/img/terminalCommandw.png)

4. **Format the new partition:**

   ```bash
   mkfs.ext4 /dev/xvdd1
   ```

   ![Terminal Format Partition](/ebsVolume/img/terminalMKFSEXT4.png)

5. **Mount the volume permanently:**  
   - Open the `fstab` file:

     ```bash
     nano /etc/fstab
     ```

   - Save and exit (`Ctrl+O`, `Enter`, then `Ctrl+X`).  
     ![Terminal Permanent Mount](/ebsVolume/img/terminalNanoPermanantMount.png)

6. **Mount and verify the entries:**

   ```bash
   mount -a
   systemctl daemon-reload
   df -h
   ```

   ![Terminal Mounting Entries, Reloading Daemon, Verifying Mounted Entries](/ebsVolume/img/terminalMountingAllEntriesReloadingDaemonAndVerifyingTheMountedEntries.png)

7. **Check `/var/www/html/images` directory:** You’ll see a `lost+found` folder there.  
   ![Terminal Apache2 Lost+Found](/ebsVolume/img/terminalApache2Lost+Found.png)

8. **Verify in browser:** After refreshing the page, you’ll notice the images are missing.  
   ![WebServer Images Gone](/ebsVolume/img/webServerImagesGone.png)

9. **Copy images back to `/var/www/html/images`:**  
   - Exit root user:

     ```bash
     exit
     ```

   - Run:

     ```bash
     cp -r images/* /var/www/html/images
     ```

   - Verify with `ls`.  
     ![Images Copied Back to /var/www/html/images](/ebsVolume/img/terminalApache2ImagesCopied.png)

10

. **Final Verification in browser:** Images are now back.  
    ![WebServer Final Verification Working](/ebsVolume/img/webServerImagesBackAgain.png)