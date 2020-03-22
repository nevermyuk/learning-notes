

# Cloudformation

[Getting started with templates:](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html)

- **CloudFormation knows how to create resources in order**'
  - Based on dependencies.



```
  Parameters:
    # Parameters are entirely optional.
    # but using them will make your cloudformation templates more reusable
  Resources:
    ResourceName:
      ResourceType
      Properties: 
```

### Resources section

- Mandatory
- Declare what resources to create.

```yaml
Description: >
   Quan 
   This template desploys a VPC.
Resources:
    CloudDevOpsVPC:
      Type: AWS::EC2::VPC
      Properties: 
        CidrBlock: 10.0.0.0/16
        EnableDnsHostnames: true
    My_IAM_Roles_for_my_EC2_Web_Server:
      Type: AWS::IAM::InstanceProfile
      Properties: 
        Roles:
         - ReadOnlyS3
```



### Parameters

- Enable input custom values to template each time we create or update a stack.

- `Parameters` always declared above`Resources`

- ### Don't hard-code parameters

  - Use separate parameter file to store param values.
  - Only JSON is supported.

```yaml
Parameters:
  ParameterLogicalID:
    Type: DataType
    ParameterProperty: value
```

```yaml
 Parameters:
    EnvironmentName:
        Description: An environment name that will be prefixed to resource names
        Type: String

    VpcCIDR: 
        Description: Please enter the IP range (CIDR notation) for this VPC
        Type: String
        Default: 10.0.0.0/16 # can provide default values.
```

### Parameter File

- To input into the template with actual parameter values
- Allows dynamic data without having to modify script directly 
- Reduces chance of logical error

```json
[
    {
        "ParameterKey": "EnvironmentName",
        "ParameterValue": "UdacityProject"
    }
]
```

### Connecting VPC & Internet Gateway

- Require Additional Resource
  - InternetGatewayAttachment

```yaml
Type: AWS::EC2::VPCGatewayAttachment
Properties: 
  InternetGatewayId: String
  VpcId: String
  VpnGatewayId: String
```



### Calling CloudFormation with CLI

```bash
aws cloudformation create-stack --stack-name MyStack --template-body file://Examplescriptfile.yml  --parameters file://paramfile.json 

```



#### Adding Subnets

To specify a subnet for VPC

```yaml
Type: AWS::EC2::Subnet
Properties: 
  AssignIpv6AddressOnCreation: Boolean
  AvailabilityZone: String
  CidrBlock: String
  Ipv6CidrBlock: String
  MapPublicIpOnLaunch: Boolean
  Tags: 
    - Tag
  VpcId: String
```

```yaml
    PrivateSubnet1: 
        Type: AWS::EC2::Subnet
        Properties:
            VpcId: !Ref VPC
            AvailabilityZone: !Select [ 0, !GetAZs '' ]
            CidrBlock: !Ref PrivateSubnet1CIDR
            MapPublicIpOnLaunch: false
            Tags: 
                - Key: Name 
                  Value: !Sub ${EnvironmentName} Private Subnet (AZ1)
  PrivateSubnet2: 
        Type: AWS::EC2::Subnet
        Properties:
            VpcId: !Ref VPC
            AvailabilityZone: !Select [ 1, !GetAZs '' ]
            CidrBlock: !Ref PrivateSubnet2CIDR
            MapPublicIpOnLaunch: false
            Tags: 
                - Key: Name 
                  Value: !Sub ${EnvironmentName} Private Subnet (AZ2)
```

**Note: Name subnet with tags to keep track of subnets created.**

### Index from GetAZ

GetAZ - returns a list of availability zones, which are indexed 0, 1 and more.

```yaml
PrivateSubnet1:
	AvailabilityZone: !Select [ 0, !GetAZ's '' ] 

PrivateSubnet2: 
	AvailabilityZone: !Select [ 1, !GetAZ's '' ]
```

-----

### NAT Gateway

