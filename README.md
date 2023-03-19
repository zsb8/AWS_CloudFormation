# AWS_CloudFormation
Use YAML to create EC2 Ubuntu instance 
# Create VPC
```
Resources:
  MyTestVpc:
    Type: "AWS::EC2::VPC"
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
```

# Create IGW and connect to VPC
```
Resources:
  MyTestIgw:
    Type: "AWS::EC2::InternetGateway"
  MyTestAttachIgw:
    Type: "AWS::EC2::VPCGatewayAttachment"
    Properties:
      VpcId: !Ref MyTestVpc
      InternetGatewayId: !Ref MyTestIgw
```

# Create subnet
```
Resources:
  MyTestVpcSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyTestVpc
      CidrBlock: 10.0.0.0/24
      AvailabilityZone: us-east-2a
```
# Create Route table and connect to subnet
```
Resources:
  MyTestPublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyTestVpc
  MyTestRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref MyTestPublicRouteTable
      SubnetId: !Ref MyTestVpcSubnet
  MyTestInternetRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyTestPublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref MyTestIgw
```

# Create Security Group
```
Resources:
  MyTestVpcSg:
    Type: AWS::EC2::SecurityGroup
    DependsOn: MyTestVpc
    Properties:
      GroupDescription: SG to test ping by zsb
      VpcId: !Ref MyTestVpc
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 0
          ToPort: 65535
          CidrIp: 0.0.0.0/0
        - IpProtocol: icmp
          FromPort: 8
          ToPort: -1
          CidrIp: 0.0.0.0/0
        - IpProtocol: udp
          FromPort: 0
          ToPort: 65535
          CidrIp: 0.0.0.0/0
```
 # Create EC2 Ubuntu Instance
 ```
 Resources:
  MyTestEc2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-00eeedc4036573771
      KeyName: pemzsb
      InstanceType: t2.micro
      NetworkInterfaces:
        - DeviceIndex: "0"
          AssociatePublicIpAddress: true
          GroupSet:
            - !Ref MyTestVpcSg
          SubnetId: !Ref MyTestVpcSubnet
 ```

# Run this YMAL
![image](https://user-images.githubusercontent.com/75282285/226187790-1f95aaa3-77fc-4b93-a51a-ba69664bd016.png)
![image](https://user-images.githubusercontent.com/75282285/226187760-8d3b7cbf-d728-4870-b8fb-5e2efae49326.png)

# Test it
![image](https://user-images.githubusercontent.com/75282285/226188651-26097929-8ffd-4576-8656-c70ac0f3bcbf.png)

![image](https://user-images.githubusercontent.com/75282285/226187841-1d303ec2-c42a-4146-909d-901a3b90f702.png)






