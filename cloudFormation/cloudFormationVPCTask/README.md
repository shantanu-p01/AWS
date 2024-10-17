# Launching EC2 Instances in a VPC with CloudWatch and SNS Notifications



## Step 1: Go to AWS CloudFormation Service
- Open the AWS Management Console and navigate to **CloudFormation**.
- Click on **Create Stack**.

    ![CloudFormation Create Stack](/cloudFormation/cloudFormationVPCTask/img/cloudFormationMainPage.png)

## Step 2: Create a Stack
- In the **Create Stack** section, for **Prepare Template**, select **Choose an existing template**.

    ![CloudFormation Prepare Stack](/cloudFormation/cloudFormationVPCTask/img/cloudFormationCreateStack.png)

## Step 3: Create a YAML File
- On your local machine, create a YAML file and paste the following content into it:

    ```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    Parameters:
      KeyPair:
        Type: String
        Description: Name of an EC2 Keypair
        Default: 1113AssignmentPubPrivSubentEC2 
      Email:
        Type: String
        Description: Email for Notifications
        Default: shantanu.verulkar.01@gmail.com 

    Resources:
      VPC:
        Type: AWS::EC2::VPC
        Properties:
          CidrBlock: 10.0.0.0/16
          EnableDnsSupport: true
          EnableDnsHostnames: true

      PublicSubnet:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: 10.0.1.0/24
          MapPublicIpOnLaunch: true
          AvailabilityZone: ap-northeast-1a

      PrivateSubnet:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId: !Ref VPC
          CidrBlock: 10.0.2.0/24
          AvailabilityZone: ap-northeast-1a

      InternetGateway:
        Type: AWS::EC2::InternetGateway

      AttachGateway:
        Type: AWS::EC2::VPCGatewayAttachment
        Properties:
          VpcId: !Ref VPC
          InternetGatewayId: !Ref InternetGateway

      PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
          VpcId: !Ref VPC

      PublicRoute:
        Type: AWS::EC2::Route
        Properties:
          RouteTableId: !Ref PublicRouteTable
          DestinationCidrBlock: 0.0.0.0/0
          GatewayId: !Ref InternetGateway

      PublicSubnetRouteTableAssociation:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PublicSubnet
          RouteTableId: !Ref PublicRouteTable

      NatEIP:
        Type: AWS::EC2::EIP

      NatGateway:
        Type: AWS::EC2::NatGateway
        Properties:
          SubnetId: !Ref PublicSubnet
          AllocationId: !GetAtt NatEIP.AllocationId

      PrivateRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
          VpcId: !Ref VPC

      PrivateRoute:
        Type: AWS::EC2::Route
        Properties:
          RouteTableId: !Ref PrivateRouteTable
          DestinationCidrBlock: 0.0.0.0/0
          NatGatewayId: !Ref NatGateway

      PrivateSubnetRouteTableAssociation:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId: !Ref PrivateSubnet
          RouteTableId: !Ref PrivateRouteTable

      PublicEC2Instance:
        Type: AWS::EC2::Instance
        Properties: 
          InstanceType: t2.micro
          KeyName: !Ref KeyPair
          ImageId: ami-0b20f552f63953f0e
          SubnetId: !Ref PublicSubnet
          SecurityGroupIds:
            - !Ref PublicInstanceSecurityGroup
          Tags:
            - Key: Name
              Value: CCSA-BCA-TY-1113-PublicEC2Instance

      PublicInstanceSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
          GroupDescription: allow ssh and http
          VpcId: !Ref VPC
          SecurityGroupIngress:
            - IpProtocol: tcp
              FromPort: 22
              ToPort: 22
              CidrIp: 0.0.0.0/0
            - IpProtocol: tcp
              FromPort: 80
              ToPort: 80
              CidrIp: 0.0.0.0/0

      PrivateEC2Instance:
        Type: AWS::EC2::Instance
        Properties:
          InstanceType: t2.micro
          KeyName: !Ref KeyPair
          ImageId: ami-074c801439a538a43
          SubnetId: !Ref PrivateSubnet
          SecurityGroupIds:
            - !Ref PrivateInstanceSecurityGroup
          Tags:
            - Key: Name
              Value: CCSA-BCA-TY-1113-PrivateEC2Instance

      PrivateInstanceSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
          GroupDescription: allow ssh access
          VpcId: !Ref VPC
          SecurityGroupIngress:
            - IpProtocol: tcp
              FromPort: 22
              ToPort: 22
              CidrIp: 0.0.0.0/0

      NotificationTopic:
        Type: AWS::SNS::Topic
        Properties:
          Subscription:
            - Protocol: email
              Endpoint: !Ref Email

      InstanceLaunchAlarm:
        Type: AWS::CloudWatch::Alarm
        Properties:
          AlarmDescription: "Public EC2 Instance launched successfully"
          MetricName: "StatusCheckFailed"
          Namespace: "AWS/EC2"
          Statistic: "Minimum"
          Period: 300
          EvaluationPeriods: 1
          Threshold: 0
          ComparisonOperator: "GreaterThanThreshold"
          Dimensions:
            - Name: "InstanceId"
              Value: !Ref PublicEC2Instance
          AlarmActions:
            - !Ref NotificationTopic
          OKActions:
            - !Ref NotificationTopic
          InsufficientDataActions:
            - !Ref NotificationTopic

      PrivateInstanceLaunchAlarm:
        Type: AWS::CloudWatch::Alarm
        Properties:
          AlarmDescription: "Private EC2 Instance launched successfully"
          MetricName: "StatusCheckFailed"
          Namespace: "AWS/EC2"
          Statistic: "Minimum"
          Period: 300
          EvaluationPeriods: 1
          Threshold: 0
          ComparisonOperator: "GreaterThanThreshold"
          Dimensions:
            - Name: "InstanceId"
              Value: !Ref PrivateEC2Instance
          AlarmActions:
            - !Ref NotificationTopic
          OKActions:
            - !Ref NotificationTopic
          InsufficientDataActions:
            - !Ref NotificationTopic

    Outputs:
      PublicInstance:
        Description: Public EC2 Instance
        Value: !Ref PublicEC2Instance
      PrivateInstance:
        Description: Private EC2 Instance
        Value: !Ref PrivateEC2Instance
    ```