```yaml
Type: AWS::EC2::NatGateway
Properties: 
  AllocationId: String
  SubnetId: String
  Tags: 
    - Tag
```

```yaml
 NatGateway1EIP:
        Type: AWS::EC2::EIP
        DependsOn: InternetGatewayAttachment
        Properties: 
            Domain: vpc
 NatGateway1: 
        Type: AWS::EC2::NatGateway
        Properties: 
            AllocationId: !GetAtt NatGateway1EIP.AllocationId
            SubnetId: !Ref PublicSubnet1

```

- EIP : ELASTIC IP
- Provides fixed IP.

- **Note** : Depends on specify dependencies for CloudFormation to take note of.

-----

### Route Table

```yaml
Type: AWS::EC2::RouteTable
Properties: 
  Tags: 
    - Tag
  VpcId: String
```



```yamk
PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties: 
            VpcId: !Ref VPC
            Tags: 
                - Key: Name 
                  Value: !Sub ${EnvironmentName} Public Routes
```

-----

### Route

```yaml
Type: AWS::EC2::Route
Properties: 
  DestinationCidrBlock: String
  DestinationIpv6CidrBlock: String
  EgressOnlyInternetGatewayId: String
  GatewayId: String
  InstanceId: String
  NatGatewayId: String
  NetworkInterfaceId: String
  RouteTableId: String
  VpcPeeringConnectionId: String
```

```yaml
DefaultPublicRoute: 
        Type: AWS::EC2::Route
        DependsOn: InternetGatewayAttachment
        Properties: 
            RouteTableId: !Ref PublicRouteTable
            DestinationCidrBlock: 0.0.0.0/0
            GatewayId: !Ref InternetGateway
```

-----

### Route Table Association With Subnets

```yaml
Type: AWS::EC2::SubnetRouteTableAssociation
Properties: 
  RouteTableId: String
  SubnetId: String
```

```yaml
PublicSubnet1RouteTableAssociation:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
            RouteTableId: !Ref PublicRouteTable
            SubnetId: !Ref PublicSubnet1
```

- Specify ID for Route Table and Subnet



**Important: ** `Routes` to be defined  with the most specific rule first before adding the least specific rule.

-----

### Output

Optional but are very useful if there are output values that you need to :

- import into another stack
- return in a response
- view in AWS console

```
Outputs:
  Logical ID:
    Description: Information about the value
    Value: Value to return
    Export:
      Name: Value to export
```

**Value is required. ** 

Name is Optional



```yaml
VPC: 
        Description: A reference to the created VPC
        Value: !Ref VPC
        Export:
          Name: !Sub ${EnvironmentName}-VPCID
          
  # Return ID of VPC and Environment Name
```





#### Join Function

------

You can use the `join` function to combine a group of `values`. The syntax requires you provide a `delimiter` and a list of values you want appended.

`Join` function syntax:

```
Fn::Join: [ delimiter, [ comma-delimited list of values ] ]
```

In the following example we are using `!Join` to combine our subnets before returning their values:

```
PublicSubnets:
        Description: A list of the public subnets
        Value: !Join [ ",", [ !Ref PublicSubnet1, !Ref PublicSubnet2 ]]
        Export:
          Name: !Sub ${EnvironmentName}-PUB-NETS
```



#### !Sub

- Substitute
- Replace  variable with new values



------



## Security Groups

```yaml
Type: AWS::EC2::SecurityGroup
Properties: 
  GroupDescription: String
  GroupName: String
  SecurityGroupEgress: 
    - Egress
  SecurityGroupIngress: 
    - Ingress
  Tags: 
    - Tag
  VpcId: String
```



#### Ingress rules and egress rules

------

- Ingress rules -  inbound traffic
  - restrict or allow traffic trying to reach our resources on specific ports.

- Egress rules  - outbound traffic.
  - restrict or allow traffic originating from our server -- usually outbound traffic is allowed  without restrictions as this doesn’t pose a risk for a security breach.





