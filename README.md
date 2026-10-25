☁️ AWS VPC Peering Project Report

[![Project](https# ☁️ AWS VPC Peering Project: Private Cross-VPC Connectivity

Connecting two Virtual Private Clouds (VPCs) via a VPC peering connection for private IPv4 communication without using the public internet[cite: 1, 3].

📄 Project Overview
Programme: EWB Courses, AWS Architect Associate (Project 01, Oct 2026)[cite: 1]

Region: Asia Pacific (Sydney), ap-southeast-2[cite: 1]

Completed Date: 18 October 2026[cite: 1]

Key Components: 2 VPCs, 3 subnets, 1 active peering connection, 2 t3.micro EC2 instances[cite: 1, 2]

🎯 Objective
The goal of this project was to connect two separate AWS Virtual Private Clouds (VPCs) with a VPC peering connection so that workloads in each VPC can talk to each other over private IPv4 addresses, without using the public internet[cite: 3].

What this project demonstrates:

Planning non-overlapping VPC and subnet CIDR ranges[cite: 3].

Creating public and private subnets with explicit route table associations[cite: 3].

Requesting, accepting, and activating a peering connection, then adding routes on both sides[cite: 3].

Launching EC2 instances in two VPCs and verifying private connectivity[cite: 3].

📐 Architecture & Address Plan
VPC A (10.10.0.0/16) holds a public and a private subnet and is the only VPC with an internet gateway[cite: 3]. VPC B (10.20.0.0/16) has a single private subnet and no internet gateway[cite: 3]. A peering connection links the two VPCs, so the private subnets reach each other directly using private IP addresses[cite: 3].

Plaintext
               Internet gateway: ewb-igw-a
                        to the Internet
                              │
                    VPC A (ewb-vpc-a)
                     10.10.0.0/16
             ┌───────────────────────────┐
             │ Public subnet             │
             │ ewb-a-public (10.10.1.0/24)│
             │ 0.0.0.0/0 -> IGW          │
             │ 10.20.0.0/16 -> pcx       │
             ├───────────────────────────┤
             │ Private subnet            │
             │ ewb-a-private (10.10.2.0/24)
             │ 10.20.0.0/16 -> pcx       │
             └─────────────┬─────────────> PEERING
                           │              ewb-a-b-peer
             EC2: ewb-ec2-a               (pcx-0ee8e4be0ede9ba99)
                           │                        │
───────────────────────────┼────────────────────────┼──────────────────────────
                           │                        ▼
                           │             VPC B (ewb-vpc-b)
                           │              10.20.0.0/16 (No IGW)
                           │             ┌───────────────────────────┐
                           └────────────►│ Private subnet            │
                                         │ ewb-b-private (10.20.1.0/24)
                                         │ 10.10.0.0/16 -> pcx       │
                                         └─────────────┬─────────────┘
                                                       │
                                                 EC2: ewb-ec2-b
Note: Peering does not give VPC B internet access through VPC A[cite: 3].

📌 Address Table
Resource (Name tag)	Placement	IPv4 CIDR
ewb-vpc-a	VPC A	10.10.0.0/16[cite: 3]
ewb-a-public	VPC A, public subnet	10.10.1.0/24[cite: 3]
ewb-a-private	VPC A, private subnet	10.10.2.0/24[cite: 3]
ewb-vpc-b	VPC B	10.20.0.0/16[cite: 3]
ewb-b-private	VPC B, private subnet	10.20.1.0/24[cite: 3]
📊 Resource Inventory
Resource	Name	ID / Detail
VPC A	ewb-vpc-a	vpc-0e13b1275c4404f86 (10.10.0.0/16)[cite: 4]
VPC B	ewb-vpc-b	vpc-0b570eaf09596cbfa (10.20.0.0/16)[cite: 4]
Subnet	ewb-a-public	subnet-093184ce1cf20194c (10.10.1.0/24)[cite: 4]
Subnet	ewb-a-private	subnet-0c3c48d885ebe... (10.10.2.0/24)[cite: 4]
Subnet	ewb-b-private	subnet-09aac6debd58277e4 (10.20.1.0/24)[cite: 4]
Internet gateway	ewb-igw-a	igw-083541cabc1ddaac6, attached to ewb-vpc-a[cite: 4]
Route table	ewb-rt-a-public	rtb-0f1d18e9fea198201, associated with ewb-a-public[cite: 4]
Route table	ewb-rt-a-private	Created in VPC A for ewb-a-private[cite: 4]
Route table	ewb-rt-b-private	rtb-0a50e327a9e62155e, associated with ewb-b-private[cite: 4]
Peering connection	ewb-a-b-peer	pcx-0ee8e4be0ede9ba99, status Active[cite: 4]
EC2 instance	ewb-ec2-a	i-0b2fa2f3b7306a262, t3.micro, ap-southeast-2a[cite: 4]
EC2 instance	ewb-ec2-b	i-08a657c4b1065e30b, t3.micro, ap-southeast-2a[cite: 4]
🛠️ Implementation Steps
Build Order
VPC A and VPC B[cite: 4]

Subnets (3)[cite: 4]

Internet gateway[cite: 4]

Route tables[cite: 4]

Peering request[cite: 4]

Peer routes[cite: 4]

EC2 instances[cite: 4]

SG rules and tests[cite: 4]

Route Table Configurations
Route table	Destination	Target	Purpose
ewb-rt-a-private	10.10.0.0/16	local	VPC A internal traffic[cite: 10]
ewb-rt-a-private	10.20.0.0/16	pcx (ewb-a-b-peer)	Forward path from VPC A to VPC B[cite: 10]
ewb-rt-b-private	10.20.0.0/16	local	VPC B internal traffic[cite: 10]
ewb-rt-b-private	10.10.0.0/16	pcx (ewb-a-b-peer)	Return path from VPC B to VPC A[cite: 10]
ewb-rt-a-public	10.10.0.0/16	local	VPC A internal traffic[cite: 10]
ewb-rt-a-public	0.0.0.0/0	igw (ewb-igw-a)	Internet access for public subnet[cite: 10]
ewb-rt-a-public	10.20.0.0/16	pcx (ewb-a-b-peer)	Public subnet to VPC B[cite: 10]
🛡️ Security Design
Security groups act as stateful firewalls[cite: 12]. Inbound rules follow the principle of least privilege: only specific lab hosts are allowed, with nothing opened to 0.0.0.0/0[cite: 12].

Security group	Type/port	Source
ewb-sg-app-a (VPC A)	Custom ICMP IPv4, Echo request	Private IP of instance in VPC B (/32)[cite: 12]
ewb-sg-app-a (VPC A)	SSH/TCP 22	Private IP of administration host (/32)[cite: 12]
ewb-sg-app-b (VPC B)	Custom ICMP IPv4, Echo request	Private IP of instance in VPC A (/32)[cite: 12]
ewb-sg-app-b (VPC B)	SSH/TCP 22	Private IP of administration host (/32)[cite: 12]
Layers a Packet Must Pass
Route table: Is there a route?[cite: 12]

Network ACL: Subnet level, stateless (Default NACL allows all)[cite: 12].

Security group: Instance level, stateful (Inbound denied by default)[cite: 12].

Instance OS: Host firewall and service (e.g., sshd must be running)[cite: 12].

🧪 Testing and Verification
Test	Where/command	Expected result
01 Baseline	ewb-ec2-a, before peering routes: ping -c 4 <private IP of ewb-ec2-b>	No replies[cite: 13]
02 Forward path	ewb-ec2-a, after routes: ping -c 4 <private IP of ewb-ec2-b>	4 replies, 0% packet loss[cite: 13]
03 Reverse path	ewb-ec2-b, after routes: ping -c 4 <private IP of ewb-ec2-a>	4 replies, 0% packet loss[cite: 13]
04 Private SSH	SSH to ewb-ec2-b through bastion path	Shell on ewb-ec2-b[cite: 13]
05 Isolation	EC2 instance details of both private hosts	No public IPv4 address[cite: 13]
06 Controlled failure	Remove ICMP Echo rule on ewb-sg-app-b, ping, then restore rule	Replies stop, then resume[cite: 13]
Bash
# Find private IP of local instance
hostname -I

# Tests 02 and 03
ping -c 4 <peer-private-ip>

# Test 04: SSH via Bastion Host
ssh -i <key>.pem -J ec2-user@<admin-host-ip> ec2-user@<ewb-ec2-b-private-ip>

# Test 05: Confirm no public address via AWS CLI
aws ec2 describe-instances --instance-ids i-0b2fa2f3b7306a262 i-08a657c4b1065e30b \
  --query 'Reservations[].Instances[].[InstanceId,PublicIpAddress,PrivateIpAddress]' --output table
💻 AWS CLI Equivalent Setup
Bash
# VPC A, subnets and internet gateway
export AWS_DEFAULT_REGION=ap-southeast-2
VPC_A=$(aws ec2 create-vpc --cidr-block 10.10.0.0/16 --query Vpc.VpcId --output text)
aws ec2 create-tags --resources $VPC_A --tags Key=Name,Value=ewb-vpc-a

SUB_A_PUB=$(aws ec2 create-subnet --vpc-id $VPC_A --cidr-block 10.10.1.0/24 \
  --availability-zone ap-southeast-2a --query Subnet.SubnetId --output text)
SUB_A_PRIV=$(aws ec2 create-subnet --vpc-id $VPC_A --cidr-block 10.10.2.0/24 \
  --availability-zone ap-southeast-2a --query Subnet.SubnetId --output text)

IGW=$(aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text)
aws ec2 attach-internet-gateway --internet-gateway-id $IGW --vpc-id $VPC_A

# Route tables in VPC A
RT_A_PUB=$(aws ec2 create-route-table --vpc-id $VPC_A --query RouteTable.RouteTableId --output text)
aws ec2 associate-route-table --route-table-id $RT_A_PUB --subnet-id $SUB_A_PUB
aws ec2 create-route --route-table-id $RT_A_PUB --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW

RT_A_PRIV=$(aws ec2 create-route-table --vpc-id $VPC_A --query RouteTable.RouteTableId --output text)
aws ec2 associate-route-table --route-table-id $RT_A_PRIV --subnet-id $SUB_A_PRIV

# VPC B, subnet and route table
VPC_B=$(aws ec2 create-vpc --cidr-block 10.20.0.0/16 --query Vpc.VpcId --output text)
aws ec2 create-tags --resources $VPC_B --tags Key=Name,Value=ewb-vpc-b

SUB_B=$(aws ec2 create-subnet --vpc-id $VPC_B --cidr-block 10.20.1.0/24 \
  --availability-zone ap-southeast-2a --query Subnet.SubnetId --output text)
RT_B=$(aws ec2 create-route-table --vpc-id $VPC_B --query RouteTable.RouteTableId --output text)
aws ec2 associate-route-table --route-table-id $RT_B --subnet-id $SUB_B

# Peering connection and routes
PCX=$(aws ec2 create-vpc-peering-connection --vpc-id $VPC_A --peer-vpc-id $VPC_B \
  --query VpcPeeringConnection.VpcPeeringConnectionId --output text)
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id $PCX

aws ec2 create-route --route-table-id $RT_A_PRIV --destination-cidr-block 10.20.0.0/16 \
  --vpc-peering-connection-id $PCX
aws ec2 create-route --route-table-id $RT_A_PUB --destination-cidr-block 10.20.0.0/16 \
  --vpc-peering-connection-id $PCX
aws ec2 create-route --route-table-id $RT_B --destination-cidr-block 10.10.0.0/16 \
  --vpc-peering-connection-id $PCX
🧹 Cleanup Script
Bash
aws ec2 terminate-instances --instance-ids i-0b2fa2f3b7306a262 i-08a657c4b1065e30b
# Wait until instances show "terminated"

aws ec2 delete-route --route-table-id $RT_A_PRIV --destination-cidr-block 10.20.0.0/16
aws ec2 delete-route --route-table-id $RT_A_PUB --destination-cidr-block 10.20.0.0/16
aws ec2 delete-route --route-table-id $RT_B --destination-cidr-block 10.10.0.0/16

aws ec2 delete-vpc-peering-connection --vpc-peering-connection-id pcx-0ee8e4be0ede9ba99
aws ec2 delete-security-group --group-id $SG_A
aws ec2 delete-security-group --group-id $SG_B

aws ec2 delete-subnet --subnet-id $SUB_A_PUB
aws ec2 delete-subnet --subnet-id $SUB_A_PRIV
aws ec2 delete-subnet --subnet-id $SUB_B

aws ec2 delete-route-table --route-table-id $RT_A_PUB
aws ec2 delete-route-table --route-table-id $RT_A_PRIV
aws ec2 delete-route-table --route-table-id $RT_B

aws ec2 detach-internet-gateway --internet-gateway-id $IGW --vpc-id $VPC_A
aws ec2 delete-internet-gateway --internet-gateway-id $IGW

aws ec2 delete-vpc --vpc-id $VPC_A
aws ec2 delete-vpc --vpc-id $VPC_B
🧠 Key Takeaways
Peering creates the link; routes, network ACLs, and security groups decide what crosses it[cite: 18].

Routes are needed in both directions, and the most specific route always wins[cite: 18].

Peering is non-transitive and never shares an internet gateway[cite: 18].

Test both directions and break one rule on purpose to find which layer is responsible[cite: 18].

Clean up in dependency order to avoid leftover charges[cite: 18].
