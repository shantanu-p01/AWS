### Steps to Launch 2 EC2 Instances on Public and Private Subnet of VPC followed by CloudWatch and SNS to monitor the initialization state of the EC2 Instance on Success Send Email to specified email address

1. **Go to AWS CloudFormation Service**
   - Open AWS Management Console and navigate to **CloudFormation**.
   - Click on **Create Stack**.
    ![CloudFormation Create Stack](/cloudFormation/cloudFormationVPCTask/img/cloudFormationMainPage.png)

2. **Create a Stack**
   - In the **Create Stack** section, for **Prepare Template**, select **Choose an existing template**.
    ![CloudFormation Prepare Stack](/cloudFormation/cloudFormationVPCTask/img/cloudFormationCreateStack.png)

3. **Create a YAML File**
   - On your local machine, create a YAML file and paste the following content into it.

    ```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    Parameters:
    KeyPair:
        Description: Name of an existing EC2 KeyPair to enable SSH access to the instances
        Type: String
        Default: 1113AssignmentPubPrivSubentEC2 # Default key pair name, can be changed during stack creation
        ConstraintDescription: Must be the name of an existing EC2 KeyPair.
    Email:
        Description: Email address to receive notifications
        Type: String
        Default: shantanu.verulkar.01@gmail.com # Default email address for notifications

    Resources:
    # VPC Creation
    VPC:
        Type: AWS::EC2::VPC
        Properties:
        CidrBlock: 10.0.0.0/16
        EnableDnsSupport: true
        EnableDnsHostnames: true
        Tags:
            - Key: Name
            Value: MyVPC

    # Public Subnet
    PublicSubnet:
        Type: AWS::EC2::Subnet
        Properties:
        VpcId: !Ref VPC
        CidrBlock: 10.0.1.0/24
        MapPublicIpOnLaunch: true
        AvailabilityZone: !Select
            - 0
            - !GetAZs ''
        Tags:
            - Key: Name
            Value: PublicSubnet

    # Private Subnet
    PrivateSubnet:
        Type: AWS::EC2::Subnet
        Properties:
        VpcId: !Ref VPC
        CidrBlock: 10.0.2.0/24
        AvailabilityZone: !Select
            - 0
            - !GetAZs ''
        Tags:
            - Key: Name
            Value: PrivateSubnet

    # Internet Gateway for Public Subnet
    InternetGateway:
        Type: AWS::EC2::InternetGateway
        Properties: {}

    AttachGateway:
        Type: AWS::EC2::VPCGatewayAttachment
        Properties:
        VpcId: !Ref VPC
        InternetGatewayId: !Ref InternetGateway

    # Route Table for Public Subnet
    PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
        VpcId: !Ref VPC
        Tags:
            - Key: Name
            Value: PublicRouteTable

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

    # NAT Gateway for Private Subnet
    NatEIP:
        Type: AWS::EC2::EIP
        DependsOn: AttachGateway
        Properties:
        Domain: vpc

    NatGateway:
        Type: AWS::EC2::NatGateway
        DependsOn: AttachGateway
        Properties:
        SubnetId: !Ref PublicSubnet
        AllocationId: !GetAtt NatEIP.AllocationId

    # Route Table for Private Subnet
    PrivateRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
        VpcId: !Ref VPC
        Tags:
            - Key: Name
            Value: PrivateRouteTable

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

    # EC2 Instance in Public Subnet (Ubuntu)
    PublicEC2Instance:
        Type: AWS::EC2::Instance
        Properties: 
        InstanceType: t2.micro
        KeyName: !Ref KeyPair
        ImageId: ami-0b20f552f63953f0e # Replace with your preferred AMI ID for Ubuntu or another instance
        SubnetId: !Ref PublicSubnet
        Tags:
            - Key: Name
            Value: 1113PublicEC2Instance
        SecurityGroupIds:
            - !GetAtt PublicInstanceSecurityGroup.GroupId

    # Security Group for Public EC2
    PublicInstanceSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
        GroupDescription: Allow SSH and HTTP access to the public instance
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

    # EC2 Instance in Private Subnet (CentOS)
    PrivateEC2Instance:
        Type: AWS::EC2::Instance
        Properties:
        InstanceType: t2.micro
        KeyName: !Ref KeyPair
        ImageId: ami-074c801439a538a43 # Replace with your preferred AMI ID for CentOS
        SubnetId: !Ref PrivateSubnet
        Tags:
            - Key: Name
            Value: 1113PrivateEC2Instance
        SecurityGroupIds:
            - !GetAtt PrivateInstanceSecurityGroup.GroupId

    # Security Group for Private EC2
    PrivateInstanceSecurityGroup:
        Type: AWS::EC2::SecurityGroup
        Properties:
        GroupDescription: Allow SSH access to the private instance
        VpcId: !Ref VPC
        SecurityGroupIngress:
            - IpProtocol: tcp
            FromPort: 22
            ToPort: 22
            CidrIp: 0.0.0.0/0

    # SNS Topic for Notifications
    NotificationTopic:
        Type: AWS::SNS::Topic
        Properties:
        Subscription:
            - Protocol: email
            Endpoint: !Ref Email

    # CloudWatch Alarm to monitor EC2 instance state
    InstanceLaunchAlarm:
        Type: AWS::CloudWatch::Alarm
        Properties:
        AlarmDescription: "Alarm when both EC2 instances are launched"
        MetricName: "StatusCheckFailed"
        Namespace: "AWS/EC2"
        Statistic: "Minimum"
        Period: "300"
        EvaluationPeriods: "1"
        Threshold: "0"
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

    # Additional Alarm for Private Instance
    PrivateInstanceLaunchAlarm:
        Type: AWS::CloudWatch::Alarm
        Properties:
        AlarmDescription: "Alarm when the Private EC2 instance is launched"
        MetricName: "StatusCheckFailed"
        Namespace: "AWS/EC2"
        Statistic: "Minimum"
        Period: "300"
        EvaluationPeriods: "1"
        Threshold: "0"
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
        Description: Public EC2 Instance ID
        Value: !Ref PublicEC2Instance
    PrivateInstance:
        Description: Private EC2 Instance ID
        Value: !Ref PrivateEC2Instance

    ```
    
    Then go back to the Stack Creation and in the Specify Template section select Upload a template file

    then click on choose file then choose the file present in your local machine and upload it
    ![CloudFormation YAML Uploading](/cloudFormation/cloudFormationVPCTask/img/cloudFormationYAMLUploading.png)

    as a confirmation youll be able to see the s3 link of your yaml file which you just uploaded
    
    ![CloudFormation YAML Uploaded](/cloudFormation/cloudFormationVPCTask/img/cloudFormationYAMLUploaded.png)

    then click on next button

    then in Specify Stack Details section 
    enter the name for your stack

    and just below the stack name filed will be parametes section where youll be able to see the parameters which we have defined in our YAML
    review those parameters and click on Next button
    ![CloudFormation Stack Name & Parameters](/cloudFormation/cloudFormationVPCTask/img/cloudFormationSpecifyTask.png)
    then scroll down till the end dont change anything from this section named Configure stack options
    and click on Next Button there
    then review all the configuration we did and click on Submit Button
    then the stack will get start to creating
    ![CloudFormation Stack Submitted](/cloudFormation/cloudFormationVPCTask/img/cloudFormationStackSubmitted.png)

    Keep refreshing the Stack's Event section to know if any error is there or not because of error there will be rollback

    also we have written the yaml template which creates an AWS environment consisting of a Virtual Private Cloud (VPC) with a public and private subnet. It provisions two EC2 instances: one in the public subnet (Ubuntu) and one in the private subnet (CentOS). An Internet Gateway allows external access to the public instance, while a NAT Gateway enables the private instance to access the internet securely.

    The template also sets up security groups to control access to the instances and includes an Amazon SNS topic for notifications. If both EC2 instances are launched successfully and do not fail within the first 5 minutes, a CloudWatch Alarm triggers, sending a notification to the specified email address.


    meanwhile while the processing is going on youll receive email from sns subscription confirmation confirm it 
    ![CloudFormation SNS Subscription Confimation Email](/cloudFormation/cloudFormationVPCTask/img/SNSConfirmationEmail.png)
    ![CloudFormation SNS Subscription Confimed](/cloudFormation/cloudFormationVPCTask/img/cloudFormationSubscriptionConfirmed.png)

    then go back to the cloudformation stack 
    wait for the stack to return the Create Complete Status
    ![CloudFormation Status - Completed](/cloudFormation/cloudFormationVPCTask/img/cloudFormationStatusCreateComplete.png)

    then go to cloudwatch alarms and check the state of the alarms
    wait until the alarm state changes to OK which will comes will send email to the specified email address

    ![CloudWatch State - OK](/cloudFormation/cloudFormationVPCTask/img/cloudWatchAlarmsOK.png)
    GO TO YOUR email inbox and check youll be see that you have received 2 email 
    where one will be sent when the private instance has been launched succssfully

    ![E-Mail Private Instance Launch Success](/cloudFormation/cloudFormationVPCTask/img/email-CloudFormationPrivate.png)
    another mail will be received when the public instance has been launched too specifying both the instances have been launched successfully
    ![E-Mail Public Instance Launch Success](/cloudFormation/cloudFormationVPCTask/img/email-CloudFomrationPublicInstance.png)

    and there now we know we have launched the ec2 instance on each private and public subnet of vpc successfully and received email on our specified email