##### Limit inbound traffic for security

------

- Any Traffic is blocked by default
- For ingress rules, limit inbound traffic for security.
  - Open only a single port or ports required by our application.
- Public-facing web servers, will require port 80 open to the world @ 0.0.0.0/0
- If you require SSH, **LIMIT PORT 22 TO YOUR OWN IP**

##### For outbound traffic

------

- For egress rules, give our resource full access to the internet
  - Give egress access to all ports from 0 to 65535
  - Allow our servers to access the internet for updates or fetching of data. 



## Bastion Hosts / Jump Box

**Best Practices!**

- Use IP-based restriction for server
- Turn off when unused
- Name SSH keys after purpose
- Always apply security group [restriction](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules-reference.html)



## UserData Script

A series of commands that you use to properly configure your server to run your application.

This is where you do things such as:

- Fetch credentials
- Set Environment Variables ( ENV=PROD, for example )
- Download and Install libraries
- Get your source files or binaries from a storage location, such as S3

##### When to use it?

- If running app from base Linux or Windows Servers , use Userdata script to configure server.
- **Not required if using Virtual Machine Image(AMI)** as everything is pre-installed

##### **Verification and troubleshooting**

- The Best way to create and verify a UserData script.
  -  Run each command manually and verify everything works as expected. 
  - If it fails, login to the server and check the logs that can be found in
    -  For Linux: `/var/log/cloud-init-output.log`. 
    - For Windows: `C:\ProgramData\Amazon\EC2-Windows\Launch\Log\UserdataExecution.log`

#### Difference between UserData on Windows and Linux

On Windows,  Option for Powershell and Bash.

```powershell
<powershell>
$file = $env:SystemRoot + "\Temp\" + (Get-Date).ToString("MM-dd-yy-hh-mm")
New-Item $file -ItemType file
</powershell>
```

On Linux, Bash.

```bash
# bash
<script>
echo Current date and time >> %SystemRoot%\Temp\test.log
echo %DATE% %TIME% >> %SystemRoot%\Temp\test.log
</script>
```



------

## Auto Scaling Concepts

#### 1. Scaling Policy

- Decide when to Add or Remove Servers from ASG.
- Best to have criteria to turn off server when not needed, and turn on when in demand.

For example, Create CloudWatch Alarm with a custom metric that collects web traffic for the past 2 hours.

If less than 100 requests, if a single server is sufficient to handle the load.

**Scale Down  Event will be triggered** if there is more than one server running at the time.

#### 2. [Launch Configuration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-as-launchconfig.html)

- Template
- Tells ASG How to run web app.

For example: My application requires 2GB RAM , 4 vCPUs, 5GB of Disk Space, NodeJS

```
Type: AWS::AutoScaling::LaunchConfiguration
Properties: 
  AssociatePublicIpAddress: Boolean
  BlockDeviceMappings: 
    - BlockDeviceMapping
  ClassicLinkVPCId: String
  ClassicLinkVPCSecurityGroups: 
    - String
  EbsOptimized: Boolean
  IamInstanceProfile: String
  ImageId: String
  InstanceId: String
  InstanceMonitoring: Boolean
  InstanceType: String
  KernelId: String
  KeyName: String
  LaunchConfigurationName: String
  PlacementTenancy: String
  RamDiskId: String
  SecurityGroups: 
    - String
  SpotPrice: String
  UserData: String
```

- Required properties : `ImageId` and `Instance Type`  .

Example : 

```yaml
WebAppLaunchConfig:
    Type: AWS::AutoScaling::LaunchConfiguration
    Properties:
      UserData:
        Fn::Base64: !Sub |
         #!/bin/bash
        apt-get update -y
        apt-get install apache2 -y
        systemctl start apache2.service
        cd /var/www/html
        echo "Demo server up and RUNNING!" > index.html

      ImageId: ami-005bdb005fb00e791
      IamInstanceProfile: !Ref ProfileWithRolesForOurApp
      SecurityGroups:
      - Ref: WebServerSecGroup
      InstanceType: t3.small
      BlockDeviceMappings:
      - DeviceName: "/dev/sdk"
        Ebs:
          VolumeSize: '10'
```