- Then go back to the Stack Creation page and in the **Specify Template** section select **Upload a template file**.
- Click on **Choose file** to select the YAML file from your local machine and upload it.

    ![CloudFormation YAML Uploading](/cloudFormation/cloudFormationVPCTask/img/cloudFormationYAMLUploading.png)

- Upon successful upload, you will see the S3 link to your YAML file.

    ![CloudFormation YAML Uploaded](/cloudFormation/cloudFormationVPCTask/img/cloudFormationYAMLUploaded.png)

- Click the **Next** button.

## Step 4: Specify Stack Details
- In the **Specify Stack Details** section, enter a name for your stack.
- Below the stack name field, review the parameters you defined in the YAML file, and click the **Next** button.

    ![CloudFormation Stack Name & Parameters](/cloudFormation/cloudFormationVPCTask/img/cloudFormationSpecifyTask.png)

## Step 5: Configure Stack Options
- Scroll down to the end of the **Configure stack options** section. You can leave everything as default and click the **Next** button.

## Step 6: Review and Create Stack
- Review all the configurations you made and click the **Submit** button. Your stack will now start creating.

    ![CloudFormation Stack Submitted](/cloudFormation/cloudFormationVPCTask/img/cloudFormationStackSubmitted.png)

- Keep refreshing the Stack's **Events** section to monitor for any errors that could cause a rollback.

## Summary of YAML Template
The YAML template creates an AWS environment consisting of a Virtual Private Cloud (VPC) with a public and private subnet. It provisions two EC2 instances: one in the public subnet (Ubuntu) and one in the private subnet (CentOS). An Internet Gateway allows external access to the public instance, while a NAT Gateway enables the private instance to access the internet securely.

The template also sets up security groups to control access to the instances and includes an Amazon SNS topic for notifications. If both EC2 instances are launched successfully and do not fail within the first 5 minutes, a CloudWatch Alarm triggers, sending a notification to the specified email address.

## Step 7: Confirm SNS Subscription
- While the stack is being created, you will receive an email from SNS for subscription confirmation. Confirm the subscription.

    ![CloudFormation SNS Subscription Confirmation Email](/cloudFormation/cloudFormationVPCTask/img/SNSConfirmationEmail.png)

    ![CloudFormation SNS Subscription Confirmed](/cloudFormation/cloudFormationVPCTask/img/cloudFormationSubscriptionConfirmed.png)

## Step 8: Monitor Stack Creation Status
- Go back to the CloudFormation stack and wait for the stack status to return **Create Complete**.

    ![CloudFormation Status - Completed](/cloudFormation/cloudFormationVPCTask/img/cloudFormationStatusCreateComplete.png)

## Step 9: Check CloudWatch Alarms
- Go to **CloudWatch Alarms** and check the state of the alarms. Wait until the alarm state changes to **OK**, which indicates that notifications will be sent to the specified email address.

    ![CloudWatch State - OK](/cloudFormation/cloudFormationVPCTask/img/cloudWatchAlarmsOK.png)

## Step 10: Check Email Notifications
- Check your email inbox. You should receive two emails:
  - One for the successful launch of the private instance.

    ![E-Mail Private Instance Launch Success](/cloudFormation/cloudFormationVPCTask/img/email-CloudFormationPrivateInstance.png)

  - Another for the successful launch of the public instance, specifying that both instances have been launched successfully.

    ![E-Mail Public Instance Launch Success](/cloudFormation/cloudFormationVPCTask/img/email-CloudFormationPublicInstance.png)

    
#### And Done!!!