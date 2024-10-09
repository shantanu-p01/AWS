### Access S3 Bucket from an EC2 instance

---

#### Step 1: Launch an EC2 Instance

1. **Go to EC2 service** and click on **Launch Instance**.
2. **Give a name** to the EC2 instance, e.g., `EC2-CCSA-TY-1113`.  
   ![EC2 Instance Name](/accessS3FromEC2/img/nameEC2.png)

3. **Select the AMI** for your instance, e.g., `Ubuntu`.  
   ![EC2 Instance AMI Selection](/accessS3FromEC2/img/amiSelection.png)

4. **Create a new Key Pair**:
   - Click on **Create New Key Pair**.
   - Save the key pair to your local machine.
   ![Creating Key Pair](/accessS3FromEC2/img/keyPairCreation.png)

5. **Add User Data**:
   - Click on **Advanced Details** and scroll down to the **UserData** field.
   - Paste the following code to update, upgrade, and install AWS CLI in the EC2 instance:

     ```bash
     #!/bin/bash
     sudo apt update -y 
     sudo apt upgrade -y 
     ```
   ![EC2 Instance UserData](/accessS3FromEC2/img/userDataEC2.png)

6. **Click on Launch Instance** to start the EC2 instance.

---

#### Step 2: Create an S3 Bucket

1. **Navigate to the S3 service** and click on **Create Bucket**.
2. **Enter a name** for your S3 bucket.  
   ![S3 Bucket Name](/accessS3FromEC2/img/s3BucketName.png)

3. **Click on Create Bucket** to finish creating the bucket.

---

#### Step 3: Upload Files to the S3 Bucket

1. Go to the S3 bucket you just created and click on **Upload**.
2. Click on **Add Files** and select the files you want to upload.
3. Scroll down and click on **Upload**.  
   ![S3 File Upload](/accessS3FromEC2/img/s3FileUpload.png)

---

#### Step 4: Connect to the EC2 Instance

1. Go back to the **EC2 instance** and connect to it.
2. **Install AWS CLI** in the instance using:

   ```bash
   sudo snap install aws-cli --classic
   ```

3. Verify the installation by typing `aws` in the command line.  
   ![EC2 Instance aws-cli Installation](/accessS3FromEC2/img/awsCliInstallation.png)
   ![EC2 Instance aws-cli Installation Verification](/accessS3FromEC2/img/awsCliInstallationVerify.png)

---

### Method 1: Using IAM Role (Preferred)

#### Step 1: Create an IAM Role

1. Go to the **IAM service**, then to **Roles**, and click on **Create Role**.
2. Select **Trusted Entity Type** as **AWS Service** and **Use Case** as **EC2**, then click **Next**.  
   ![IAM Role Trusted Entity and Use Case](/accessS3FromEC2/img/iamRoleEntityUseCase.png)

3. In the **Add Permissions** section, search for `S3FullAccess` and select the policy, then click **Next**.  
   ![IAM Role Permission](/accessS3FromEC2/img/iamRolePermission.png)

4. **Name your IAM role**, then click **Create Role**.  
   ![IAM Role Name](/accessS3FromEC2/img/roleName.png)

#### Step 2: Attach IAM Role to the EC2 Instance

1. Go to the **EC2 service**, select the instance, and click on the **Actions** dropdown.
2. Click on **Security**, then select **Modify IAM Role**.  
   ![EC2 Modify Role](/accessS3FromEC2/img/ec2InstanceModifyRole1.png)

3. **Select the IAM Role** you just created, then click **Update IAM Role**.  
   ![EC2 Role Selection](/accessS3FromEC2/img/ec2InstanceModifyRole2.png)

---

### Method 2: Using IAM User (Without IAM Role)

#### Step 1: Create an IAM User

1. Go to the **IAM service**, then to **Users**, and click on **Create User**.
2. **Enter a name** for the IAM user.  
   ![IAM User Name](/accessS3FromEC2/img/iamUserName.png)

3. Click **Next**, then select **Attach Policy Directly**.  
   ![IAM Permissions Options](/accessS3FromEC2/img/iamPermissionOptions.png)

4. Search for `S3FullAccess` and attach the policy.  
   ![IAM Permission S3FullAccess](/accessS3FromEC2/img/iamAttachingS3Policy.png)

5. Click **Next** and then **Create User**.

#### Step 2: Create Access Keys for the IAM User

1. Go to the newly created IAM user and click on **Create Access Key**.  
   ![IAM Create Access Key](/accessS3FromEC2/img/iamUserCreateAccessKey.png)

2. Select **Command Line Interface (CLI)** as the use case.  
   ![IAM Access Key Use Case](/accessS3FromEC2/img/iamUserAccessKeyCliUseCase.png)

3. Check the confirmation field and click **Create Access Key**.  
   ![IAM Access Key Confirmation](/accessS3FromEC2/img/iamCreateAccessKeyConfirmation.png)

4. Copy the **Access Key ID** and **Secret Access Key**.  
   ![IAM Access Key Retrieved](/accessS3FromEC2/img/iamUserAccessKeySuccess.png)

#### Step 3: Configure AWS CLI on EC2

1. Go back to the EC2 instance and run the following command to configure AWS CLI:

   ```bash
   aws configure
   ```

2. Enter the **Access Key ID**, **Secret Access Key**, and **region** when prompted.  
   ![EC2 AWS Configure](/accessS3FromEC2/img/iamAWSConfigure.png)

---

#### Step 5: Access the S3 Bucket from EC2

1. **List the buckets**:

   ```bash
   aws s3 ls
   ```

   ![EC2 aws s3 ls](/accessS3FromEC2/img/ec2AWSS3ls.png)

2. **List the contents of a specific S3 bucket**:

   ```bash
   aws s3 ls s3://s3-ccsa-ty-1113
   ```

   ![EC2 aws s3 ls](/accessS3FromEC2/img/awsS3ListFiles.png)

#### Step 6: Write Data to S3 Bucket

1. Create a file on the EC2 instance:

   ```bash
   echo "Hello There!!!" > 1113.txt
   ```

   ![EC2 create file 1113.txt](/accessS3FromEC2/img/ec2CreateFile1113WriteAccess.png)

2. **Upload the file to S3**:

   ```bash
   aws s3 cp ~/1113.txt s3://s3-ccsa-ty-1113
   ```

   ![EC2 transfer file 1113.txt](/accessS3FromEC2/img/ec21113FileTransferSuccess.png)

3. **Read the file from S3**:

   ```bash
   aws s3 cp s3://s3-ccsa-ty-1113/1113.txt -
   ```

   ![EC2 read file 1113.txt](/accessS3FromEC2/img/ec2Read1113File.png)