- Specified 10gbs for our `VolumeSize`.
- Referenced the previously defined `WebServerSecGroup` for our `SecurityGroup`
- Set our `InstanceType` to `t3.medium` for our `EC2 Instance`

#### 3. Load Balancer

- Single point of entry for users.
- Distribute traffic to any amount of servers.
- Allows ASG to scale server down to 1 at low demand and increase as demands increases.
- The user doesn't experience any difference when visiting the site.

##### Relationship between Target Groups and Auto Scaling groups.

- Load Balancer is a server that forward traffic evenly across a group of servers, known as a Target Group.

##### How do we provide IP to the servers if they are part of ASG?

- Use `TargetGroupARNs` property of ASG .
- Automatically associate any new servers and remove discarded servers from the Target group.
- Include the ARN of Load Balancer’s target group in this property of ASG
- LB will always know where to send traffic.

The following is the required syntax for `Load Balancer` and `Listener`

```yaml
 WebAppLB:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Subnets:
      - Fn::ImportValue: !Sub "${EnvironmentName}-PUB1-SN"
      - Fn::ImportValue: !Sub "${EnvironmentName}-PUB2-SN"
      SecurityGroups:
      - Ref: LBSecGroup
  Listener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      DefaultActions:
      - Type: forward
        TargetGroupArn:
          Ref: WebAppTargetGroup
      LoadBalancerArn:
        Ref: WebAppLB
      Port: '80'
      Protocol: HTTP
  ALBListenerRule:
      Type: AWS::ElasticLoadBalancingV2::ListenerRule
      Properties:
        Actions:
        - Type: forward
          TargetGroupArn: !Ref 'WebAppTargetGroup'
        Conditions:
        - Field: path-pattern
          Values: [/]
        ListenerArn: !Ref 'Listener'
        Priority: 1
```

**Syntax for Target Group**

```
Type: AWS::ElasticLoadBalancingV2::TargetGroup
Properties: 
  HealthCheckEnabled: Boolean
  HealthCheckIntervalSeconds: Integer
  HealthCheckPath: String
  HealthCheckPort: String
  HealthCheckProtocol: String
  HealthCheckTimeoutSeconds: Integer
  HealthyThresholdCount: Integer
  Matcher: 
    Matcher
  Name: String
  Port: Integer
  Protocol: String
  Tags: 
    - Tag
  TargetGroupAttributes: 
    - TargetGroupAttribute
  TargetType: String
  Targets: 
    - TargetDescription
  UnhealthyThresholdCount: Integer
  VpcId: String
```





`Health Checks` are the requests your `Application Load Balancer`sends to its registered targets. These periodic requests test the status of these targets. You can see us defining our `Health Check` properties in the example below:

```
 WebAppTargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      HealthCheckIntervalSeconds: 35
      HealthCheckPath: /
      HealthCheckProtocol: HTTP
      HealthCheckTimeoutSeconds: 30
      HealthyThresholdCount: 2
      Port: 80
      Protocol: HTTP
      UnhealthyThresholdCount: 5
      VpcId: 
        Fn::ImportValue:
          Fn::Sub: "${EnvironmentName}-VPCID"
```

**In the above example we specify the following:**

- The port where our targets receive traffic - `Port: 80`
- The protocol the load balancer uses when performing health checks on targets - `HealthCheckProtocol: HTTP`
- The time it takes to determine a non-responsive target is unhealthy - `HealthCheckIntervalSeconds: 35`
- The number of healthy/unhealthy checks required to change the health status - `HealthyThresholdCount: 2` `UnhealthyThresholdCount: 5`





