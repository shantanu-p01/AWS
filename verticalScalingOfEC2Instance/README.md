# Vertical Scaling of EC2 Instance Type

### Step 1: Launch an EC2 Instance
1. **Go to EC2 service** and click on **Launch Instance**.
2. **Give a name** to the EC2 instance, e.g., `EC2-CCSA-TY-1113`.  
   ![EC2 Instance Name](/verticalScalingOfEC2Instance/img/nameEC2.png)

### Step 2: Select AMI
1. **Select the AMI** for your instance, e.g., `Ubuntu`.  
   ![EC2 Instance AMI Selection](/verticalScalingOfEC2Instance/img/amiSelection.png)

### Step 3: Create a Key Pair
1. **Create a new Key Pair**:
   - Click on **Create New Key Pair**.
   - Save the key pair to your local machine.  
   ![Creating Key Pair](/verticalScalingOfEC2Instance/img/keyPairCreation.png)

### Step 4: Launch the Instance
1. **Click on Launch Instance** to start the EC2 instance.

---

### Step 5: Stop the EC2 Instance
1. Navigate to the **EC2 instance** we just created.
2. Click on the **Instance State** dropdown button and select **Stop Instance**.  
   ![EC2 State Stop](/verticalScalingOfEC2Instance/img/ec2StateStop.png)
3. In the popup, click the **Stop** button.  
   ![EC2 State PopUp](/verticalScalingOfEC2Instance/img/ec2StopPopUp.png)
4. Wait for the instance to stop.

---

### Step 6: Change the Instance Type
1. After the instance is stopped, click on the **Actions** dropdown tab.
2. Click on **Instance Settings**, then select **Change Instance Type**.  
   ![EC2 Change Instance Type](/verticalScalingOfEC2Instance/img/ec2ChangeInstanceType.png)

3. In the **Change Instance Type** section, find the **New Instance Type** field.
4. Select the desired instance type (e.g., `t2.medium`), then scroll down and click on the **Change** button.  
   ![EC2 Change Instance Type](/verticalScalingOfEC2Instance/img/ec2T2.Medium.png)

---

### Step 7: Start the EC2 Instance
1. Go back to the **EC2 instance**.
2. Click on **Change Instance State**, and then select **Start Instance** to start the EC2 instance.  
   ![EC2 Change Instance State Start](/verticalScalingOfEC2Instance/img/ec2StateeStop.png)

3. Verify that the EC2 instance type has been successfully changed from `t2.micro` to `t2.medium`.  
   ![EC2 Change Instance Type Change](/verticalScalingOfEC2Instance/img/ec2typeChanged.png)