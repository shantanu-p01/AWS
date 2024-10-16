### Steps to Launch an EC2 Instance via CloudFormation

1. **Go to AWS CloudFormation Service**
   - Open AWS Management Console and navigate to **CloudFormation**.
   - Click on **Create Stack**.

2. **Create a Stack**
   - In the **Create Stack** section, for **Prepare Template**, select **Choose an existing template**.

3. **Create a YAML File**
   - On your local machine, create a YAML file and paste the following content into it.
   - **Ensure to replace** the **Instance Name**, **KeyPair Name**, **Region**, and **AMI ID** with your specific values:

   ```yaml
   Resources:
     CloudFormationEC21113: # Replace this with the name you want for the EC2 instance
       Type: AWS::EC2::Instance
       Properties:
         AvailabilityZone: ap-northeast-1a  # Replace with your desired region's availability zone
         ImageId: ami-0b20f552f63953f0e     # Replace with the AMI ID you want to use
         InstanceType: t2.micro
         KeyName: CloudFormationKeyPair1113  # Replace with the KeyPair name you created
         SecurityGroups:
           - !Ref CloudFormationEC2SecurityGroup1113  # Reference the Security Group below

     CloudFormationEC2SecurityGroup1113:  # Replace with the name for the Security Group
       Type: AWS::EC2::SecurityGroup
       Properties:
         GroupDescription: Allow SSH and HTTP
         SecurityGroupIngress:
           - IpProtocol: tcp
             FromPort: 22
             ToPort: 22
             CidrIp: 0.0.0.0/0
           - IpProtocol: tcp
             FromPort: 80
             ToPort: 80
             CidrIp: 0.0.0.0/0
   ```

4. **Continue Stack Creation**
   - Click **Next**.
   - For **Stack Name**, enter `CCSA-BCA-TY-1113`.

5. **Configure IAM Role for EC2**
   - Open a new tab and go to **AWS IAM**.
   - In the left sidebar, click on **Roles**.
   - Click on **Create Role**.

6. **Create Role**
   - Select **Trust Entity Type: AWS Service**.
   - Select **Use Case: CloudFormation**.
   - In the **Add Permission** section, search for `AmazonEC2FullAccess`.
   - Click **Next** and enter the **Role Name**: `ROLE-BCA-CCSA-TY-1113`.
   - Click on **Create Role**.
   - Copy the **ARN** of the role just created.

7. **Attach IAM Role to Stack**
   - Navigate back to the CloudFormation stack creation process.
   - In the **Permissions** section, select **IAM Role ARN** from the dropdown and paste the **ARN** copied from the role.
   - In the **Stack Failure Options**, leave everything as default.
   - Click **Next**.

8. **Review and Submit**
   - Review all the configurations.
   - Click **Submit**.

9. **Verify EC2 Instance**
   - Go to the **EC2 Service**.
   - You should see an instance in the **Initializing** state.

This YAML file will deploy an EC2 instance with SSH and HTTP access using a KeyPair, Security Group, and IAM Role for EC2 access.