#### Exercise

- Storing Parameter in SSM

##### Storing 

```bash
aws ssm put-parameter --name "DBURL" --value "exampledb.com" --type String --overwrite
```

##### Retrieving

```bash
aws ssm get-parameter --name "DBURL"
```

- JSON will be returned.
- To retrieve using EC2 Instance, attach a IAM role with SSM permissions



## [Relational Database Service](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-rds-database-instance.html)(RDS)

```yaml
Type: AWS::RDS::DBInstance
Properties: 
  AllocatedStorage: String
  AllowMajorVersionUpgrade: Boolean
  AssociatedRoles: 
    - DBInstanceRole
  AutoMinorVersionUpgrade: Boolean
  AvailabilityZone: String
  BackupRetentionPeriod: Integer
  CACertificateIdentifier: String
  CharacterSetName: String
  CopyTagsToSnapshot: Boolean
  DBClusterIdentifier: String
  DBInstanceClass: String
  DBInstanceIdentifier: String
  DBName: String
  DBParameterGroupName: String
  DBSecurityGroups: 
    - String
  DBSnapshotIdentifier: String
  DBSubnetGroupName: String
  DeleteAutomatedBackups: Boolean
  DeletionProtection: Boolean
  Domain: String
  DomainIAMRoleName: String
  EnableCloudwatchLogsExports: 
    - String
  EnableIAMDatabaseAuthentication: Boolean
  EnablePerformanceInsights: Boolean
  Engine: String
  EngineVersion: String
  Iops: Integer
  KmsKeyId: String
  LicenseModel: String
  MasterUsername: String
  MasterUserPassword: String
  MaxAllocatedStorage: Integer
  MonitoringInterval: Integer
  MonitoringRoleArn: String
  MultiAZ: Boolean
  OptionGroupName: String
  PerformanceInsightsKMSKeyId: String
  PerformanceInsightsRetentionPeriod: Integer
  Port: String
  PreferredBackupWindow: String
  PreferredMaintenanceWindow: String
  ProcessorFeatures: 
    - ProcessorFeature
  PromotionTier: Integer
  PubliclyAccessible: Boolean
  SourceDBInstanceIdentifier: String
  SourceRegion: String
  StorageEncrypted: Boolean
  StorageType: String
  Tags: 
    - Tag
  Timezone: String
  UseDefaultProcessorFeatures: Boolean
  VPCSecurityGroups: 
    - String
```



## S3

- Object store

- Must have DNS compliant name

- CLI

- Used to store config/media or log file since ASG will not store files.

  ```bash
  aws s3 ls <link to S3 bucket>
  aws s3 ls s3://bucketname
  aws s3 cp examplefile.txt s3://bucketname
  
  # set bucket policy
  aws s3api put-bucket-policy --bucket bucketName --policy file://ipbucket.json
  #view bucket policy
  aws s3api get-bucket-policy --bucket bucketName
  
  ```

  ```json
  #ipbucket.json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "IPAllow",
              "Effect": "Allow",
              "Principal": "*",
              "Action": "s3:*",
              "Resource": "arn:aws:s3:::testBucket/*",
              "Condition": {
                  "IpAddress": {
                      "aws:SourceIp": "YourIPAddress/32"
                  },
                  "NotIpAddress": {
                      "aws:SourceIp": "IntruderIP/32"
                  }
              }
          }
      ]
  }
  ```

  

#### Best Practice

- Keep S3 as private as possible.
- if sharing files publicly, use pre-signed URLs that will expire.
- if web app receives incoming files, such as PDFs or Photos, save to local temporary storage before moving the data to S3 . Temporary storage is faster.
- When creating IAM Roles,limit access to a specific bucket and not to all buckets.





## Exercise Questions

### Infrastructure Diagram

Review the AWS Reference Architecture Page

