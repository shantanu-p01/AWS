# Attaching Load Balancer to EC2 Instances

Follow the steps below to attach a Load Balancer to EC2 Instances using the AWS Management Console.

---

## Step 1: Create Security Group

1. Navigate to **EC2 Service** in AWS Management Console.
2. On the left panel, scroll down to the **Network & Security** section and click on **Security Groups**.
3. Click on **Create Security Group** and fill in the following details:
   - **Name**: `SG-CCSA-TY-1113` (Example Name)
   - **Description**: `SG-For-Launch-Template` (Example Description)

   ![SG-BasicDetails](/ec2LoadBalancer/img/SG-BasicDetails.png)

4. Add **Inbound Rules**:
   - **Type**: SSH | **Source**: Anywhere-IPv4
   - **Type**: HTTP | **Source**: Anywhere-IPv4
   - **Type**: HTTPS (Optional) | **Source**: Anywhere-IPv4

   ![SG-InboundRules](/ec2LoadBalancer/img/SG-InboundRules.png)

5. Click on **Create Security Group**.
6. Your Security Group will be created successfully.

   ![SG-CreationSuccess](/ec2LoadBalancer/img/SG-CreationSuccess.png)

---

## Step 2: Create Launch Template

1. Go back to **EC2 Service**.
2. In the **Instances** section on the left panel, click on **Launch Templates**.
3. Click on **Create Launch Template** and fill in the following details:
   - **Name**: `LT-CCSA-TY-1113` (Example Name)

   ![LT-Name](/ec2LoadBalancer/img/LT-Name.png)

4. Select **Amazon Machine Image (AMI)**: Choose **Ubuntu**.

   ![LT-AMI](/ec2LoadBalancer/img/LT-AMI.png)

5. Select **Instance Type**: `t2.micro`.

   ![LT-t2.micro](/ec2LoadBalancer/img/LT-t2.micro.png)

6. For the **Key Pair**, click on **Create new Key Pair** and fill in the details:
   - **Name**: `KP-CCSA-TY-1113` (Example Name)
   - **Type**: RSA
   - **Format**: `.pem`

   ![LT-KeyPair](/ec2LoadBalancer/img/LT-KeyPair.png)

7. Click on **Create Key Pair**.

8. In **Network Settings**, select the security group you created earlier.

   ![LT-SecurityGroup](/ec2LoadBalancer/img/LT-SecurityGroup.png)

9. In the **Advanced Details** section, scroll down to the **User Data** section and enter the following script:

   ```bash
   #!/bin/bash
   sudo apt update -y 
   sudo apt upgrade -y 
   sudo apt install apache2 -y 
   sudo systemctl start apache2 
   sudo systemctl enable apache2 
   echo "<h1>This Message is from $(hostname -f)</h1>" > /var/www/html/index.html
   ```

   ![LT-UserData](/ec2LoadBalancer/img/LT-UserData.png)

10. Recheck all details and click **Create Launch Template**.

11. The launch template will be created successfully.

---

## Step 3: Launch EC2 Instances

1. Go back to the **EC2 Service**.
2. In the **Instances** section, click on the dropdown beside the **Launch Instances** button and select **Launch Instance from Template**.

   ![LT-Selection](/ec2LoadBalancer/img/LT-Selection.png)

3. Select the launch template you just created.

   ![LT-TemplateSelection](/ec2LoadBalancer/img/LT-TemplateSelection.png)

4. Change the number of instances you want to launch in the **Summary** section.

   ![LT-NumberOfInstances](/ec2LoadBalancer/img/LT-NumberOfInstances.png)

5. Recheck everything and click **Launch Instance**.
6. Go back to the **Instances** section, and you will see the newly created instances.

   ![LT-InstancesCreated](/ec2LoadBalancer/img/LT-InstancesCreated.png)

---

## Step 4: Create Target Group

1. In the **EC2 Service**, scroll down to the **Load Balancing** section and click on **Target Groups**.
2. Click on **Create Target Group**.
3. In the **Basic Configuration** section:
   - Choose **Target Group Type** as **Instances**.

   ![TG-Type](/ec2LoadBalancer/img/TG-Type.png)

4. Enter the following details:
   - **Name**: Your preferred target group name.
   - **Protocol**: HTTP
   - **Port**: 80
   - **IP Address Type**: IPv4

   ![TG-Name](/ec2LoadBalancer/img/TG-Name.png)

5. In the **Health Checks** section, set the protocol to **HTTP** and the path to `/`.

   ![TG-Health](/ec2LoadBalancer/img/TG-Health.png)

6. Click **Next**.

7. In the **Available Instances** section, select the instances you created earlier and click **Include as Pending Below**.

   ![TG-AvailableInstances](/ec2LoadBalancer/img/TG-AvailableInstances.png)

8. Recheck the selected targets and click **Create Target Group**.

9. The target group will be created successfully.

   ![TG-Success](/ec2LoadBalancer/img/TG-Success.png)

---

## Step 5: Create Application Load Balancer

1. In the **EC2 Service**, scroll down to the **Load Balancing** section and click on **Load Balancers**.
2. Click on **Create Load Balancer**.
3. In the **Load Balancer Type** section, choose **Application Load Balancer** and click **Create**.

   ![LB-Type](/ec2LoadBalancer/img/LB-Type.png)

4. In the **Basic Configuration** section, enter the following details:
   - **Name**: `ALB-CCSA-TY-1113` (Example Name)
   - **Scheme**: Internet-facing
   - **IP Address Type**: IPv4

   ![LB-Name](/ec2LoadBalancer/img/LB-Name.png)

5. In the **Network Mapping** section, select all the **Availability Zones**.

   ![LB-Zones](/ec2LoadBalancer/img/LB-Zones.png)

6. In the **Security Group** section, select the security group you created earlier.

   ![LB-SecurityGroup](/ec2LoadBalancer/img/LB-SecurityGroup.png)

7. In the **Listener and Routing** section, select the target group you created earlier.

   ![LB-ListenerAndRouting](/ec2LoadBalancer/img/LB-ListenerAndRouting.png)

8. Scroll down, review everything, and click **Create Load Balancer**.

9. The load balancer status will show as **Provisioning**. Wait until the status changes to **Active**.

   ![LB-Active](/ec2LoadBalancer/img/LB-Active.png)

---

## Step 6: Test Load Balancer

1. Copy the **DNS Name** of the load balancer and open it in your browser.
2. You should see the Private IP DNS name of one of the EC2 instances in the browser.
3. Refresh the page to see the private IP DNS names of the other EC2 instances being served in a round-robin fashion.

   ![LB-IP1](/ec2LoadBalancer/img/LB-IP1.png)
   ![LB-IP2](/ec2LoadBalancer/img/LB-IP2.png)
   ![LB-IP3](/ec2LoadBalancer/img/LB-IP3.png)

---

Now you have successfully attached a Load Balancer to EC2 Instances using AWS!