##### Visit the [AWS Reference Architecture Page](https://aws.amazon.com/architecture/) and study some of the diagrams. As a case-study, pay particular attention to [WordPress Architecture](https://github.com/aws-samples/aws-refarch-wordpress), which is the CMS software that runs on nearly 60% of all sites on the internet.WordPress is a great example to help you understand how auto-scaling servers keep track of non-database data, such as images, config files, and similar objects, in this particular case by means of external storage (EFS) mounted to the EC2 Servers -- much like the way you would have your personal files and photos in an external USB drive that you can connect to any computer when you need to access them.

Task List

- Go to https://aws.amazon.com/architecture/
- Review Software Architecture Diagrams
- Study the WordPress Architecture in particular as a case-study, which you can find here: https://github.com/aws-samples/aws-refarch-wordpress



Create a diagram to represent a corporate-only cloud

##### There is one more real-world scenario that is very popular, but not covered in this course. And that is the “extension of the on-premises network”.In this case, you’d have a network that only contains private subnets, and does not have NAT Gateways. These components get replaced by a VGW (Virtual Gateway) and a VPN Connection. You’ll also need a CGW (Customer Gateway), which represents the on-premises side of the VPN Connection.

### Networking

Create an "extension of the on-premises network"

##### Using the provided CloudFormation script to deploy a private corporate cloud.You’ll need to remove any components not needed (such as public subnets and NAT Gateways). There is no need to connect the VPN to anything, but go ahead and include it in your code.

Task List

- Modify the provided CloudFormation script
- Deploy it using the CLI Tool

### Servers and Security Group

Research the EC2 Parameter Store

##### One way to avoid hard-coding ever-changing values into your server infrastructure is by using parameters. Examples of parameters that frequently change include database connection details, and sensitive data, such as passwords and secret API access keys.Using the AWS CLI Tool, store and retrieve parameters from the EC2 Parameter Store. Play around with changing the value from the EC2 Console and not just the CLI Tool.It’s critical that you practice this, as this is not only a best practice but also a common practice.

#### Storage and Databases

### Deploy a MySQL database associated with a security group

##### Create a CloudFormation script that deploys a MySQL DB with an associated security group.

Task List

- On the EC2 Console, create a security group that you'll associate with EC2 instances and RDS
- Using CloudFormation, create a script that uses your security group and creates an RDS MySQL DB

### Get familiar with Bucket policies

Task List

- In the S3 Console, create a bucket and enable static web hosting
- create an index.html and upload it to your bucket
- using the AWS CLI Tool and your text editor, create a bucket policy to enable public access to it ( hint: Use the AWS Documentation )
- update the policy to restrict access only to your own IP address





## Solution Answers

https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution







## Supporting Materials

https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1

#### Reading Materials

[Accompanying Material](https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1/tree/master/supporting_material)

[Intro to architecturing as scale](https://lethain.com/introduction-to-architecting-systems-for-scale/)

[AWS Reference Architecture Page](https://aws.amazon.com/architecture/)

**Load Balancers**

[CLB](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html)

[ALB](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

[NLB](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

**VPC and Internet Gateway**

[CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

[VPC and Subnets in AWS](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)

[Route Table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

[Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)

[VPC](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html)

[VPCCidrBlock](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpccidrblock.html)

[Internet Gateway](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html)

**Subnet and NAT Gateway**

[Subnet Resource Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html)

[DependsOn Attribute](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-dependson.html)

[Creating NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating)

[NAT Gateway Resource Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-natgateway.html)

**Routing**

[Route Tables Overview](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

[Route Table Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route-table.html)

[Route Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html)

[Subnet Route Table Association Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet-route-table-assoc.html)

**Outputs**

[Outputs Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html)

[Join Function](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-join.html)

[Substitutes](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html)

**Load Balancing**

[ASG FAQ](https://aws.amazon.com/autoscaling/faqs/) 

[Launch Config](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchConfiguration.html)

[TargetGroups Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-targetgroup.html)

[Health Checks for Your TargetGroups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

