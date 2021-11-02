# SAA-C02 Notes
> These are my personal notes from Adrian Cantrill's (SAA-C02) course.
Learning Aids from
[aws-sa-associate-saac02](https://github.com/acantril/aws-sa-associate-saac02).
There may be errors, so please purchase his course to get the original content
and show support <https://learn.cantrill.io.>

## Table of Contents

- [Cloud-Computing-Fundamentals](#Cloud-Computing-Fundamentals)
- [AWS-Fundamentals](#AWS-Fundamentals)
- [IAM-Accounts-AWS-Organizations](#IAM-Accounts-AWS-Organizations)
- [Simple-Storage-Service-S3](#Simple-Storage-Service-S3)
- [Virtual-Private-Cloud-VPC](#Virtual-Private-Cloud-VPC)
- [Elastic-Cloud-Compute-EC2](#Elastic-Cloud-Compute-EC2)
- [Containers-and-ECS](#Containers-and-ECS)
- [Advanced-EC2](#Advanced-EC2)
- [Route-53](#Route-53)
- [Relational-Database-Service-RDS](#Relational-Database-Service-RDS)
- [Network-Storage-EFS](#Network-Storage-EFS)
- [HA-and-Scaling](#HA-and-Scaling)
- [Serverless-and-App-Services](#Serverless-and-App-Services)
- [CDN-and-Optimization](#CDN-and-Optimization)
- [Advanced-VPC](#Advanced-VPC)
- [Hybrid-and-Migration](#Hybrid-and-Migration)
- [Security-Deployment-Operations](#Security-Deployment-Operations)
- [NoSQL-and-DynamoDB](#NoSQL-and-DynamoDB)

---

## Cloud-Computing-Fundamentals

Cloud computing provides

1. On-Demand Self-Service: Provision and terminate using a UI/CLI without
human interaction.
2. Broad Network Access: Access services over any networks on any devices using
standard protocols and methods.
3. Resource Pooling: Economies of scale, cheaper service.
4. Rapid Elasticity: Scale up and down automatically in response to system load.
5. Measured Service: Usage is measured. Pay only for what you consume.

### Public vs Private vs Multi Cloud

- Public Cloud: using 1 public cloud such as AWS, Azure, Google Cloud.
- Private Cloud: using on-premises real cloud. Must meet 5 requirements.
- Multi-Cloud: using more than 1 public cloud in one deployment.
- Hybrid Cloud: using public and private clouds in one environment
  - This is **NOT** using Public Cloud and Legacy on-premises hardware.

### Cloud Service Models

The *Infrastructure Stack* or *Application Stack* contains multiple components
that make up the total service. There are parts that **you** manage as well
as portions the **vendor** manages. The portions the vendor manages and you
are charged for is the **unit of consumption**

1. On-Premises: The individual manages all components from data to facilities.
Provides the most flexibility, but also most IT intensive.
2. Data Center Hosting: Place equipment in a building managed by a vendor.
You pay for the facilities only.
3. Infrastructure as a Service (IaaS): Vendor manages facilities and everything
else related to servers up to the OS. You pay per second or minute for the OS
used to the vendor. Lose some flexibility, but big risk reductions.
4. Platform as a Service (PaaS): Good for running an application only. The
unit of consumption is the runtime environment. You manage the application
and the data, but the vendor manges all else.
5. Software as a Service (SaaS): You consume the software as a service. This
can be Outlook or Netflix. There are almost no risks or additional costs, but
very little control.

There are additional services such as *Function as a Service*,
*Container as a Service*, and *DataBase as a Service* which be explained later.

---

## AWS-Fundamentals

### AWS Support Plans

- Basic (free)
- Developer (one user, general guidance)
- Business (multiple users, personal guidance)
- Enterprise (Technical account manager)

### Public vs Private Services

Refers to the networking only, not permissions.

- Public Internet: AWS is a public cloud platform and connected to the public
internet. It is not on the public internet, but is next to it.
- AWS Public Zone: Attached to the Public Internet.
S3 Bucket is hosted in the Public Zone, not all services are.
Just because you connect to a public service,
that does not mean you have permissions to access it.
- AWS Private Zone: No direct connectivity is allowed between the AWS Private
Zone and the public cloud unless this is configured for that service.
This is done by taking a part of the private service and projecting it into the
AWS public zone which allows public internet to make inbound or outbound
connections.

### [AWS Global Infrastructure](https://www.infrastructure.aws/)

#### Regions

AWS Region is an area of the world they have selected for a full deployment of
AWS infrastructure.

Areas such as countries or states

- Ohio
- California
- Singapore
- Beijing
- London
- Paris

AWS can only deploy regions as fast as their planning allows.
Regions are often not near their customers.

#### AWS Edge Locations

Local distribution points. Useful for services such as Netflix so they can store
data closer to customers for low latency high speed transfers.

If a customer wants to access data stored in Brisbane, they will stream data
from the Sydney Region through an Edge Location hosted in Brisbane.

#### AWS Management

Regions are connected together with high speed networking.
Some services such as EC2 need to be selected in a region.
Some services are global such as IAM

#### Region's 3 Benefits

- Geographical Separation
  - Useful for natural disasters
  - Provide isolated fault domain
  - Regions are 100% isolated
- Geopolitical Separation
  - Different laws change how things are accessed
  - Stability from political events
- Location Control
  - Tune architecture for performance
  - Duplicate infrastructure at closer points to customers

#### Regions and AZs

Region Name: Asia Pacific (Sydney)
Region Code: ap-southeast-2

AWS will provide between 2 and 6 AZs per region.
AZs are isolated compute, storage, networking, power, and facilities.
Components are allowed to distribute load and resilience by using multiple zones.

AZs are connected to each other with high speed redundant networks.

#### Service Resilience

1. Globally Resilient: IAM or Route 53. No way for them to go down. Data is
replicated throughout multiple regions.
2. Region Resilient: Operate as separate services in each region. Generally
replicate data to multiple AZs in that region.
3. AZ Resilient: Run from a single AZ. It is possible for hardware to fail in an
AZ and the service to keep running because of redundant equipment, but should
not be relied on.

### AWS Default VPC

VPC is a virtual network inside of AWS.
A VPC is within 1 account and 1 region which makes it regionally resilient.
A VPC is private and isolated until decided otherwise.

One default VPC per region. Can have many custom VPCs which are all private
by default.

Exam facts:
- regions can only have 1 default VPC and many custom VPCs
- custom VPCs allow flexible network configuration, the default VPC has a fixed scheme
- there's no limit to how many services use the default VPC
- some services can behave oddly if the default VPC doesn't exist (better to not remove it)
- default VPCs can be recreated

#### Default VPC Facts

VPC CIDR - defines start and end ranges of the VPC.
IP CIDR of a default VPC is always: **172.31.0.0/16**

Configured to have one subnet in each AZ in the region by default.

Subnets are given one section of the IP ranges for the default service.
In general do not use the Default VPC in a region because it is not flexible.

Default VPC is large because it uses the /16 range.
A subnet is smaller such as /20
The higher the / number is, the smaller the grouping.

Two /17's will fit into a /16, sixteen /20 subnets can fit into one /16.

### Elastic Compute Cloud (EC2)

Default compute service. Provides access to virtual machines called instances.

**IaaS** - Infrastructure as as Service

The unit of consumption is an instance
EC2 instance is configured to launch into a single VPC subnet.
Private service by default, public access must be configured.
The VPC needs to support public access. If you use a custom VPC then you must
handle the networking on your own.

EC2 deploys into one AZ. If it fails, the instance fails.

Different sizes and capabilities all use On-Demand Billing - Per second.
Only pay for what you consume.

Charge for running the instance, CPU, memory and storage.
Extra cost for any commercial software the instance deploys with.

Local on-host storage or **Elastic Block Storage**

Pricing based on:

- CPU
- Memory
- Storage
- Networking

#### Running State

Charged for all four categories.

- Running on a physical host using CPU.
- Using memory even with no processing.
- OS is stored on disk allocated
- Networking is always ready to transfer information.

#### Stopped State

Charged for EBS storage  only.

- No CPU resources are being consumed
- No memory is being used
- Networking is not running
- Storage is allocated to the instance for the OS.

#### Terminated State

No charges, deletes the disk and prevents all future charges.

#### AMI (Server Image)

AMI can use used to create an instance or created from an instance.
AMIs in one region are not available from other regions.

Contains:

- Permissions: control which accounts can and can't use the AMI.

  - Public: Anyone can launch it.

  - Owner - Implicit allow, only the owner can use it  spin up new instances

  - Explicit - owner grants access to AMI for specific AWS accounts

- Root Volume: contain the **Boot Volume**

- Block Device Mapping: links the volumes that the AMI has and
how they're presented to the operating system. Determines which volume is a
boot volume and which volumes is a data volume.

AMI Types:

- Amazon Quick Start AMIs
- AWS Marketplace AMIs
- Community AMIs
- Private AMIs

#### Connecting to EC2

- Windows using RDP (Remote Desktop Protocol), Port 3389
- Linux SSH protocol, Port 22

Login to the instance using an SSH key pair.
Private Key - Stored on local machine to initiate connection.
Public Key - AWS places this key on the instance.

### S3 (Default Storage Service)

Global Storage platform. Runs from all regions and is a public service.
Can be accessed anywhere from the internet with an unlimited amount of users.

This should be the default storage platform

S3 is an object storage, not file, or block storage.
You can't mount an S3 Bucket.

#### Objects

Can be thought of a file. Two main components:

- Object Key: File name in a bucket
- Value: Data or contents of the object
  - Zero bytes to 5 TB

Other components:

- Version ID
- Metadata
- Access Control
- Sub resources

#### Buckets

- Created in a specific AWS Region.
- Data has a primary home region. Will not leave this region unless told.
- Blast Radius = Region
- Unlimited number of Objects
- Name is globally unique
- All objects are stored within the bucket at the same level.

If the objects name starts with a slash such as `/old/Koala1.jpg` the UI will
present this as a folder. In actuality this is not true, there are no folders.

### CloudFormation Basics

Templates can modify infrastructure to, create, update and delete.

Written in YAML or JSON

```YAML
## This is not mandatory unless a description is added
AWSTemplateFormatVersion: "version date"

## Give details as to what this template does.
## If you use this section, it MUST immediately follow the AWSTemplateFormatVersion.
Description:
  A sample template

## Can control the command line UI. The bigger your template, the more likely
## this section is needed
Metadata:
  template metadata

## Prompt the user for more data. Name of something, size of instance,
## data validation
Parameters:
  set of parameters

## Another optional section. Allows lookup tables, not used often
Mappings:
  set of mappings

## Decision making in the template. Things will only occur if a condition is met.
## Step 1: create condition
## Step 2: use the condition to do something else in the template
Conditions:
  set of conditions

Transform:
  set of transforms

## The only mandatory field of this section
Resources:
  set of resources

## Once the template is finished it can return data or information.
## Could return the admin or setup address of a word press blog.
Outputs:
  set of outputs
```

#### Resources

An example which creates an EC2 instance

```YAML
Resources:
  Instance: ## Logical Resource
    Type: 'AWS::EC2::Instance' ## This is what will be created
    Properties: ## Configure the resources in a particular way
      ImageId: !Ref LatestAmiId
      Instance Type: !Ref Instance Type
      KeyName: !Ref Keyname
```

Once a template is created, AWS will make a stack. This is a living and active
representation of a template. One template can create infinite amount of stacks.

For any **Logical Resources** in the stack,
CF will make a corresponding **Physical Resources** in your AWS account.

It is cloud formations job to keep the logical and physical resources in sync.

A template can be updated and then used to update the same stack.

### CloudWatch Basics

Collects and manages operational data on your behalf.

Three products in one

- Metrics: data relating to AWS products, apps, on-prem solutions
- Logs: collection, monitoring
- Events: event hub
  - If an AWS service does something, CW events can perform another action
  - Generate an event to do something at a certain time of day or time of week.

#### Namespace

Container for monitoring data.
Naming can be anything so long as it's not `AWS/service` such as `AWS/EC2`.
This is used for all metric data of that service

#### Metric

Time ordered set of data points such as:

- CPU Usage
- Network IN/OUT
- Disk IO

This is not for a specific server. This could get things from different servers

Anytime CPU Utilization is reported, the **datapoint** will report

- Timestamp = 2019-12-03
- Value = 98.3

**Dimensions** separate data points for different **things** or
**perspectives** within the same metric

#### Alarms

Has two states `ok` or `alarm`.State can send an SNS or action.
Third state can be insufficient data state. Not a problem, just wait.

### Shared Responsibility Model

AWS: Responsible for security **OF** the cloud

Customer: Responsible for security **IN** the cloud

### High Availability (HA), Fault-Tolerance (FT), and Disaster Recover (DR)

#### High Availability (HA)

- Aims to **ensure** an agreed level of operational **performance**, usually
**uptime**, for a **higher than normal period**
- Instead of diagnosing the issue, swap it out.
- Redundant hardware to minimize downtown
- User disruption is not ideal, but is allowed
  - The user might need to log back in or lose some data on their screen.
- Maximizing a system's uptime
  - 99.9% (Three 9's) = 8.7 hours downtime per year.
  - 99.999 (Five 9's) = 5.26 minutes downtime per year.

#### Fault-Tolerance (FT)

- System can **continue operating properly**
in the event of the **failure of some** (one or more faults within) of its
**components**
- Fault tolerance is much more complicated than high availability and more
expensive. Outages must be minimized and the system needs levels of
redundancy.
- An airplane is an example of system that needs Fault Tolerance. It has
more engines than it needs for redundancy.

Example:
A patient is waiting for a life saving surgery and is under anesthetic.
While being monitored, the life support system is dosing medicine.
This type of system cannot only be highly available, even a movement of
interruption is deadly.

#### Disaster Recover (DR)

- Set of policies, tools and procedures to **enable the recovery** or
**continuation** of **vital** technology infrastructure and systems
**following a natural or human-induced disaster**.
- DR can largely be automated to eliminate the time for recovery and errors.

This involves:

- Pre-planning
  - Ensure plans are in place for extra hardware
  - Do not store backups at the same site as the system
- DR Processes
  - Cloud machines ready when needed

This is designed to keep the crucial and non replaceable parts of the
system in place.

### Domain Name System (DNS)

DNS is a discovery service. Translates machines into humans and vice-versa.
It is a huge database and has to be distributed.

Parts of the DNS system

- DNS Client: Piece of software running on the OS for a device you're using.
- Resolver: Software on your device or server which queries DNS on your behalf.
- Zone: A part of the DNS database.
  - This would be www.amazon.com
  - What the data is, the substance
- Zonefile: physical database for a zone
  - How physically that data is stored
- Nameserver: where zonefiles are hosted

Steps:

Find the Nameserver which hosts a particular Zonefile.
Query that Nameserver for a record with that Zone.
It then passes the information back to the client.

#### DNS Root

The starting point of DNS.
DNS names are read right to left with multiple parts separated by periods.

`www.netflix.com.`

The period is assumed to be there in a browser when it's not present.
The DNS Root is hosted on DNS Root Servers (13). These are hosted
by 12 major companies.

**Root Hints** is a pointer to the DNS Root server

Process

1. DNS client asks DNS Resolver for IP address of a given DNS name.
2. Using the Root Hints file, the DNS Resolver communicates with one or
more of the root servers to access the root zone and begin the process
of finding the IP address.

The Root Zone is organized by IANA (Internet Assigned Numbers Authority).
Their job is to manage the contents of the root zone. IANA is in charge
of the DNS system because they control the root zone.

#### DNS Hierarchy

Assuming a laptop is querying DNS directly for www.amazon.com and using
a root hints file to know how to access a root server and query the root zone.

- When something is trusted in DNS, it is an **authority**.
- One piece can be authoritative for root.
- One piece can be authoritative for amazon.com
- The root zone is the start and the only thing trusted in DNS.
- The root zone can delegate a part of itself to another zone or entity.
- That someone else then becomes authoritative for that piece of itself only.
- The root zone is just a database of the top level domains.

The top level domains are the only things to the left of the DNS name.

- `.com` or `.org` are generic top level domains (GTLD)
- `.uk` is a country code top level domains (CCTLD)

**Registry** maintains the zones for a TLD (e.g .ORG)
**Registrar** has relationships with the .org TLD zone manager
allowing domain registration

### Route53 Fundamentals

- Registers domains
- Can Host Zone Files on managed nameservers
- This is a global service, no need to pick a region
- Globally Resilience
  - Can operate with failure in one or more regions

#### Register Domains

Has relationships with all major registries

- Route 53 will check with the top level domain to see if the name is available
- Router 53 creates a zonefile for the domain to be registered
- Allocates nameservice for that zone
  - Generally four of these for one individual zone
  - This is a hosted zone
  - The zone file will be put on these four managed nameservers
- Router 53 will communicate with the `.org` registry and add the nameserver
records into the zone file for the top level domain.
  - This is done with a nameserver record.

#### Route53 Details

**Zonefiles** in AWS
Hosted on four managed name servers

- Can be **public** or **private**

### DNS Record

- Nameserver (NS): Allows delegation to occur in the DNS.
- A and AAAA Records: Maps the host to a v4 or v6 host type. Most of the time
you will make both types of record, A and AAAA.
- CNAME Record Type: Allows DNS shortcuts to reduce admin overhead.
CNAMES cannot point directly at an IP address and only another name.
- MX records: How emails are sent. They have two main parts:
  - Priority: Lower values for the priority field are higher priority.
  - Value
    - If it is just a host, it will not have a dot on the right. It is assumed
to be part of the same zone as the host.
    - If you include a dot on the right, it is a ***fully qualified domain name***
- TXT Record: Allows you to add arbitrary text to a domain.
One common usage is to prove domain ownership.

#### TTL - Time To Live

This is a numeric setting on DNS records in seconds.
Allows the admin to specify how long the query can be stored
at the resolver server.
If you need to upgrade the records, it is smart to lower the TTL value first.

Getting the answer from an Authoritative Source is known as an
**Authoritative Answer**.

If another client queries the same thing, they will get back a
**Non-Authoritative** response.

---

## IAM-Accounts-AWS-Organizations

### IAM Identity Policies

Identity Policies are attached to AWS Identities which are
IAM users, IAM groups, and IAM roles. These are a set of security statements
that ALLOW or DENY access to AWS resources.

When an identity attempts to access AWS resources, that identity needs
to prove who it is to AWS, a process known as **Authentication**.
Once authenticated, that identity is known as an **authenticated identity**

#### Statement Components

- Statement ID (SID): Optional field that should help describe
  - The resource you're interacting
  - The actions you're trying to perform
- Effect: is either `allow` or `deny`.
  - It is possible to be allowed and denied at the same time
- Action are formatted `service:operation`. There are three options:
  - specific individual action
  - wildcard as an action
  - list of multiple independent actions
- Resource: similar to action except for format `arn:aws:s3:::catgifs`

#### Priority Level

- Explicit Deny: Denies access to a particular resource cannot be overruled.
- Explicit Allow: Allows access so long there is not an explicit deny.
- Default Deny (Implicit): IAM identities start off with no resource access.

#### Inline Policies and Managed Policies

- Inline Policy: grants access and assigned on each accounts individually.
- Managed Policy (best practice): one policy is applied to all users at once.

### IAM Users

Identity used for anything requiring **long-term** AWS access

- Humans
- Applications
- Service Accounts

If you can name a thing to use the AWS account, this is an IAM user.

When a **principal** wants to **request** to perform an action,
it will **authenticate** against an identity within IAM. An IAM user is an
identity which can be used in this way.

There are two ways to authenticate:

- Username and Password
- Access Keys (CLI)

Once the **Principal** has authenticated, it becomes an **authenticated identity**

#### Amazon Resource Name (ARN)

Uniquely identify resources within any AWS accounts.

This allows you to refer to a single or group of resources.
This prevents individual resources from the same account but in
different regions from being confused.

ARN generally follows the same format:

```bash
arn:partition:service:region:account-id:resource-id
arn:partition:service:region:account-id:resource-type/resource-id
arn:partition:service:region:account-id:resource-type:resource-id
```

- partition: almost always `aws` unless it is china `aws-cn`
- region: can be a double colon (::) if that doesn't matter
- account-id: the account that owns the resource
  - EC2 needs this
  - S3 does not need account-id because its globally unique
- resource-type/id: changes based on the resource

An example that leads to confusion:

- arn:aws:s3:::catgifs
  - This references an actual bucket
- arn:aws:s3:::catgifs/*
  - This refers to objects in that bucket, but not the bucket itself.

These two ARNs do not overlap

#### IAM FACTS

- 5,000 IAM users per account
- IAM user can be a member of 10 groups

### IAM Groups

Containers for users. **You cannot login to IAM groups** They have no
credentials of their own. Used solely for management of IAM users.

Groups bring two benefits

1. Effective administrative style management of users based on the team
2. Groups can have Inline and Managed policies attached.

AWS merges all of the policies from all groups the user is in together.

- The 5000 IAM user limit applies to groups.
- There is **no all users** IAM group.
  - You can create a group and add all users into that group, but it needs to be
created and managed on your own.
- No Nesting: You cannot have groups within groups.
- 300 Group Limit per account. This can be fixed with a support ticket.

**Resource Policy** A bucket can have a policy associated with that bucket.
It does so by referencing the identity using an ARN (Amazon Reference Name).
A policy on a resource can reference IAM users and IAM roles by the ARN.
A bucket can give access to one or more users or one or more roles.

**GROUPS ARE NOT A TRUE IDENTITY**
**THEY CAN'T BE REFERENCED AS A PRINCIPAL IN A POLICY**

An S3 Resource cannot grant access to a group, it is not an identity.
Groups are used to allow permissions to be assigned to IAM users.

### IAM Roles

A single thing that uses an identity is an IAM User.

IAM Roles are also identities that are used by large groups of individuals.
If have more than 5000 principals, it could be a candidate for an IAM Role.

IAM Roles are **assumed** you become that role.

This can be used short term by other identities.

IAM Users can have inline or managed policies which control which permissions
the identity gets within AWS

Policies which grant, allow or deny, permissions based on their associations.

IAM Roles have two types of roles can be attached.

- Trust Policy: Specifies which identities are allowed to assume the role.
- Permissions Policy: Specifies what the role is allowed to do.

If an identity is allowed on the **Trust Policy**, it is given a set
of **Temporary Security Credentials**. Similar to access keys except they
are time limited to expire. The identity will need to renew them by
reassuming the role.

Every time the **Temporary Security Credentials** are used, the access
is checked against the **Permissions Policy**. If you change the policy, the
permissions of the temp credentials also change.

Roles are real identities and can be referenced within resource policies.

Secure Token Service (sts:AssumeRole) this is what generates the temporary
security credentials (TSC).

### When to use IAM Roles

Lambda Execution Role.
For a given lambda function, you cannot determine the number of principals
which suggested a Role might be the ideal identity to use.

- Trust Policy: to trust the Lambda Service
- Permission Policy: to grant access to AWS services.

When this is run, it uses the sts:AssumeRole to generate keys to
CloudWatch and S3.

It is better when possible to use an IAM Role versus attaching a policy.

#### Emergency or out of the usual situations

Break Glass Situation - There is a key for something the team does not
normally have access to. When you break the glass, you must have a reason
to do.
A role can have an Emergency Role which will allow further access if
its really needed.

#### Adding AWS into existing corp environment

You may have an existing identity provider you are trying to allow access to.
This may offer SSO (Single Sign On) or over 5000 identities.
This is useful to reuse your existing identities for AWS.
External accounts can't be used to access AWS directly.
To solve this, you allow an IAM role in the AWS account to be assumed
by one of the active directories.
**ID Federation** allowing an external service the ability to assume a role.

#### Making an app with 1,000,000 users

**Web Identity Federation** uses IAM roles to allow broader access.
These allow you to use an existing web identity such as google, facebook, or
twitter to grant access to the app.
We can trust these web identities and allow those identities to assume
an IAM role to access web resources such as DynamoDB.
No AWS Credentials are stored on the application.
Can scale quickly and beyond.

#### Cross Account Access

You can use a role in the partner account and use that to upload objects
to AWS resources.

### AWS Organizations

Without an organization, each AWS account needs it's own set of IAM users
as well as individual payment methods.
If you have more than 5 to 10 accounts, you would want to use an org.

Take a single AWS account **standard AWS account** and create an org.
The standard AWS account then becomes the **master account**.
The master account can invite other existing standard AWS accounts. They will
need to approve their joining to the org.

When standard AWS accounts become part of the org, they
become **member accounts**.
Organizations can only have one **master accounts** and zero or more
**member accounts**

#### Organization Root

This is a container that can hold AWS member accounts or the master account.
It could also contain **organizational units** which can contain other
units or member accounts.

#### Consolidated billing

The individual billing for the member accounts is removed and they pass their
billing to the master account.
Inside an AWS organization, you get a single monthly bill for the master
account which covers all the billing for each users.
Can offer a discount with consolidation of reservations and volume discounts

#### Create new accounts in an org

Adding accounts in an organization is easy with only an email needed.
You no longer need IAM users in each accounts. You can use IAM roles
to change these.
It is best to have a single AWS account only used for login.
Some enterprises may use an AWS account while smaller ones may use the master.

#### Role Switching

Allows you to switch between accounts from the command line

### Service Control Policies

Can be used to restrict what member accounts in an org can do.

JSON policy document that can be attached:

- To the org as a whole by attaching to the root container.
- A specific Organizational Unit
- A specific member only.

The master account cannot be restricted by SCPs which means this
should not be used because it is a security risk.

SCPs limit what the account, **including root** can do inside that account.
They don't grant permissions themselves, just act as a barrier.

#### Allow List vs Deny List

Deny list is the default.

When you enable SCP on your org, AWS applies `FullAWSAccess`. This means
SCPs have no effect because nothing is restricted. It has zero influence
by themselves.

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }
}
```

SCPs by themselves don't grant permissions. When SCPs are enabled,
there is an implicit deny.

You must then add any services you want to Deny such as `DenyS3`

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Deny",
    "Action": "s3:*",
    "Resource": "*"
  }
}
```

**Deny List** is a good default because it allows for the use of growing
services offered by AWS. A lot less admin overhead.

**Allow List** allows you to be conscience of your costs.

- To begin, you must remove the `FullAWSAccess` list
- Then, specify which services need to be allowed access.
- Example `AllowS3EC2` is below

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
        "Effect": "Allow",
        "Action": [
            "s3:*",
            "ec2:*"
        ],
    "Resource": "*"
    }
  ]
}
```

### CloudWatch Logs

This is a public service, this can be used from AWS VPC or on premise
environment.

This allows to **store**, **monitor** and **access** logging data.

- This is a piece of information data and a timestamp
- Can be more fields, but at least these two

Comes with some AWS Integrations.
Security is provided with IAM roles or Service roles
Can generate metrics based on logs **metric filter**

#### Architecture of CloudWatch Logs

It is a regional service `us-east-1`

Need logging sources such as external APIs or databases. This sends
information as **log events**. These are stored in **log streams**. This is a
sequence of log events from the same source.

**Log Groups** are containers for multiple logs streams of the same
type of logging. This also stores configuration settings such as
retention settings and permissions.

Once the settings are defined on a log group, they apply to all log streams
in that log group. Metric filters are also applied on the log groups.

### CloudTrail Essentials

Concerned with who did what.

Logs API calls or activities as **CloudTrail Event**

Stores the last 90 days of events in the **Event History**. This is enabled
by default and is no additional cost.

To customize the service you need to create a new **trail**.
Two types of events. Default only logs Management Events

- Management Events:
Provide information about management operations performed on resources
in the AWS account. Create an EC2 instance or terminating one.

- Data Events:
Objects being uploaded to S3 or a Lambda function being invoked. This is not
enabled by default and must be enabled for that trail.

#### CloudTrail Trail

Logs events for the AWS region it is created in. It is a regional service.

Once created, it can operate in two ways

- One region trail
- All region trail
  - Collection of trails in all regions
  - When new regions are added, they will be added to this trail automatically

Most services log events in the region they occur. The trail then must be
a one region trail in that region or an all region trail to log that event.

A small number of services log events globally to one region. Global services
such as IAM or STS or CloudFront always log their events to `us-east-1`

A trail must have this enabled to have this logged.

AWS services are largely split into regional services or global services.

When the services log, they log in the region they are created or
to `us-east-1` if they are a global service.

A trail can store events in an S3 bucket as a compressed JSON file. It can
also use CloudWatch Logs to output the data.

CloudTrail products can create an organizational trail. This allows a single
management point for all the APIs and management events for that org.

#### CloudTrail Exam PowerUp

- It is enabled by default for 90 days without S3
- Trails are how you configure S3 and CWLogs
- Management events are only saved by default
- IAM, STS, CloudFront are Global Service events and log to `us-east-1`
  - Trail must be enabled to do this
- NOT REALTIME - There is a delay. Approximately 15 minute delay

#### CloudTrail Pricing

<https://aws.amazon.com/cloudtrail/pricing/>

---

## Simple-Storage-Service-(S3)

### S3 Security

**S3 is private by default!** The only identity which has any initial
access to an S3 bucket is the account root user of the account which owns that
bucket.

#### S3 Bucket Policy

This is a **resource policy**

- controls who has access to that resource
- can allow or deny access from different accounts
- can allow or deny anonymous principals
  - this is explicitly declared in the bucket policy itself.

Different from an **identity policy**

- controls what that identity can access
- can only be attached to identities in your own account
  - no way of giving an identity in another account access to a bucket.

Each bucket can only have one policy, but it can have multiple statements.

#### ACLs (Legacy)

A way to apply a subresource to objects and buckets.
These are legacy and AWS does not recommend their use.
They are inflexible and allow simple permissions.

#### S3 Exam PowerUp

When to use Identity Policy or Bucket Policy:

Identity

- Controlling high mix of different resources.
  - Not every service supports resource policies.
- Want to manage permissions all in one place, use IAM.
- Must have access to all accounts accessing the information.

Bucket

- Managing permissions on a specific product.
- If you need anonymous or cross account access.

ACLs: NEVER - unless you must.

### S3 Static Hosting

Normal access is via AWS APIs.
This allows access via HTTP using a web browser.

When you enable static website hosting you need two HTML files:

- index document
  - default page returned from a website
  - entry point for most websites
- error document
  - similar to index, but only when something goes wrong

Static website hosting creates a **website endpoint**.

This is influenced by the bucket name and region it is in.
This cannot be changed.

You can use a custom domain for a bucket, but then the bucket name matters.
The name of the bucket must match the domain.

#### Offloading

Instead of using EC2 to host an entire website, the compute service
can generate a HTML file which points to the resources hosted on a static
bucket. This ensures the media is retrieved from S3 and not EC2.

#### Out-of-band pages

This may be an error page to display maintenance if the server goes offline.
We could then change our DNS and move customers to a backup website on S3.

#### S3 Pricing

- Cost to store data, per GB / month fee
  - Prorated for less than a GB or month.
- Data transfer fee
  - Data in is always free
  - Data out is a per GB charge
- Each operation has a cost per 1000 operations.
  - Can add up for static website hosting with many requests.

### Object Versioning and MFA Delete

Without Versioning:

- Each object is identified solely by the object key, it's name.
- If you modify an object, the original of that object is replaced.
- The attribute, **ID of object**, is set to **null**.

Versioning

- This is off by default.
- Once it is turned on, it cannot be turned off.
- Versioning can be suspended and enabled again.
- This allows for multiple versions of objects within a bucket.
- Objects which would modify objects **generate a new version** instead.

The latest or current version is always returned when an object version
is not requested.

When an object is deleted, AWS puts a **delete marker** on the object
and hides all previous versions. You could delete this marker to enable
the item.

To delete an object, you must delete all the versions of that object
using their version marker.

#### MFA Delete

Enabled within version configuration in a bucket.
This means MFA is required to change bucket versioning state.
MFA is required to delete versions of an object.

In order to change a version state or delete a particular version of an object,
you need to provide the serial number of your MFA token as well as the code
it generates. These are concatenated and passed with any API calls.

### S3 Performance Optimization

Single PUT Upload

- Objects uploaded to S3 are sent as a single stream by default.
- If the stream fails, the upload fails and requires a restart of the transfer.
- Single PUT upload up to 5GB

Multipart Upload

- Data is broken up into smaller parts.
- The minimum data size is 100 MB.
- Upload can be split into maximum of 10,000 parts.
  - Each part can range between 5MB and 5GB.
  - Last leftover part can be smaller than 5MB as needed.
- Parts can fail in isolation and restart in isolation.
- The risk of uploading large amounts of data is reduced.
- Improves transfer rate to be the speed of all parts.

S3 Accelerated Transfer

- Off by default.
- Uses the network of AWS edge locations to speed up transfer.
- Bucket name cannot contain periods.
- Name must be DNS compatible.
- Benefits improve the larger the location and distance.
  - The worse the start, the better the performance benefits.

### Encryption 101

#### Encryption at Rest

- An example is a password on a laptop
  - If the laptop is stolen, the data is already encrypted and useless.
- Commonly within cloud environments. Even if someone could
find and access the base storage device, they can't do anything with it.
- Only one entity involved

#### Encryption in Transit

- An encryption tunnel outside the raw data.
- Anyone looking from the outside will only see a stream of scrambled data.
- Used when there are multiple parties or systems at play.

#### Terms

- plaintext: unencrypted data not limited to text
- key: a password
- ciphertext: encrypted data generated by an algorithm from plaintext and a key

#### Symmetric Encryption

The key is handed from one entity to another before the data.
This is difficult because the key needs to be transferred securely.
If the data is time sensitive, the key needs to be arranged beforehand.

#### Asymmetric Encryption

- public key: cannot decrypt data but can generate ciphertext
- private key: can decrypt data encrypted by the public key

The public key is uploaded to cloud storage.
The data is encrypted and sent back to the original entity.
The private key can decrypt the data.

This is secure because stolen public keys can only encrypt data.
Private keys must be handled securely.

#### Signing

Encryption by itself does not prove who encrypted the data.

1. An entity can encrypt a message with their private key.
2. Their public key is hosted in an accessible location.
3. The receiving party can use the public key to confirm who sent the message.

#### Steganography

Encryption is obvious when used. There is no denying that the
data was encrypted. Someone could force you to decrypt the data packet.

A file can be hidden in an image or other file. If it difficult
to find the message unless you know what to look for.

One party would take another party's public key and encrypt some data to create
ciphertext. That ciphertext can be hidden in another file so long as both
parties know how the data will be hidden.

### Key Management Service (KMS)

- Regional service
  - Every region is isolated when using KMS.
- Public service
  - Occupies the AWS public zone and can be connected to from anywhere.
- Create, store, and manage keys.
  - Can handle both symmetric and asymmetric keys.
- KMS can perform cryptographic operations itself.
- Keys never leave KMS.
- Keys use **FIPS 140-2 (L2)** security standard.
  - Some features are compliant with Level 3.
  - All features are compliant with Level 2.

#### CMKs - Customer Master Keys

- Managed by KMS and used within cryptographic operations.
- AWS services, applications, and the user can all use them.
- Think of them as a container for the actual physical master keys.
- These are all backed by **physical** key material.
- You can generate or import the key material.
- CMKs can be used for up to **4KB of data**.

It is logical and contains

- Key ID: unique identifier for the key
- Creation Date
- Key Policy: a type of resource policy
- Description
- State of the Key: active or not

#### Data Encryption Key (DEK)

- Generated by KMS using the CMK and `GenerateDataKey` operation.
- Used to encrypt data larger than 4KB in size.
- Linked to a specific CMK so KMS can tell that a specific DEK was
generated with a specific CMK.

KMS does not store the DEK, once provided to a user or service, it is
discarded. KMS doesn't actually perform the encryption or decryption
of data using the DEK or anything past generating them.

When the DEK is generated, KMS provides two version.

- Plaintext Version - This can be used immediately.
- Ciphertext Version - Encrypted version of the DEK.
  - This is encrypted by the CMK that generated it.
  - In the future it can be decrypted by KMS using the CMK assuming
  you have the permissions.

Architecture

1. DEK is generated right before something is encrypted.
2. The data is encrypted with the plaintext version of the DEK.
3. Discard the plaintext data version of the DEK.
4. The encrypted DEK is stored next to the ciphertext generated earlier.

#### KMS Key Concepts

- Customer Master Keys (CMK) are isolated to a region.
  - Never leave the region or KMS.
  - Cannot extract a CMK.
- AWS managed CMKs
  - Created automatically by AWS when using a service such
  as S3 which uses KMS for encryption.
- Customer managed CMKS
  - Created explicitly by the customer.
  - Much more more configurable, for example the key policy can be edited.
  - Can allow other AWS accounts access to CMKS

All CMKs support key rotation.

- AWS automatically rotates the keys every 1095 days (3 years)
- Customer managed keys rotate every year.

CMK itself contains:

- Current backing key, physical material used to encrypt and decrypt
- Previous backing keys created from rotating that material

KMS can create an alias which is a shortcut to a particular CMK.
Aliases are also per region. You can create a `MyApp1` alias in all regions
but they would be separate aliases, and in each region it would be pointing
potentially at a different CMK.

#### Key Policy (resource policy)

- Every CMK has one.
- Customer managed CMKs can adjust the policy.
- Unlike other policies, KMS has to be explicitly told that keys trust the AWS
account that they're in.
- The trust isn't automatic so be careful when adjusting key policies.
- You always need a key policy in place so the key trusts the account and so
that the account can manage it by applying IAM permission policies to IAM users
in that account.
- In order for IAM to work, IAM is trusted by the account, and the account
needs to be trusted by the key.
- It sets up this chain of trust from the key to the account to IAM and then
to an IAM user, if they're granted any identity permissions.

### KMS Key Demo

Linux/macOS commands

```bash
aws kms encrypt \
    --key-id alias/catrobot \
    --plaintext fileb://battleplans.txt \
    --output text \
    --query CiphertextBlob \
    --profile iamadmin-general | base64 \
    --decode > not_battleplans.enc
```

```bash
aws kms decrypt \
    --ciphertext-blob fileb://not_battleplans.enc \
    --output text \
    --profile iamadmin-general \
    --query Plaintext | base64 --decode > decryptedplans.txt
```

### Object Encryption

Buckets aren't encrypted, **objects are**.
Multiple objects in a bucket can use a different encryption methods.

Two main methods of encryption S3 is capable of supporting.
Both types are encryption at rest. Data sent from a user to S3 is automatically
encrypted in transit outside of these methods.

Client-Side encryption

- Objects being encrypted by the client before they leave.
- Data being sent the whole time it is sent as cypher text.
- AWS has no way to see into the data.
- The encryption burden is on the customer and not AWS.

Server-Side encryption

- Data is encrypted in transit using HTTPS
- Data inside the tunnel is still in its original unencrypted form.
- Data reaches S3 server in plain text form.
- After S3 sees the data, it is then encrypted.
- AWS will handle some or all of these processes.

#### SSE-C (Server-side encryption with customer provided keys)

- Customer is responsible for the keys themselves.
- S3 services manages the actual encryption and decryption
  - Offloads CPU requirements for encryption.
- Customer still needs to generate and manage the key.
- S3 will see the unencrypted object throughout this process.

SSE-C Encryption Steps

1. When placing an object in S3, you provide encryption key and plaintext object
2. Once the key and object arrive, it is encrypted.
3. A hash of the key is taken and attached to the object.
The hash can identify if the specific key was used to encrypt the object.
4. The key is then discarded after the hash is taken.
5. The encrypted and one-way hash are stored persistently on storage.

To decrypt the object, you must tell S3 which object to decrypt and provide
it with the key used to encrypt it. If the key that you supply is correct,
the proper hash, S3 will decrypt the object, discard the key, and return the
plaintext version of the object.

#### SSE-S3 AES256 (Server-side encryption w/ Amazon S3 managed keys)

AWS handles both the encryption and decryption process as well as the
key generation and management. This provides very little control over how
the keys are used, but has little admin overhead.

SSE-S3 Encryption Steps

1. When putting data into S3, only need to provide plaintext.
2. S3 generates fully managed and rotated **master key** automatically.
3. Object generates a key specific for each object that is uploaded.
4. The master key is used to encrypt the specific object key, and the
unencrypted version of that key is discarded.
5. The encrypted file and encrypted key are stored side by side in S3.

Three Problems with this method:

- Not good for regulatory environment where keys and access must be controlled.
- No way to control key material rotation.
- No role separation. A full S3 admin can decrypt data and open objects.

#### SSE-KMS (Server-side encryption w/ customer master keys stored in AWS KMS)

Much like SSE-S3, where AWS handles both the keys and encryption process.
KMS handles the master key and not S3. The first time an object is uploaded,
S3 works with KMS to create an AWS managed CMK. This is the default key
which gets used in the future.

Every time an object is uploaded, S3 uses a dedicated key to encrypt that object
and that key is a data encryption key which KMS generates using the CMK.
The CMK does not need to be managed by AWS and can be a customer managed CMK.

SSE-KMS Encryption Steps

1. S3 is provided a plaintext version of the data encryption key as well
as an encrypted version.
2. The data is encrypted with the plaintext key and the key discarded.
3. The encrypted key is stored alongside the encrypted object.

When uploading an object, you can create and use a customer managed CMK. This
allows the user to control the permissions and the usage of the key material.
In regulated industries, this is reason enough to use SSE-KMS
You can also add logging and see any calls against this key from CloudTrail.

The best benefit is the role separation. To decrypt any object, you need
access to the CMK that was used to generate the unique key that encrypted them.
The CMK is used to decrypt the data encryption key for that object.
That decrypted data encryption key is used to decrypt the object itself.
If you don't have access to KMS, you don't have access to the object.

### S3 Object Storage Classes

Picking a storage class can be done while uploading a specific object.
The default is S3 standard. Once an object is uploaded to a specific class,
it can be easily changed as long as some conditions are met.

Objects in S3 are stored in a specific region.

#### S3 Standard

- Default AWS storage class that's used in S3, should be user default as well.
- S3 Standard is region resilient, and can tolerate the failure of an AZ.
- Objects are replicated to at least 3+ AZs when they are uploaded.
- 99999999999% durability
- 99.99% availability
- Offers low latency and high throughput.
- No minimums, delays, or penalties.
- Billing is storage fee, data transfer fee, and request based charge.

All of the other storage classes trade some of these compromises for another.

#### S3 Standard-IA

- Designed for less frequent rapid access when it is needed.
- Cheaper rate to store data you will rarely need, but if you do need it, you
need it quickly.
- ~54% cheaper than S3 standard.
- Minimum 128KB charge for each object.
  - Cost benefits might be negated for smaller objects.
- 30 days minimum duration charge per object.
- Retrieval fee for every GB of data retrieved from this class.
- 99.9% availability, slightly lower than standard S3.

Designed for data that isn't accessed often, long term storage, backups,
disaster recovery files. The requirement for data to be safe is most important.

#### One Zone-IA

- Designed for data that is accessed less frequently but needed quickly.
- 80% of the base cost of Standard-IA.
- Same minimum size and duration fee as Standard-IA
- Data is only stored in a single AZ, no 3+ AZ replication.
- 99.5% availability, lower than Standard-IA

Great choice for secondary copies of primary data or backup copies.

If data is easily creatable from a primary data set, this would be a great
place to store the output from another data set.

#### S3 Glacier

- No immediate access to objects, retrieval in minutes or hours.
- Make a request to access objects then after a duration, you get access.
  - Retrieval time anywhere from 1 min - 12 hrs
- Secure, durable, and low cost storage for archival data.
- 17% of the base cost of S3 standard
- 99999999999% durability
- 99.99% availability
- 3+ AZ replication
- 40KB minimum object capacity charge
- 90 days minimum storage duration charge.

Retrieval methods:

- Expedited: 1 - 5 minutes, but is the most expensive
- Standard: 3 - 5 hours to restore.
- Bulk: 5 - 12 hours. Has the lowest cost and is good for a large set of data.

#### S3 Glacier Deep Archive

- Designed for long term backups and as a tape-drive replacement.
- 4.3% of the base cost of S3 standard
- 180 days minimum storage duration charge.
- Standard retrieval within 12 hours, bulk retrieval in 48 hours.
- Cannot use to make data public or download normally.

#### S3 Intelligent-Tiering

- Combination of standard and standard IA.
- Uses automation to remove overhead of moving objects.
- Additional fee of $0.0025 per 1,000 objects tracked.
- If an object is not accessed for 30 days, it will move into Standard-IA.

This is good for objects that are unknown their access pattern.

### Object Lifecycle Management

Intelligent-Tiering is used for objects where access patterns is unknown.
A lifecycle configuration is a set of **rules** that consists of **actions**.

#### Transition Actions

Change the storage class over time such as:

- Move an object from S3 to IA after 90 days
- After 180 days move to Glacier
- After one year move to Deep Archive

Objects must flow downwards, they can't flow in the reverse direction.

#### Expiration Actions

Once an object has been uploaded and changed, you can purge older versions
after 90 days to keep costs down.

### S3 Replication

There are two types of S3 replication available.

- Cross-Region Replication (CRR)
  - Allows the replication of objects from a source bucket to a destination
bucket in **different** AWS regions.
- Same-Region Replication (SRR)
  - Allows the replication of objects from a source bucket to a destination
bucket in the **same** AWS region.

Architecture for both is similar, only difference is if both buckets are
in the same account or different accounts.

The replication configuration is applied to the source bucket and configures
S3 to replicate from this source bucket to a destination bucket.
It also configures the IAM role to use for the replication process.
The role is configured to allow the S3 service to assume it based on
its trust policy. The role's permission policy allows it to read objects on the
source bucket and replicate them to the destination bucket.

When different accounts are used, the role is not by default trusted
by the destination account. If configuring between accounts, you must
add a bucket policy on the destination account to allow the IAM role from
the source account access to the bucket.

#### S3 Replication Options

- Which objects are replicated.
  - Default is all source objects, but can select a smaller subset of objects.
- Select which storage class the destination bucket will use.
  - Default is the same type of storage, but this can be changed.
- Define the ownership of the objects.
  - The default is they will be owned by the same account as the source bucket.
  - If the buckets are in different accounts, the objects in the destination
  could be owned by the source account and not allowed access.
- Replication Time Control (RTC)
  - Adds a guaranteed level of SLA within 15 minutes for extra cost.
  - This is useful for buckets that must be in sync the whole time.

#### Important Replication Tips

- Replication is not retroactive.
  - If you enable replication on a bucket that already has objects, the old
  objects will not be replicated.
- Both buckets must have versioning enabled.
- It is a one way replication process only.
- Replication by default can handle objects that are unencrypted or SSE-S3.
  - With configuration it can handle SSE-KMS, but KMS requires more
configuration to work.
  - It cannot replicate objects with SSE-C because AWS does not have the keys
necessary.
- Source bucket owner needs permissions to objects. If you grant cross-account
access to a bucket. It is possible the source bucket account will not own
some of those objects.
- Will not replicate system events, glacier, or glacier deep archive.
- No deletes are replicated.

#### Why use replication

SRR - Log Aggregation
SRR - Sync production and test accounts
SRR - Resilience with strict sovereignty requirements
CRR - Global resilience improvements
CRR - Latency reduction

### S3 Presigned URL

A way to give another person or application access to a object inside an S3
bucket using your credentials in a safe way.

IAM admin can make a request to S3 to generate a presigned URL by providing:

- security credentials
- bucket name
- object key
- expiry date and time
- indicate how the object or bucket will be accessed

S3 will create a presigned URL and return it. This URL will have encoded inside
it the details that IAM admin provided. It will be configured to expire at
a certain date and time as requested by the IAM admin user.

#### S3 Presigned URL Exam PowerUp

- You can create a presigned URL for an object you have do not have access to.
The object will not allow access because your user does not have access.
- When using the URL the permission that you have access to, match the identity
that generated it at the moment the item is being accessed.
- If you get an access deny it means the ID never had access, or lost it.
- Don't generate presigned URLs with an IAM role.
  - The role will likely expire before the URL does.

### S3 Select and Glacier Select

This provides a ways to retrieve parts of objects and not the entire object.

If you retrieve a 5TB object, it takes time and consumes 5TB of data.
Filtering at the client side doesn't reduce this cost.

S3 and Glacier select lets you use SQL-like statements to select part of the
object which is returned in a filtered way.
The filtering happens at the S3 service itself saving time and data.

---

## Virtual-Private-Cloud-VPC

### Networking Refresher

#### IPv4 - RFC 791 (1981)

Dotted decimal notation for human readability.

- 4 numbers from 0 to 255 separated by a period.
- Octet are the numbers between the period.

There are just over 4 billion addresses.
This was not very flexible because it was either too small or large for
some corporations. Some IP addresses was always left unused.

#### Classful Addressing

- Class A range
  - Starts at `0.0.0.0` and ends at `127.255.255.255`.
  - Split into 128 class A networks
  - Handed out to large companies
- Class B Range
  - Half the range of class A.
  - Starts at `128.0.0.0` and ends at `191.255.255.255`.
- Class C Range
  - Half of range class B
  - Starts at `192.0.0.0` and ends at `223.255.255.255`.

#### Internet / Private IPs - RFC1918

These can't communicate over the internet and are used internally only

- One class A network: `10.0.0.0` - `10.255.255.255`
- 16 Class B networks: `172.16.0.0` - `172.31.255.255`
- 256 Class C networks: `192.168.0.0` - `192.168.255.255`

#### Classless inter-domain routing (CIDR)

CIDR networks are represented by the starting IP address of the network
called the network address and the prefix.

CIDR Example: `10.0.0.0/16`

- `10.0.0.0` is the first address on the network
- /16 is the size of the network called the prefix.
  - The bigger the prefix, the smaller the network
  - The smaller the prefix, the bigger the network.
- /16 provides 65,536 addresses.
- `10.0.0.0/17` and `10.0.128.0/17` are each half of the original example.
  - This is called **subnetting**

#### IP address notations to remember

- `0.0.0.0/0` means all IP addresses
- `10.0.0.0/8` means 10.ANYTHING - Class A
- `10.0.0.0/16` means 10.0.ANYTHING - Class B
- `10.0.0.0/24` means 10.0.0.ANYTHING - Class C
- `10.0.0.0/32` means only 1 IP address

`10.0.0.0/16` is the equivalent of `1234` as a password. You should consider
other ranges that people might use to ensure it does not overlap.

#### Packets

Contains:

- source IP address
- destination IP address
- data the source IP wants to communicate with the destination IP.

TCP and UDP are protocols built on top of IP.

- TCPIP means TCP running with IP
- UDPIP means UDP running with IP

TCP/UDP Segment has a source and destination port number.
This allows devices to have multiple conversations at the same time.
In AWS when data goes through network devices, filters can be set based on
IP addresses and port numbers.

#### IPv6 - RFC 8200 (2017)

`2001:0db8:28ac:0000:0000:82ae:3910:7334`

The value is hex and there are two octets per spacing or one hextet.
The redundant zeros can be removed to create:

`2001:0db8:28ac:0:0:82ae:3910:7334`

or you can remove them all entirely once per address

`2001:0db8:28ac::82ae:3910:7334`

Each address is 128 bits long. They are addressed by the start of the network
and the prefix.
Since each grouping is 16 values, we can multiple the groups by this to achieve
the prefix.

`2001:0db8:28ac::/48` really means the network starts at
`2001:0db8:28ac:0000:0000:0000:0000:0000` and finishes at
`2001:0db8:28ac:ffff:ffff:ffff:ffff:ffff`

`::/0` represents all IPv6 addresses

### VPC Sizing and Structure

VPC Consideration

- What size should the VPC be. This will limit the use.
- Are there any networks we can't use?
- Be mindful of ranges other VPCs use or are used in other cloud environments
- Try to predict the future uses.
- VPC structure with tiers and resilience (availability) zones
- VPC min /28 network (16 IP)
- VPC max /16 (65456 IP)
- Avoid common range 10.0 or 10.1, include up to 10.10
  - Suggest starting of 10.16 for a nice clean base 2 number.

Reserve 2+ network ranges per region being used per account.
Think of the highest region you will operate in and add extra as a buffer.

An example using 4 AWS accounts.

- Regions with 2 ranges in each Region
  - 3 regions in US
  - 1 region in Europe
  - 1 region in AUS
- Total of 40 ranges, 10 ranges for each account.

#### How to size VPC

A subnet is located in one availability zone.
Try to split each subnet into tiers (web, application, db, spare).
Since each Region has at least 3 AZ's, it is a good practice to start
splitting the network into 4 different AZs.
This allows for at least one subnet in each AZ, and one spare.
Taking a /16 subnet and splitting it 16 ways will make each a /20.

### Default VPC facts

- regions can only have 1 default VPC and many custom VPCs
- custom VPCs allow flexible network configuration, the default VPC has a fixed scheme
- there's no limit to how many services use the default VPC
- some services can behave oddly if the default VPC doesn't exist (better to not remove it)
- default VPCs can be recreated


### Custom VPC

- Regional Isolated and Resilient Service.
  - Operates from all AZs in that region
- Allows isolated networks inside AWS.
- Nothing IN or OUT of a VPC without explicit configuration.
  - Isolated blast radius. Any problems are limited to that VPC or anything
  connected to it.
- Flexible configuration
- Hybrid networking to allow connection to other cloud or on-prem networking.
- Default or Dedicated Tenancy. This refers to how the hardware is configured.
  - Default allows on a per resource decision later on.
  - Dedicated locks any resourced created in that VPC to be on dedicated
  hardware which comes at a cost premium.

#### Custom VPC Facts

IPv4 private and public IPs

- Allocated 1 mandatory private IPv4 CIDR blocks
  - Min /28 prefix (16 IP)
  - Max /16 prefix (65,536 IP)
- Can add secondary IPv4 Blocks after creation.
  - Max of 5, can be increased with a support ticket
  - When thinking of VPC, it has a pool of private IPv4 addresses and can
  use public addresses when needed.

Single assigned IPv6 /56 CIDR block

- Still being matured, not everything works the same as IPv4.
- With increasing use of IPv6, this should be added as a default
- Range is either allocated by AWS as in you have no choice on which range
to use, or you can select to use your own IPv6 addresses which you own.
- IPv6 does not have private addresses, they are all routed as public by
default.

#### DNS provided by R53

Available on the base IP address of the VPC + 2.
If the VPC is `10.0.0.0` then the DNS IP will be `10.0.0.2`

Two options that manage how DNS works in a VPC:

- Edit DNS hostnames
  - If true, instances with public IPs in a VPC are given public DNS hostnames.
  - If false, this is not available.

- Edit DNS resolution
  - If true, instances in the VPC can use the DNS IP address.
  - If false, this is not available.

### VPC Subnets

- AZ Resilient subnetwork of a VPC.
  - If the AZ fails, the subnet and services also fail.
  - High availability needs multiple components into different AZs.
- 1 subnet can only have 1 AZ.
- 1 AZ can have zero or many subnets.
- IPv4 CIDR is a subset of the VPC CIDR block.
  - Cannot overlap with any other subnets in that VPC
- Subnet can optionally be allocated IPv6 CIDR block.
  - (256 /64 subnets can fit in the /56 VPC)
- Subnets can communicate with other subnets in the VPC by default.

#### Reserved IP addresses

There are five IP addresses within every VPC subnet that you cannot use.
Whatever size of the subnet, the IP addresses are five less than you expect.

If using `10.16.16.0/20` (`10.16.16.0` - `10.16.31.255`)

- Network address: `10.16.16.0`
- Network + 1: `10.16.16.1` - VPC Router
- Network + 2: `10.16.16.2` - Reserved for DNS
- Network + 3: `10.16.16.3` - Reserved for future AWS use
- Broadcast Address: `10.16.31.255` (Last IP in subnet)

#### DHCP Options Set

This is how computing devices receive IP addresses automatically. There is
one options set applied to a VPC at one time and this configuration flows
through to subnets.

- This can be changed, can create new ones, but you cannot edit one.
- If you want to change the settings
  - You can create a new one
  - Change the VPC allocation to the new one
  - Delete the old one

#### IP allocation Options

- Auto Assign public IPv4 address
  - This will create a public IP address in addition to their private subnet.
  - This is needed to make a subnet public.
- Auto Assign IPv6 address
  - For this to work, the subnet and VPC need an allocation of addresses.

### VPC Routing and Internet Gateway

VPC Router is a highly available device available in every VPC which moves
traffic from somewhere to somewhere else.
Router has a network interface in every subnet in the VPC.
Routes traffic between subnets.

Route tables defines what the VPC router will do with traffic
when data leaves that subnet.
A VPC is created with a main route table. If you don't associate a custom
route table with a subnet, it uses the main route table of the VPC.

If you do associate a custom route table you create with a subnet, then the
main route table is disassociated. A subnet can only have one route table
associated at a time, but a route table can be associated by many subnets.

#### Route Tables

When traffic leaves the subnet that this route table is associated with, the
VPC router reviews the IP packets looking for the destination address.
The traffic will try to match the route against the route table. If there
are more than one routes found as a match, the prefix is used as a priority.
The higher the prefix, the more specific the route, thus higher priority.
If the target says local, that means the destination is in the VPC itself.
Local route can never be updated, they're always present and the local route
always takes priority. This is the exception to the prefix rule.

#### Internet Gateway

**PUBLIC SUBNET** - a subnet that's associated with a route table that has a route to an Internet Gateway

A managed service that allows gateway traffic between the VPC and the internet
or AWS Public Zones (S3, SQS, SNS, etc.)

- Regional resilient gateway attached to a VPC.
- One IGW will cover all AZ's in a region the VPC is using.
- A VPC can have either:
  - No IGW and be entirely private.
  - One IGW
- IGW can be created and attached to no VPC.
- Runs from within the AWS public zone.

#### Using IGW

In this example, an EC2 instance has:

- Private IP address of 10.16.16.20
- Public address of 43.250.192.20

The public address is not public and connected to the EC2 instance itself.
Instead, the IGW creates a record that links the instance's private IP
to the public IP. This is why when an EC2 instance is created it only
sees the private IP address. This is IMPORTANT. For IPv4 it is not configured
in the OS with the public address.

When the linux instance wants to communicate with the linux update service,
it makes a packet of data.
The packet has a source address of the EC2 instance and a destination address
of the linux update server. At this point the packet is not configured with
any public addressing and could not reach the linux update server.

The packet arrives at the internet gateway.

The IGW sees this is from the EC2 instance and analyzes the source IP address.
It changes the packet source IP address from the linux EC2 server and puts
on the public IP address that is routed from that instance. The IGW then
pushes that packet on the public internet.

On the return, the inverse happens. As far as it is concerned, it does not know
about the private address and instead uses the instance's public IP address.

If the instance uses an IPv6 address, that public address is good to go. The IGW
does not translate the packet and only pushes it to a gateway.

#### Bastion Host / Jumpbox

It is an instance in a public subnet inside a VPC.
These are used to allow incoming management connections.
Once connected, you can then go on to access internal only VPC resources.
Used as a management point or as an entry point for a private only VPC.

This is an inbound management point. Can be configured to only allow
specific IP addresses or to authenticate with SSH. It can also integrate
with your on premise identification service.

### Network Access Control List (NACL)

Network Access Control Lists (NACLs) are a type of security filter
(like firewalls) which can filter traffic as it enters or leaves a subnet.

All VPCs have a default NACL, this is associated with all subnets of that VPC
by default.
NACLs are used when traffic enters or leaves a subnet.
Since they are attached to a subnet and not a resource, they only filter
data as it crosses in or out.
If two EC2 instances in a VPC communicate, the NACL does nothing because
it is not involved.

NACLs have an inbound and outbound sets of rules.

When a specific rule set has been called, the one with the lowest
rule number first.
As soon as one rule is matched, the processing stops for
that particular piece of traffic.

The action can be for the traffic to **allow** or **deny** the traffic.

Each rule has the following fields related to traffic

- type
- protocol: tcp, udp, or icmp
- port range
- Inbound rule: Source - who traffic is from
- Outbound rule: Destination - who traffic is destined to

Examples:

- ssh: tcp port 22
- http: tcp port 80
- https: tcp port 443
- ping traffic: icmp

If all of those fields match, then the first rule will either allow or deny.

The rule at the bottom with `*` is the **implicit deny**
This cannot be edited and is defaulted on each rule list.
If no other rules match the traffic being evaluated, it will be denied.

#### NACLs example below

- Bob wants to view a blog using https(tcp/443)
- We need a NACL rule to allow TCP on port 443.
- All IP communication has two parts
  - Initiation
  - Response
- Bob is initiating a connection to the server to ask for a webpage
- Server will respond with an **Ephemeral** port
- Bob talks to the webserver connecting to a port on that server (tcp/443)
  - This is a well known port number
- Bob's PC tells the server it can talk to back to Bob on a specific port
  - Wide range from port 1024, 65535
  - That response is outbound traffic
- When using NACLs, you must add an outbound port for the response traffic
as well as the inbound port. This is the ephemeral port.
- If the webserver is not managing the apps server, it may communicate
back on a different port.
- This back and forth communication can be hard to configure for.

#### NACL Exam PowerUp

- NACLs are stateless
- Initiation and response traffic are separate streams requiring two rules.
- NACLs are attached to subnets and only filter data as it crosses the
subnet boundary. Two EC2 instances in the same subnet will not check against
the NACLs when moving data.
- Can explicitly allow and deny traffic. If you need to block one particular
thing, you need to use NACLs.
- They only see IPs, ports, protocols, and other network connections.
No logical resources can be changed with them.
- NACLs cannot be assigned to specific AWS resources.
- NACLs can be used with security groups to add explicit deny (Bad IPs/nets)
- One subnet can only be assigned to one NACL at a time.

NACLs are processed in order starting at the lowest rule number until
it gets to the catch all. A rule with a lower rule number will be processed
before another rule with a higher rule number.

### Security Groups

- SGs are boundaries which can filter traffic.
- Attached to a resource and not a subnet.
- SGs have two sets of rules like NACLs.
- SGs are stateful.
  - Only one inbound rule is needed.
  - They see traffic and response as the same thing.
- Understand AWS logical resources so they're not limit to IP traffic only.
  - Can have a source and destination referencing the instance and not the IP.
- Default SG is created in a VPC to allow all traffic.
  - Does so by referencing itself. Anything this SG is attached to is matched
  by this rule.
- SGs have a hidden implicit **Deny**.
  - Anything that is not allowed in the rule set for the SG is implicitly denied.
- SG cannot explicit deny anything.
  - NACLs are used in conjunction with SGs to do explicit denys.

#### SGs vs NACL

- NACLs are used when products cannot use SGs, e.g. NAT Gateways.
- NACLs are used when adding explicit deny, such as bad IPs or bad actors.
- SGs is the default almost everywhere because they are stateful.
- NACLs are associated with a subnet and only filter traffic that crosses
that boundary. If the resource is in the same subnet, it will not do anything.

### Network Address Translation (NAT) Gateway

Internet Gateways are Region-resilient, but NAT Gateways are AZ-resilient.
- this means that **1 NAT Gateway is associated with only 1 subnet** and this subnet has to be **public** (i.e. has to be able to connect to Internet Gateway to allow for public IP)
- therefore we have to create **one NAT Gateway for each AZ we are operating in !!!!**

REMEMBER: one subnet can have 0-1 route tables, but 1 route table can be associated with 0-many subnets (One-To-Many relationship)

How do private subnet + NAT Gateway in a public subnet + Internet Gateway work together?
- 1) traffic originates from one of the **private subnets' devices** (it has to originate there, cannot originate in public internet)
- 2) the **private subnet** in which device resides has a **route table associated** - this route table routes traffic to a NAT Gateway
- 3) this NAT Gateway (one for each AZ) resides in a **public subnet** - a subnet that's associated with a route table that has a route to an Internet Gateway
- 4) therefore it uses this public subnet's route table to route traffic to Internet Gateway

NAT Gateway vs NAT Instance - **most important difference** which features on exams often is the security difference.
NAT Gateway **can only use NACL**, while NAT Instances **can use NACL or Security Groups attached to the EC2 instance**.

**NAT Gateways don't work with IPv6 !!!**. That's because inside AWS **all IPv6 addresses are publicly routable**.
The Internet Gateway works directly with IPv6 addresses.
- for IPv6, setting "::/0 Route" + Internet Gateway allows **bi-directional connectivity**
- for IPv6, setting "::/0 Route" + Egress-Only Internet Gateway allows **outbound only connectivity**
Set of different processes that can address IP packets by changing
their source or destination addresses.

**IP masquerading**, hides CIDR block behind one IP. This allows many IPv4
addresses to use one public IP for **outgoing** internet access.
Incoming connections don't work. Outgoing connections can get a response
returned.

- **Gives Private CIDR range outgoing internet access** - this means that private devices can initiate outgoing connection to public Internet and receive response data, **but connections cannot be initiated from the public internet to these private IP addresses.** This means NAT Gateways are **not bi-directional**.
- Must run from a public subnet to allow for public IP address.
- Internet Gateway subnets configure to allocate public IPv4 addresses
  and default routes for those subnets pointing at the IGW.
- Uses Elastic IPs (Static IPv4 Public)
  - Don't change
  - Allocated to your account
- AZ resilient service , but HA in that AZ.
  - If that AZ fails, there is no recovery.
- For a **fully region resilient service, you must deploy one NATGW in each AZ with a Route Table in each AZ with NATGW as target**.
- Managed service, scales up to 45 Gbps. Can deploy multiple NATGW to increase
bandwidth.
- AWS charges on usage per hour and data volume processed.

NATGW cannot do port forwarding or be a bastion server. In that case it might
be necessary to run a NAT EC2 instance instead.

---

## Elastic-Cloud-Compute-EC2

EC2 provides Infrastructure as a Service (IaaS Product)

### Virtualization 101

Servers are configured in three sections without virtualization.

- CPU hardware
- Kernel
  - Operating system
  - Runs in **privileged mode** and can interact with the hardware directly.
- User Mode
  - Runs applications.
  - Can make a **system call** to the Kernel to interact with the hardware.
  - If an app tries to interact with the hardware without a system call, it
  will cause a system error and can crash the server or at minimum the app.

#### Emulated Virtualization - Software Virtualization

Host OS operated on the HW and included a hypervisor (HV).
SW ran in privileged mode and had full access to the HW.
Guest OS wrapped in a VM and had devices mapped into their OS to emulate real
HW. Drivers such as graphics cards were all SW emulated to allow the process
to run properly.

The guest OS still believed they were running on real HW and tried
to take control of the HW. The areas were not real and only allocated
space to them for the moment.

The HV performs **binary translation**.
System calls are intercepted and translated in SW on the way. The guest OS needs
no modification, but slows down a lot.

#### Para-Virtualization

Guest OS are modified and run in HV containers, except they do not use slow
binary translation. The OS is modified to change the **system calls** to
**user calls**. Instead of calling on the HW, they call on the HV using
**hypercalls**. Areas of the OS call the HV instead of the HW.

#### Hardware Assisted Virtualization

The physical HW itself is virtualization aware. The CPU has specific
functions so the HV can come in and support. When guest OS tries to run
privileged instructions, they are trapped by the CPU and do not halt
the process. They are redirected to the HV from the HW.

What matters for a VM is the input and output operations such
as network transfer and disk IO. The problem is multiple OS try to access
the same piece of hardware but they get caught up on sharing.

#### SR-IOV (Singe Route IO virtualization)

Allows a network or any card to present itself as many mini cards.
As far as the HW is concerned, they are real dedicated cards for their
use. No translation needs to be done by the HV. The physical card
handles it all. In EC2 this feature is called **enhanced networking**.

### EC2 Architecture and Resilience

EC2 instances are virtual machines run on EC2 hosts.

Tenancy:

- **Shared** - Instances are run on shared hardware, but isolated from other customers.
- **Dedicated** - Instances are run on hardware that's dedicates to a single customer.
  Dedicated instances may share hardware with other instances from the same AWS account
  that are not Dedicated instances.
- **Dedicated host** - Instances are run on a physical server fully dedicated for your use.
  Pay for entire host, don't pay for instances.

- AZ resilient service. They run within only one AZ system.
  - You can't access them cross zone.

EC2 host contains

- Local hardware such as CPU and memory
- Also have temporary instance store
  - If instance moves hosts, the storage is lost.
- Can use remote storage, Elastic Block Store (EBS).
  - EBS allows you to allocate volumes of persistent storage to instances
within the same AZ.
- 2 types of networking
  - Storage networking
  - Data networking

EC2 Networking (ENI)

When instances are provisioned within a specific subnet within a VPC
A primary elastic network interface is provisioned in a subnet which
maps to the physical hardware on the EC2 host. Subnets are also within
one specific AZ. Instances can have multiple network interfaces, even within
different subnets so long as they're within the same AZ.

An instance runs on a specific host. If you restart the instance
it will stay on that host until either:

- The host fails or is taken down by AWS
- The instance is stopped and then started, different than restarted.

The instance will be relocated to another host in the same AZ. Instances
cannot move to different AZs. Everything about their hardware is locked within
one specific AZ.
A migration is taking a **copy** of an instance and moving it to a different AZ.

In general instances of the same type and generation will occupy the same host.
The only difference will generally be their size.

#### EC2 Strengths

Long running compute needs. Many other AWS services have run time limits.

Server style applications

- things waiting for network response
- burst or stead-load
- monolithic application stack
  - middle ware or specific run time components
- migrating application workloads or disaster recovery
  - existing applications running on a server and a backup system to intervene

### EC2 Instance Types

What's the difference between instance types?
- Raw CPU, Memory, local Storage capacity & type
- **resource ratios** (e.g. higher CPU/memory ratio or memory/CPU)
- storage and data network **bandwidth**
- system architecture / vendor (e.g. AMD, Intel)
- additional features and capabilities (e.g. graphic cards)


What are the type categories?
- **General Purpose** (T, M) - default steady state workloads with even resources
- **Compute Optimized** (C) - Media processing, scientific modeling and gaming
- **Memory Optimized** (R, X) - Processing large in-memory data sets
- **Accelerated Computing** (P, G, F) - Hardware GPU, FPGAs
- **Storage Optimized** (H, I, D) - Large amounts of super fast local storage.
  Massive amounts of IO per second. Elastic search and analytic workloads.

#### Naming Scheme

R5dn.8xlarge - whole thing is the instance type. When in doubt give the
full instance type

- 1st char: Instance family.
- 2nd char: Instance generation. Generally always select the newest generation.
- char after period: Instance size. Memory and CPU considerations.
  - Often easier to scale system with a larger number of smaller instance sizes.
- 3rd char - before period: additional capabilities
  - a: amd cpu
  - d: NVMe storage
  - n: network optimized
  - e: extra capacity for ram or storage

### Storage Refresher

- **Instance Store**
  - Block Storage (see below)
  - Direct (local) attached storage
  - Super fast
  - Ephemeral storage or temporary storage
- **Elastic Block Store (EBS)**
  - Block Storage (see below)
  - Network attached storage
  - Volumes delivered over the network
  - Persistent storage lives on past the lifetime of the instance

#### Three types of storage

- Block Storage: Volume presented to the OS as a collection of blocks. No
structure beyond that. These are mountable and bootable. The OS will
create a file system on top of this, NTFS or EXT3 and then it mounts
it as a drive or a root volume on Linux. Spinning hard disks or SSD. This
could also be delivered by a physical volume. Has no built in structure.
You can mount an EBS volume or boot off an EBS volume.
  - mountable
  - bootable

- File Storage: Presented as a file share with a structure. You access the
files by traversing the storage. You cannot boot from storage, but you
can mount it.
  - mountable
  - not bootable

- Object Storage: It is a flat collection of objects. An object can be anything
with or without attached metadata. To retrieve the object, you need to provide
the key and then the value will be returned. This is not mountable or
bootable. It scales very well and can have simultaneous access.
  - not mountable
  - not bootable

#### Storage Performance
These 3 things cannot exist in isolation:
- IO Block Size: Determines how to split up the data.
- IOPS (Input Output Per Second): How many reads or writes a system can accommodate per second.
- Throughput: End rate achieved, expressed in MB/s (megabyte per second).

`Block Size * IOPS = Throughput`

We need to pick the right size of IO Block Size and maximize the IOPS to achieve the highest Throughput.

Too large IO Block Size = the IOPS will decrease

### Elastic Block Store (EBS)
- Provides **network storage** for the EC2 service
- We use EBS to create **volumes** in an **Availability Zone**, and we can attach this volume to EC2 instances in **the same AZ**
- Everytime we launch EC2 instance, we're using EBS behind the scenes for the **boot volume** of that instance
- Allocate block storage (**volumes**) to instances.
- Volumes are isolated to one AZ.
  - The data is highly available and resilient for that AZ.
  - All of the data is replicated within that AZ. The entire AZ must have
  a major fault to go down.
- Two physical storage types available (SSD/HDD)
- Varying level of performance (IOPS, T-put)
- Billed as GB/month.
  - If you provision a 1TB for an entire month, you're billed as such.
  - If you have half of the data, you are billed for half of the month.
- Four types of volumes, each with a dominant performance attribute.
  - General purpose SSD (gp2)
  - Provisioned IOPS SSD (io1)
    - maximum IOPS such as databases
  - T-put optimized HDD (st1)
    - maximum t-put for logs or media storage
  - Cold HDD (sc1)


**SSD-based types of volumes focus on IOPS**
**HDD-based types of volumes focus on Throughput (MiB/s)**

#### SSD Volume Types: EBS - General Purpose SSD (gp2)

Uses a performance bucket architecture based on the IOPS it can deliver.
The GP2 starts with 5,400,000 IOPS allocated. It is all available instantly.

You can consume the capacity quickly or slowly over the life of the volume.
The capacity is filled back based upon the volume size.
Min of 100 IOPS added back to the bucket per second.

Above that, there are 3 IOPS/GiB of volume size. The max is 16,000 IOPS.
This is the **baseline performance**

Default for boot volumes and should be the default for data volumes.
Can only be attached to one EC2 instance at a time.

#### SSD Volume Types: EBS - Provisioned IOPS SSD (io1)

You pay for capacity and the IOPs set on the volume.
This is good if your volume size is small but need a lot of IOPS.

50:1 IOPS to GiB Ratio
64,000 is the max IOPS per volume assuming 16 KiB I/O.

Good for latency sensitive workloads such as mongoDB.
Multi-attach allows them to attach to multiple EC2 instances at once.

#### HDD Volume Types

- great value
- great for high throughput vs IOPs
- 500 GiB - 16 TiB
- **Neither can be used for EC2 boot volumes!!!!!!!!**
- Good for streaming data on a hard disk.
  - Media conversion with large amounts of storage.
- Frequently accessed high throughput intensive workload
  - log processing
  - data warehouses
- The access patterns should be sequential
  - Massive inefficiency for small reads and writes

Two types

- st1 (notice letter 't' - Throughput optimised)
  - Starts at 1 TiB of credit per TiB of volume size.
  - 40 MB/s baseline per TiB
  - Burst of 250 MB/s per TiB
  - Max t-put of 500 MB/s
- sc1 (notice letter 'c' - Cold Storage HDD)
  - Designed for less frequently accessed data, it fills slower.
  - 12 MB/s baseline per TiB
  - Burst of 80 MB/s per TiB
  - Max t-put of 250 MB/s

#### EBS Exam Power Up

- Volumes are created in an AZ, isolated in that AZ.
- If we want to migrate them into another AZ, we need to create **snapshots**. Snapshots are created in the same region where origin AZ is, but can be later copied to other regions. Therefore we can migrate our EBS volumes across AZs and Regions.
- To migrate across same region (from one AZ to another):
  - first, create snapshot of EBS volume 1
  - when the snapshot is ready, create a new EBS volume 2 **from this snapshot**
  - attach the EBS volume 2 to instance in it's AZ
- To migrate across different region - copy the snapshot to another region and then create a new EBS volume from it.
- If an AZ fails, the volume is impacted. **Snapshots help there** (Snapshots are backups of volume that are put in S3 - it makes them Region resilient)
- Highly available and resilient in that AZ. (**Replicates data to multiple storage platforms in one AZ**) The only reason for failure is
if the whole AZ fails.
- Generally one volume to one instance, except **io1** with multi-attach
- Has a GB/m fee regardless of instance state.
- EBS maxes at 80k IOPS per instance and 64k vol (io1)
- Max 2375 MB/s per instance, 1000 MiB/s (vol) (io1)

### EC2 Instance Store

- Local **block storage** attached to an instance.
- Physically connected to one EC2 host.
  - They are isolated to that one specific **host**.
  - **Instances on that host can access them**.
- Highest storage performance in AWS.
- Included in instance price, use it or lose it.
- Can be attached **ONLY at launch**. Cannot be attached later.

Each instance has a collection of volumes that are
locked to that specific host. If the instance moves, the data doesn't.

Instances can move between hosts for many reasons:

- If an instance is stopped and started, that migrates hosts.
- If a host undergoes AWS maintenance, it will be wiped.
- If you change the type of an instance, these will be lost.
- If a physical hardware fails, then the data is gone.

The number, size, and performance of instance store volumes vary based on the
type of instance used. Some instances do not have any instance store volumes
at all.

#### Instance Store Exam PowerUp

- Instance store volumes are local to EC2 host.
- Can only be added at launch time. Cannot be added later.
- Lost on instance **move**, **resize** or **hardware failure**
- **Highest data performance in all of AWS**.
  - more IOPS and Throughput vs EBS
- You pay for it anyway, it's included in the price.
- TEMPORARY - **we can't use Instance Store for anything that requires secure, persistent data**

### EBS vs Instance Store

If the read/write can be handled by EBS, that should be default.

When to use EBS

- **Highly available and reliable in an AZ**. Can self correct against HW issues.
- Persist independently from EC2 instances.
  - Can be removed or reattached.
  - You can terminate instance and keep the data.
- Multi-attach feature of **io1**
  - Can create a multi shared volume.
- **Region resilient backups**. (Snapshots - automated backups of volume to S3. For Instance Store there's no automated, AWS-native solution)
- Require **up to 64,000 IOPS and 1,000 MiB/s per volume** - remember for exam
- Require **up to 80,000 IOPS and 2,375 MB/s per instance** - remember for exam

When to use Instance Store

- Great value, they're included in the cost of an instance.
- **More than 80,000 IOPS and 2,375 MB/s per instance** - we can go into milions here
- If you need temporary storage, or can handle volatility.
  - e.g. database server with: boot in gp2, persistent store in io2, temporary store in Instance Store
- **Stateless** services, where the server holds nothing of value.
- Hard lifecycle link between storage and the instance.
  - This ensures the data is erased when the instance goes down.

### EBS Snapshots, restore, and fast snapshot restore

- Efficient way to backup EBS volumes to S3.
  - The data becomes region resilient.
- Can be used to **migrate data** between hosts ...
  - in the same AZ
  - in different AZ
  - in different region

Snapshots are incremental volume copies to S3.
The first is a **full copy** of `data` on the volume. This can take some time.
EBS won't be impacted, but will take time to copy in the background.
Future snaps are incremental, consume less space and are quicker to perform.

If you delete an incremental snapshot, it moves data to ensure subsequent
snapshots will work properly.

Volumes can be created (restored) from snapshots.
Snapshots can be used to move EBS volumes between AZs.
Snapshots can be used to migrate data between volumes.

#### Snapshot and volume performance

- When creating a new EBS volume without a snapshot, the performance is
available immediately.
- When restoring from S3, performs **Lazy Restore**
  - If you restore a volume, it will transfer it slowly in the background.
  - If you attempt to read data that hasn't been restored yet, it will
  immediately pull it from S3, but this will achieve lower levels of performance
  than reading from EBS directly.
  - You can force a read of every block all data immediately using 'dd' command - this will make sure the data is pulled ASAP, not when someone references it e.g. week later

### Snapshot and volume performance - FSR

Fast Snapshot Restore (FSR) allows for immediate restoration.
You can create 50 of these FSRs per region. When you enable it on
a snapshot, you pick the **snapshot specifically and the AZ** that you want to be
able to do instant restores to. Each combination of **Snapshot and AZ** counts
as **one FSR set**. You can have 50 FSR sets per region.
FSR is not free and can get expensive with lots of different snapshots.

#### Snapshot Consumption and Billing

Billed using a GB/month metric.
20 GB stored for half a month, represents 10 GB-month.

**This is used data, not allocated data.** If you have a 40 GB volume but only
use 10 GB, you will only be charged for the allocated data.
This is not how EBS itself works.
  - in case of EBS, we pay for the allocated data (which includes data that is in use at any time, but also the data that is not used)
  - in case of EBS snapshots, we only pay for the "used" data (i.e. the data that has been backed up)

The data is incrementally stored which means **doing a snapshot every 5 minutes will not necessarily increase the charge as opposed to doing one every hour.**
  - this means we can do snapshots frequently

**At each stage, a new Snapshot is only storing data inside itself (which is new or changed), and it's referencing previous snapshots for anything which isn't changed.**

#### EBS Encryption

Provides at rest encryption for block volumes and snapshots.

When you don't have EBS encryption, the volume is not encrypted.
The physical hardware itself may be performing at rest encryption, but
that is a separate thing.

When you set up an EBS volume initially, EBS uses KMS and a customer master key.
This can be the EBS default (CMK) which is referred to as `aws/ebs` or it
could be a customer managed CMK which you manage yourself.

That key is used by EBS when an encrypted volume is created. The CMK
generates an encrypted data encryption key which is stored on the volume with
the physical disk. This key can only be encrypted by KMS when a role with
the proper permissions makes the request.

When the volume is first used, EBS asks CMS to decrypt the key and stores
the decrypted key in **memory on the EC2 host** while it's being used. At all
times it's encrypted form is stored on the EBS volume.

When the EC2 instance is using the encrypted volume, it can use the
decrypted data encryption key to read and write data to the volume. It is used
for all cryptographic operations when data is being used to and from the
volume.

When data is stored at rest, it is stored as ciphertext.

If the EBS volume is ever moved, the decrypted key is discarded from the original Host's memory, leaving only the encrypted version on the physical storage of EBS volume.
Then if an instance wants to use this volume again, it has to decrypt the key using KMS and load it into it's new EC2 Host.

If a snapshot is made of an encrypted EBS volume, the **same data encryption key** is used for that snapshot. Any **volume** made from this snapshot is **by default also encrypted** in the same way, although we can pick **different CMK**.

Every time you create a new EBS volume from scratch, it creates a new data encryption key.

Such encryption doesn't cost anything, so **we should use it by default**.

##### EBS Encryption Exam Power Up

- AWS accounts can be set to encrypt all new EBS volumes by default (it's done in EC2 Dashboard - per region)
  - **it doesn't cost anything and doesn't cause any performance issues**
  - It will use the default CMK unless a different one is chosen.
  - Each volume uses 1 unique DEK (data encryption key)
  - Snapshots and future volumes made from that snapshot also use the same DEK
- Can't change a volume to NOT be encrypted.
  - You could mount an unencrypted volume and copy things over but you can't
  change the original volume.
- **The EC2 instance OS itself isn't aware of the encryption !!!!!!!, there is no performance loss.**
  - The volume itself is encrypted using AES256
  - This occurs between the EC2 host and the EBS system itself.
  - The OS does not see any encryption. It simply writes data out and reads
  data in from a disk.
- **If an exam question does not use AES256, or it suggests you need an OS to encrypt or hold the keys, then you need to perform full disk encryption at the operating system level.**
  - We can use full disk encryption on OS level, along with either EBS volume encryption or no EBS volume encryption - those are 2 separate things
  - in case of full disk encryption on OS level, there will be **CPU performance penalty**

### EC2 Network Interfaces, Instance IPs and DNS

An EC2 instance starts with at least one ENI - elastic network interface. This first interface is called Primary ENI.


An instance may have ENIs in separate subnets, but everything must be within one AZ.

When you launch an instance with e.g. Security Groups, they are attached to network interface and not the instance.



#### Elastic Network Interface (ENI)

**Each ENI has following properties**:

- MAC address
- Primary IPv4 **private** address
  - From the range of the subnet the ENI is within.
  - Will be static and not change for the lifetime of the instance
    - e.g. `10.16.0.10`
  - **Given a DNS name that is associated with the address**.
    - e.g. `ip-10-16-0-10.ec2.internal`
    - **Only resolvable inside the VPC and always points at private IP address**
- 0 or more secondary private IP addresses
- 0 or 1 public IPv4 address
  - The instance must manually be set to receive an IPv4 address or spun into a
subnet which automatically allocates an IPv4.
**This is a dynamic IP that is not fixed.**
  - If you stop an instance the address is removed.
  - When you start up again, it is given a brand new IPv4 address.
  - Restarting the instance will not change the IP address.
  - Changing between EC2 hosts will change the address.
  - This will be allocated a **public DNS name !!!!!!!!!!!**.
    - Outside of the VPC, the DNS will resolve to the public IP address.
    - Inside of the VPC, the DNS will resolve to the private IP address.
    - **!!!!!!!!!! This allows one single DNS name for an instance, and allows traffic to resolve to an internal address inside the VPC and the public will resolve to a public IP address.**
- 1 elastic IP per private IPv4 address
  - in contrary to regular public IPv4 addresses, **elastic addresses are static** and don't change
  - ... therefore **we are charged for them even if they're not attached to any instance**
  - If you use Elastic IP then you will always know what it is, as it stays the same, this helps in case you need to access services through the NAT inwards (forwarding).
  - Can associate with a private IP on the **primary interface** or **secondary interface**.
  - **in case of primary interface, if you are using a public IPv4 and assign an elastic IP, the original IPv4 address will be replaced with elastic IP**
      - **if you remove the elastic IP, a regular (dyniamic) public IP address will be reassigned, but it won't be the exact same one as before**
- 0 or more IPv6 address on the interface
  - These are by default public addresses.
- Security groups
  - Applied to network interfaces.
  - Will impact all IP addresses on that interface.
  - If you need different IP addresses impacted by different security
  groups, then you need to make multiple interfaces with these IP addresses being separated and apply different
  security groups to those interfaces.
- Source / destination checks
  - If traffic is on the interface, it will be discarded if it is not
  from going to or coming from one of the IP addresses

Secondary interfaces function in all the same ways as primary interfaces except
you can detach interfaces and move them to other EC2 instances.

#### ENI Exam PowerUp

- Legacy software is licensed using a mac address.
  - If you provision a secondary ENI to a specific license, you can move
around the license to different EC2 instances.
- Multi homed (subnets) management and data.
- **Different security groups can be attached to different interfaces**.
- **!!!!!!!!!! The OS doesn't see the IPv4 public address !!!!!!!!!!!!!!!!**
  - this is provided by process called "NAT" performed by the Internet Gateway
- You always configure the private IPv4 private address on the interface.
- Never configure an OS with a public IPv4 address.
- IPv4 Public IPs are Dynamic, starting and stopping will kill it.
- **Public DNS** for a given instance will resolve to the primary private IP
address inside VPC, and to public IP outside VPC. If you have instance to instance communication within
the VPC, it will never leave the VPC. It does not need to touch the Internet Gateway.

### Amazon Machine Image (AMI)

Images of EC2 instances that can be used to launch multiple EC2 instances with same configuration.

- When you launch an EC2 instance, you are using an Amazon provided AMI.
- Can be Amazon or community provided
- Marketplace (can include commercial software)
  - Will charge you for the instance cost and an extra cost for the AMI
- AMIs are regional with a unique ID.
- Controls permissions
  - Default only your account can use it.
  - Can be set to be public.
  - Can have specific AWS accounts on the AMI.
- Can create an AMI from an existing EC2 instance to capture the current config.

#### AMI Lifecycle

1. Launch: EBS volumes are attached to EC2 devices using block device IDs.

   - BOOT /dev/xvda
   - DATA /dev/xvdf

2. Configure: customize the instance from applications or volume sizes.

3. Create Image or AMI

    - AMI contains:
      - Permissions: who can use it, is it public or private
      - **EBS snapshots are created from attached EBS volumes**
        - **Snapshots are referenced inside the AMI using block device mapping**
        - Table of data that links the snapshot IDs that you've just
        created when making that AMI and it has for each one of those
        snapshots, a device ID that the original volumes had on the EC2
        instance (e.g. "snapshot-abc123 /dev/xvda \n snapshot-def456 /dev/xvdf")

4. Launch: When launching an instance, the snapshots are used to create new EBS
volumes in the AZ of the EC2 instance and contain the same block device mapping (i.e. /dev/xvda, /dev/xvdf)

#### AMI Exam PowerUps

- AMI can only be used in **one region**
  - **... but can be copied across regions (includes its snapshots) !!!**
- **AMI Baking**: creating an AMI from a configuration instance.
- An **AMI cannot be edited**. If you need to update an AMI, launch an instance,
make changes, then make **new AMI**
- Remember permissions **by default are your account only**
- Billing is for the **storage capacity for the EBS snapshots** the AMI references.

### EC2 Pricing Models

#### On-Demand Instances

- Hourly rate based on OS, size, options, etc
- Billed in seconds (60s min) or hourly
  - Depends on the OS
- Default pricing model
- No long-term commitments or upfront payments
- New or uncertain application requirements
- Short-term, spiky, or unpredictable workloads which can't tolerate disruption.

#### Spot Instances

- Up to 90% off on-demand, but depends on the spare capacity.
- You can set a maximum hourly rate in a certain AZ in a certain region.
- If the max price you set is above the spot price, you pay only that spot
price for the duration that you consume that instance.
- As the spot price increases, you pay more.
- Once this price increases past your maximum, it will terminate the instance.
- Useful for:
  - Apps that have flexible start and end times
  - Apps which only make sense at low cost
  - Apps which can **tolerate failure** and continue later
- Great for data analytics when the process can occur later at a lower use time.

#### Reserved Instance

- Up to 75% off on-demand.
- The trade off is commitment.
- You're buying capacity in advance for 1 or 3 years.
- Flexibility on how to pay
  - All up front
  - Partial upfront
  - No upfront

- Best discounts are for 3 years all up front.
- Reserved in region, or AZ with capacity reservation.
- Reserved instances take priority for AZ capacity.
- Can perform scheduled reservation when you can commit to specific time windows.

- Great if you have a known stead state usage, email usage, domain server.
- Cheapest option with **no tolerance for disruption**

### Instance Status Checks and Autorecovery

Every instance has two high level status checks

- **System Status Checks**
  - Failure on:
    - loss of system power
    - loss of network connectivity
    - **host** software issues
    - **host** hardware issues
- **Instance Status Checks**
  - Failure on:
    - Corrupted file system
    - Incorrect Instance Networking
    - OS Kernel Issue

Autorecovery can kick in and help:

- Recover this instance
  - can be a number of steps depending on the failure
  - all recovery steps are performed inside the AZ our instance resides in
  - ... this means that **this recovery process won't work if an AZ fails**
  - **doesn't support all instance types, only a subset of them - particularly newest generations**
  - **doesn't work on instances that only use Instance Store - we need to have EBS volume attached**
  - **very simple feature, not meant for recovery of large scale or complex systems**
- Stop this instance
- Terminate this instance
  - useful in a cluster
- Reboot this instance

### Horizontal and Vertical Scaling

#### Vertical Scaling

As customer load increases, the server may need to grow to handle more data.
The server can increase in capacity, but this will require a reboot.

- Each resize requires a reboot - **disruption**
- Often times vertical scaling can only occur during planned outages.
- Larger instances also carry a $ premium compared to smaller instances.
- Instance size is an upper cap on performance.
- **No application modification is needed**
  - if an application can run in an instance, it can run in a bigger instance
  - works for all applications, even monoliths (all code in one app)

#### Horizontal Scaling

As the customer load increases, this adds additional capacity.
Instead of one running copy of an application, you can have multiple versions
running on each server.


**This requires a load balancer.**
When customers try to access an application, the load balancer ensures the
servers get equal parts of the load.

- Sessions are everything.
- With horizontal scaling you may shift between instances
  - to cope with this, we need either application support or off-host sessions.
- Servers are **stateless**, the app stores session data elsewhere.
  - **No disruption while scaling up or down**
- No real limits to scaling.
- Uses smaller instances so you pay less, allows for better granularity.

### Instance Metadata

**Important topic - is often brought up on exams**
EC2 service provides data to EC2 Instances
Accessible inside ALL instances

**Memorize `http://169.254.169.254/latest/meta-data/` !!!!!!**
  - we can curl it from inside of EC2 Instances
  - e.g. to get a public IPv4 address of an instance, run: `curl http://169.254.169.254/latest/meta-data/public-ipv4`

Metadata contains information on:
- Environment
- Networking (e.g. instance's public IP address)
- Authentication
  - EC2 Instance can assume a Role and gain access to other AWS resources
- User-Data


We can also use **ec2-metadata** tool.
- to download it, run: `wget http://s3.amazonaws.com/ec2metadata/ec2-metadata`
- `./ec2-metadata --help` - list possible arguments
- `./ec2-metadata -a` - to get AMI (ami-id) used to launch the instance
- `./ec2-metadata -z` - to get AZ
- `./ec2-metadata -s` - to get Security Groups

**Metadata doesnt' require authentication and is not encrypted !!!!!**. Anyone who can gain access to the
instance can see the meta-data. This can be restricted by local firewall rules (blocking accesss to 169.254.169.254)

---

## Containers-and-ECS

### Intro to Containers

Virtualization Problems

Using an EC2 virtual machine with Nitro Hypervisor, 4 GB ram, and 40 GB disk,
the OS can consume 60-70% of the disk and much of the available memory.
Containers leverage the similarities of multiple guest OS by removing duplicate
resources. This allows applications to run in their own isolated environments.

#### Image Anatomy

A Docker image is composed of multiple layers and not a monolithic disk image.
Each line of a Docker image creates a new filesystem layer on top of the
previous. Images are created from scratch or a base image.
Images contain read only layers, images are layer onto images.

Docker container is the same as a Docker image, except it
has an additional READ/WRITE layer of the container.

If you have lots of containers with very similar base
structures, they will share the parts that overlap.
The other layers are reused between containers.

#### Container Registry

Registry or hub of container images.
Dockerfile can create a container image where it gets stored
in the container registry.

Docker hosts can run many containers based on one or more images.
A single image can generate Containers on many different Docker hosts.

#### Container Key Concepts

- Docker files are used to build Docker images
- Containers are portable and always run as expected.
  - Anywhere there is a compatible host, it will run exactly as you intended.
- Containers are lightweight, use the host OS for the heavy lifting.
  - File system layers are shared when possible.
- Containers only run the application and environment it needs to run.
- Ports need to be **exposed** to allow outside access from the host and beyond.
- Application stacks can be multi container

### Elastic Container Service (ECS) Concepts

- **supports only Docker standard !!!!!!!! i.e. no Cargo and others**
- Accepts containers and instructions you provide.
- ECS allows you to create a cluster.
  - Clusters are where containers run from.
- Container images will be located on a registry.
  - AWS provides ECR (elastic container registry)
  - Dockerhub can be used as well.
- **Container definition** gives ECS just enough info about the single container.
  - A pointer to which image to use and the ports that will be exposed.
- **Task definitions** store the resources used by the task.
  - Stores **task role**, an IAM role that allows the task access to other
  AWS resources.
- Task is not by itself highly available.



Benefits of containers:
- fast to startup
- portable
- lightweight

Containers are **not fully** isolated from other containers.


ECS **Service** is configured via Service Definition and represents
how many copies of a task you want to run for scaling and HA.

### ECS Cluster Types

ECS Cluster manages:

- Scheduling and Orchestration
- Cluster manager
- Placement engine


**ECS Cluster types:**
- Fargate (Network Only)
- EC2 Linux + Networking
- EC2 Windows + Networking

#### EC2 mode

ECS cluster is created within a VPC. It benefits from the multiple AZs that
are within that VPC.
You specify an initial size which will drive an **auto scaling group**.

ECS using EC2 mode is not a serverless solution, you need to worry about
capacity for your cluster.

The container instances are not delivered as a managed service, they
are managed as normal EC2 instances.
You can use spot pricing or prepaid EC2 servers.

#### Fargate mode

Removes more of the management overhead from ECS, no need to manage EC2.

**Fargate shared infrastructure** allows all customers
to access from the same pool of resources.

Fargate deployment still uses a cluster with a VPC where AZs are specified.

For ECS tasks, they are injected into the VPC. Each task is given an
elastic network interface which has an IP address within the VPC. They then
run like a VPC resource.

You only pay for the container resources you use.

#### EC2 vs ECS(EC2) vs Fargate

If you already are using containers, use **ECS**.

**EC2 mode** is good for a large workload if you are price conscious.
This allows for spot pricing and prepayment.

**Fargate** is great if you,

- Have a large workload but are overhead conscious.
- Have small or burst style workloads.
- Use batch or periodic workloads.

---

## Advanced-EC2

### Bootstrapping EC2 using User Data

Bootstrapping is a process where scripts or other config steps can be run when
an instance is first launched. This allows an instance to be brought to service
in a particular configured state.

In systems automation, bootstrapping allows the system to self configure.
In AWS this is **EC2 Build Automation**.

This could perform some software installs and post install configs.

Bootstrapping is done using **user data** and is injected into the instance
in the same way that meta-data is. It is accessed using the meta-data IP.

<http://169.254.169.254/latest/user-data>

Anything you pass in is executed by the instance OS **only once on launch**!
- **if we update user data and restart instance, it won't be executed again**

**EC2 doesn't validate the user data**. You can tell EC2 to pass in trash data
and the data will be injected. The OS needs to understand the user data.

#### Bootstrapping Architecture

An AMI is used to launch an EC2 instance in the usual way to create
an EBS volume that is attached to the EC2 instance. This is based on the
block mapping inside the AMI.

Now the EC2 service provides some user data through to the EC2 instance.
There is SW within the OS designed to look at the metadata IP for any user data.
If it sees any user data, it executes this on launch of that instance.

This is treated like any other script the OS runs. At the end of running
the script, the instance will either be in:

- Running state and ready for service.

or


- Bad config but still likely running.
  - The instance will probably still pass its checks.
  - It will not be configured as you expected.

#### User Data Key Points

- EC2 doesn't know what the user data contains, it's just a block of data.
- The user data is not secure, anyone can see what gets passed in. For this
reason it is important not to pass passwords or long term credentials.

- User data is limited to 16 KB in size. Anything larger than this will
need to pass a script to download the larger set of data.

- User data can be modified if you stop the instance, change the user
data, then restart the instance. This won't be executed since the instance
has already started.

#### Boot-Time-To-Service-Time

How quickly after you launch an instance is it ready for service?
This includes the time for EC2 to provision the instance and any software
downloads that are needed for the user.
When looking at an AMI, this can be measured in minutes.

Bootstrapping is about automating installation **after the launch** of the instance.
- AMI -> Instance -> Bootstrapping
- gives more configurability than AMI baking, but increases Post Launch Time (time needed for the installation/configuration to finish)


AMI baking will front load the time needed by configuring as much as possible.
- AMI baking -> AMI -> Instance
- gives less configurability than Bootstrapping, but decreases Post Launch Time. We need to AMI bake installation/configuration which don't change across our instances and don't need to be configured.


The optimal way is to combine both of these processes (bootstrapping and AMI baking)

### AWS::CloudFormation::Init

**cfn-init** is a helper script installed on EC2 OS.
This is much more complex than User Data.

- User Data is procedural and run by the OS line by line.
- cfn-init can be procedural, but can also be desired state.
  - Can specify particular versions of packages. It will ensure things are
  configured to that end state.
  - Can manipulate OS groups and users.
  - Can download sources and extract them using authentication.
- Handles packages, groups, users, sources, files, commands, services
- Provided with directives via **Metadata** section, directive **AWS::CloudFormation::Init** on a CFN resource
- **User Data can be run once (on launch), whereas cfn-init can be configured to watch for updates of metadata on an object in a template**
  - **... if that metadata changes, then cfn-init can be executed again and make changes to configuration**

This is executed as any other command by being passed into the instance as part
of the user data and retrieves its directives from the CloudFormation
stack and you define this data in the CloudFormation template called
`AWS::CloudFormation::Init`.

#### cfn-init explained

Starts off with a **CloudFormation template**.
This has a logical resource within it which is to create an EC2 instance.
This has a specific section called `Metadata`.
This then passes in the information passed in as `UserData`.
cfn-init gets variables passed into the user data by CloudFormation.

It knows the desired state and can work towards a final configuration.
This can monitor the user data and change things as the EC2 data changes.

#### CreationPolicy and Signals

If you pass in user data, there is no way for CloudFormation to know
if the EC2 instance was provisioned properly. It may be marked as complete,
but the instance could be broken.

A **CreationPolicy** is something which is added to a logical resource
inside a CloudFormation template. You create it and supply a timeout value.

This waits for a signal from the resource itself before moving to a create
complete state.

Signal can be sent using `/opt/aws/bin/cfn-signal -e $? --stack ... --resource ... --region ...`
- this line should be added after running `cfn-init` command
- both of these lines would be put in User Data

### EC2 Instance Roles

IAM roles are the best practice ways for services to be granted permissions.
EC2 instance roles are roles that an instance can assume and anything
running in that instance has the permissions that role grants.

Starts with an IAM role with a permissions policy.
IAM Role allows the **EC2 Service** to assume that role.

The **Instance Profile !!!!!** is the item that allows the permissions to get
inside the instance !!!

When you create an instance role in the console:
- an Instance Profile is created out of IAM Role with the same name
- then the **Instance Profile is attached to the Instance itself**


When IAM roles are assumed, you are provided temporary roles based on the
permission assigned to that role. These credentials are passed through
instance **meta-data**.

**EC2 and the secure token service ensure the Role credentials never expire !!!**
- the credentials are automatically renewed

Key facts

- Credentials are inside meta-data
  - iam/security-credentials/role-name
  - automatically rotated - always valid
  - Resources need to check the meta-data periodically
- **Should always use Roles compared to storing long term credentials (Access Keys)**
- CLI tools use Role credentials automatically
  - after the Role is attached to an instance, it can run aws commands without the need to run `aws configure`


How to create and use EC2 Role:
- go to IAM service
- click Create role
- pick "AWS service" as type of trusted entity
- pick "EC2" as use case
- pick access type, e.g. AmazonS3FullAcccess
- go to EC2 service
- right click on an instance, go to Instance Settings, then Attach/Replace IAM Role
- select previously created IAM Role
- now we can run e.g. `aws s3 ls`
  - if we run `curl http://169.254.169.254/latest/meta-data/iam/security-credentials` it will list of active Roles


### AWS System Manager Parameter Store (SSM Paramater Store)

Subproduct of System Manager service.

Passing secrets into an EC2 instance is bad practice because anyone
who has access to the meta-data has access to the secrets.

Parameter store allows for storage of **configuration** and **secrets**
- String, StringList, SecureString
- License codes, Database Strings, Full Configs, Passwords
- Hierarchies, Versioning
  - **Hierarchy means we set parameter names according to /... format, e.g. `/my-cat-app/dbstring`**
  - think of paramater names as filenames in a directory structure
- Plaintext and Ciphertext

Parameter Store:

- Can store license codes, database strings, and full configs and passwords.
- Allows for hierarchies and versioning.
- Can store plaintext and ciphertext.
  - This integrates with **kms** to encrypt passwords.
- Allows for public parameters such as the latest AMI parameter to be stored
and referenced for EC2 creating.
- Is a public service so any services needs access to the public sphere or
to be an AWS public service.
- Applications, EC2 instances, lambda functions can all request access to
parameter store.
- Tied closely to IAM, can use
  - Long term credentials such as access keys.
  - Short term use of IAM roles


Retrieve parameter and its value: `aws ssm get-parameters --names /my-cat-app/dbstring --profile iamadmin-general`
- will get json with Name, Type, Value, Version, LastModifiedDate, ARN
- encrypted value will be presented as encrypted
- if we use `--with-decryption`, then encrypted parameter values will be decrypted (same for command below)


Retrieve entire hierarchy: `aws ssm get-parameters-by-path --path /my-cat-app/ --profile iamadmin-general`
- will get json with all parameters in that hierarchy

### System and Application Logging on EC2

Remember:
- CloudWatch is for metrics
- CloudWatch Logs is a subset of CloudWatch, aimed at storing, managing and visualizing any logging data
- CloudWatch and CloudWatch Logs **cannot natively capture data inside an EC2 instance**

**CloudWatch Agent** is required for OS visible data. It sends this data into CW.


For CW to function, it needs **configuration and permissions** in addition
to having the CW agent installed.


The CW agent needs to know what information to inject into CW and CW Logs.

The agent also needs some permissions to interact with AWS.
- **This is done with an IAM role as best practice.**
- The IAM role has permissions to interact with CW logs.
- The IAM role is attached to the instance which provides the instance and
anything running on the instance, permissions to manage CW logs.

The data requested is then injected in CW logs.
There is one log group for each individual log we want to capture.
There is one log stream for each group for each instance that needs
management.

We can use **Parameter Store** to store the configuration for the CW agent as a parameter.

To use CW Agent and Logs:
- connect to EC2 Instance
- download CW Agent .rpm file
- install it
- attach IAM Role to the Instance
  - give it CloudWatchAgentServerPolicy and AmazonSSMFullAccess
- run Configuration Wizard for CW Agent (`sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard`)
  - should be fine with mostly default options in the wizard
  - provide log file paths (e.g. /var/log/httpd/error_log)
  - the config file will be located at `/opt/aws/amazon-cloudwatch-agent/bin/config.json`
  - ... but we can also set to store the config in SSM parameter store - by default this is set to true in the Config Wizard
  - the config .json file stored in SSM can be later used for future instances
- CW Agent expects dir to exist: /usr/share/collectd/ and file: /usr/share/collectd/types.db
- run the cloudwatch-agent-ctl with proper parameters (point to config stored in SSM)
- we can then go to CW -> Logs -> Log groups -> find our group (for each log file path, e.g. /var/log/httpd/error_log) and Log Stream inside it - logs will be there



### EC2 Placement Groups

#### Cluster Placement

Pack instances close together

Achieves the highest level of performance available with EC2.


If you launch with 9 instances and AWS places you in a place with capacity
for 12, you are now limited in how many you can add.
- Best practice is to launch all of the instances within that group at the
same time.


Cluster placements need to be part of the same AZ. Cluster
placement groups are generally the same rack, but they can even be the same
EC2 host.

All members have direct connections to each other. They can achieve
**10 Gbps single stream** vs 5 Gbps normally. Thanks to the close physical location, they also have the lowest
latency and max PPS (Packets per second) possible in AWS.

If the hardware fails, the entire cluster will fail.

##### Cluster Placement Exam PowerUp

- Clusters can't span multiple AZs. **ONE AZ ONLY !!!!!!!!!!**. The first AZ used will lock down the cluster.
- They can span VPC peers.
- Requires a supported instance type.
- Best practice is to use the same type of instance and launch all at once.
- This is the only way to achieve **10Gbps SINGLE stream !!!**, other data metrics
assume multiple streams.


#### Spread Placement

Keep instances separated

This provides the best resilience and availability.
Spread groups can span **multiple AZs**.
- distinct racks
- if a rack fails, it will only affect instances inside of it


Information will be put on distinct
racks with their own network or power supply. **There is a limit of 7 instances per AZ** The more AZs in a region, the more instances inside a spread placement
group.


##### Spread Placement Exam PowerUp

- Provides the highest level of availability and resilience.
  - **Each instance by default runs from a different rack - it has separated part of hardware**
- 7 instances per AZ is a hard limit.
- Not supported for dedicated instances or hosts.

Use case: small number of critical instances that need to be kept separated
from each other. **Several mirrors of an application**



#### Partition Placement

Groups (partitions) of instances spread apart

Designed for situations **when we have more than 7 instances per AZ**, but we still require ability to **separate those instances**

Partition Placement group can be created across multiple AZs in a Region.
- e.g. AZ-A and AZ-B
- **maximum of 7 partitions per AZ**
- each partition has its **own racks !!!!!!!!!!! - no sharing between partitions**
- 

If a problem occurs with one rack's networking or power, it will
at most take out one instance.


The main difference to Spread is you can launch **as many instances as we want** in each partition.

When you launch a partition group, you can allow AWS decide or you can
specifically decide.



##### Partition Placement Exam PowerUp

- 7 **partitions** maximum for each AZ
  - Spread is basically hardcoded 1 AZ per each partition (so limit of 7 instances per AZ still applies)
- Instances can be placed into a **specific partition, or AWS can pick**.
  - so we have an element of control
- This is not supported on Dedicated Hosts.
- Great for HDFS, HBase, and Cassandra


Example question:
If you run a large application which uses 100's of EC2 instances and it needs exposure to physical location for performance and availability reasons. Which placement group should you use.
- answer: Partition (each partition has own racks)



### EC2 Dedicated Hosts

- EC2 host allocated to you in its entirety.
- **Pay for the entire host** itself which is **designed for a family of instances**.
- **There are no instance charges.**
- You can pay for a host **on-demand** or **reservation with 1 or 3 year terms**.
- Host hardware has **physical sockets and cores**. This dictates how many instances can be run on the HW.

Hosts are designed for a specific size and family. If you purchase one host, you
configure what type of instances you want to run on it. With the **older VM**
system you **cannot mix and match**. The new **Nitro** system **allows for mixing and matching host size**.

#### Dedicated Hosts Limitations

- **AMI Limits**, some versions (RHEL, SUSE Linux, any Windows AMIs) can't be used
- **Amazon RDS** instances are not supported
- **Placement Groups** are not supported for dedicated hosts.
- Hosts can be shared with other organization accounts using **RAM** - Resource Access Management
- This is mostly used for software licensing problems (??? read more on that)

### Enhanced Networking

- Enhanced networking uses **SR-IOV**
  - **the physical network interface is aware of the virtualization !!!**
- Each instance is given exclusive access to one of the logical network cards created from physical network card

- There is **no charge** for this and is available on most EC2 types (modern ones mostly).
  - in many cases it's enabled by default
- It allows for **higher IO (Throughput)** and **Lower Host CPU** usage
- This provides more **Bandwidth** and higher PPS (Packets per second).
- Provides **consistent (!!!) lower latency**.
- for exam, we don't need to know how to implement it

#### EBS Optimized

Historically network on EC2 was shared with the same network stack used
for both data networking and EBS storage networking.

EBS optimized instance means that some **stack optimizations** have taken place
and **dedicated capacity** has been provided for that instance for EBS usage.

**Most** new instances **support this** and have this **enabled by default** for no charge.
- for older instance types, there is some support, but enabling costs extra

---

## Route-53

### Public Hosted Zones

- **R53 Hosted Zone** - a **DNS database** for a given section of global DNS data (e.g. animals4life.org)
- **R53 Public Hosted Zone** -a type of R53 Hosted Zone which is hosted on
R53-provided **public DNS name servers**.
- when creating a hosted zone, AWS provides
at least 4 DNS name servers which host the zone.

This is globally resilient service due to multiple DNS servers.

Hosted zones are created automatically when you register a domain using R53.

Hosted zones can be created separately. If you want to register a domain
elsewhere and use R53 to host the zone file and records for that domain, then
you can specifically create a hosted zone and point at an externally
registered domain at that zone.
There is a monthly fee to host each hosted zone within R53 and a fee for
any queries made to that service.

Hosted Zones are what the DNS system references via delegation and name server
records. A hosted zone, when referenced in this way by the DNS system, is known
as being **authoritative** for a domain.
It becomes the single source of truth for a domain.


How it works (R53 and Public Hosted Zones)?
- we have a DNS Client (device / server within the ISP) - they query `www.animals4life.org`
- they move through the DNS tree
- at some point DNS client gets the IP addresses of R53 Name Servers to use for this particular domain
  - so Client queries the R53 Name Servers looking for a particular record
  - there's at least 4 name servers distributed globally for each R53 domain
- the R53 Name Servers then locate the `animals4life.org` zone file/database, on which **Hosted Zone** is stored
  - this is just a database containing records
  - e.g. `A` records for www
- this data is accessed and passed back through the R53 Name Servers all the way back to DNS Client

### Route 53 Health Checks

- Route checks will allow for periodic health checks on the servers (e.g. if there's A record pointing to EC2 instance, R53 will use health checks to determine the health of that particular target).
  - If one of the servers has a bug, this will be removed from the list.
  - If the bug gets fixed, the health check will pass and the server will be
added back into a healthy state.


- **Health checks are separate from records**, but are used by them (records) inside R53.
  - You don't create health checks inside records themselves.
- Checks are performed by a fleet of global health checkers. If you think
they are bots and block them, this could cause alarms.

- Checks occur **every 30 seconds by default** (BUT THERE ARE MULTIPLE CHECKERS DOING THE CHECK). This can be increased to **10 seconds for additional costs.**
  - since there are multiple checkers (around 15) doing the same health check in 30s intervals, it means that on average a health check will be performed every 2-3 seconds
  - in case of 10s interval, this will result in more than 1 health check per second


Checks could be one of **three types**:
- **TCP checks**: R53 tries to establish TCP with end point within 10 seconds.
- **HTTP/HTTPS**: Same as TCP but within 4 seconds. The end point must respond
with a 200 or 300 status code within 3 seconds of checking.
- **HTTP/HTTPS with String Matching**: Same as above, the response body must have a string within the first
5120 bytes. The string is chosen by the user.

It will be deemed **healthy** or **unhealthy**.

There are three types of checks **architecture** (what will be monitored):
- Endpoint checks
- CloudWatch Alarm
- Checks of Checks (multiple calculated checks on application's components)

### Route 53 Routing Policies

- **Simple**: 
  - Route traffic to a single resource. 
  - Client queries the resolver which has **one record**. 
  - ... it will respond with **3 values** (IP addresses) and these get forwarded back to the client
  - ... the client then picks one of the three at **random**.
  - This is a **single record only. No health checks !!!**

- **Failover**: 
  - Create **two records** of the **same name and the same type**. 
    - One is set to be the primary and the other is the secondary. 
  - This is the same as the Simple policy except for the **response**. 
    - Route 53 knows the health of both instances. As long as the primary is healthy, it will respond with this one. If the health check with the primary fails, the secondary (backup) will be returned instead. 
  - This is set to implement **Active-Passive Failover**: one record is active (primary), other is passive (seconday)

- **Weighted**: 
  - Create **multiple records of the same name** within the hosted zone.
  - For each of those records, you provide a **weighted value**. 
    - The total weight is the same as the weight of all the records of the same name. 
    - If all of the parts of the same name are healthy, it will distribute the load based on the weight. 
    - If one of them fails its health check, it will be skipped over and over again until a good one gets hit. 
    - e.g. `www 'A' ip_address (90)` and `www 'A' ip_address2 (10)` - first is chosen 90% of time, second is chosen 10% of time
  - This can be used for **simple traffic distribution (load balancing)**,  **migration to separate servers by adjusting the weight over time**.

- **Latency-based**:
  - **Multiple records** in a hosted zone can be created with the same name and same type. 
    - for each record, we specify region to which it corresponds to
    - e.g. `www 'A' ip_address (us-east-1)`
  - When a client request arrives, it knows which **region the request comes from**.
    - It knows the **lowest latency** and will respond with the lowest latency record.

- **Geolocation**: 
  - Focused to delivering results matching the query of your customers. 
  - The record will first be matched **based on the country** if possible.
    - If this does not happen, the record will be checked based on the **continent**.
    - Finally, if nothing matches again it will respond with the **default** response as long as **default record exists** (otherwise no record will be sent back).
  - This can be used for **licensing rights**. If **overlapping** regions occur, the priority will always go to the **most specific or smallest region**. 
    - e.g. The US (country) will be chosen over the North America (continent) record.

- **Multi-value**: 
  - With multi-value, you can have **multiple records** with the **same name** and each of these records can have a health check. 
  - R53 using this method will respond to queries with any and all healthy records, but it **removes any records that are marked as unhealthy** from those responses. 
  - This **removes** the problem with simple routing where a **single unhealthy record can make it through to your customers**. 
  - Great alternative to simple routing when you need to improve the **reliability**, and it's an alternative to failover when you have **more than two records** to respond with, but don't want
the complexity or the overhead of weighted routing.

---

### Route 53 Private Hosted Zones

In contrary to Public Hosted Zones, we can also create Private Hosted Zones in R53.
- we choose what **VPC** they should be associated with
- these Hosted Zones will be accessible only from within the VPC they are associated with
- we can associate multiple VPCs with one Private Hosted Zone


Useful if we want to create a Hosted Zone accessible only within our company.
- e.g. our intranet


If we have overlapping records in both Public and Private Hosted Zones, the **private would take precedence over public**.
- for these records, we would have the same domain name
- but they would point to different IP addresses

---

### Route 53 Exam tips

- Question: Which type of recordset is generally used to point at AWS resources?
- Answer: A + Alias


Memorize the Routing Policies.

## Relational-Database-Service-RDS

### Database Refresher

Systems to store and manage data.

#### Relational (SQL)

- Structured Query Language (SQL) is a feature of most RDS.
- Structure to the data known as a **schema**.
  - Defined in advance.
  - Defines names of things
  - Valid values of things
  - Types of data which is stored and where
- Fixed relationship between tables.
  - This is defined before data is entered into the database.

Every row in a table must have a value for the **primary key**.
There must be a value stored for every attribute in the table.

SQL systems are relational so we generally define relationships between
tables as well. This is defined with a **join table**.
A join table has a **composite key** which is a key formed of two parts.
Composite keys together must be unique.

Keys in different tables are how the relationships between the tables
are defined.

The Table schema and relationships must be defined in advance which can be
hard to do.

#### Non-Relational (NoSQL)

Not a single thing, and is a catch all for everything else.
There is generally no schema or a weak one.

##### Key-Value databases

This is just a list of keys and value pairs.
So long as every key is unique, there is no real schema or structure needed.
These are really fast and highly scalable.
This is also used for **in memory caching**.


In **exam**, Key-Value databases could be chosen as solution for:
- **simple requirements**
- data, which is just **names and values**, or pairs, or keys and values
- **no structure** necessary
- scalable, really fast

##### Wide Column Store

DynamoDB is an example of wide column store database.

Each row or item has one or more keys.
One key is called the partition key.
You can have additional keys other than the partition key called the
sort or range key.

It can be **single key** (only partition key) or **composite key**
(partition key and sort key).


- example: **DynamoDB**


**Each set of single/composite keys has to be unique**

Every item in a table can also have attributes, but **they don't have to be the same between values**.
- **any item can have any attribute** (or even no attribute at all)
- but **every item has to use the same key structure**, and the key has to be **unique**


Advantages:
- very fast, super scalable
- as long as we don't need SQL commands, it's great for large scale projects

##### Document

Documents are generally formatted using JSON or XML.

This is an extension of a key-value store where each document is interacted
with via an ID that's unique to that document, but the value of the document
contents are exposed to the database allowing you to interact with it.

Good for order databases, or collections, or contact stale databases.

Great for nested data items (deep attribute interactions) within a document structure such as catalogs, user profiles.

##### (SQL) Row Store Database (MySQL)

We interact with data based on rows.

Often called OLTP (Online Transactional Processing Databases).
- as name suggests, they're perfect for systems which perform transactions
  - e.g. order databases, contact databases

If you needed to read the price of one item you need that
row first. If you wanted to query all of the sizes of every order, you will
need to check for each row.

Great for things which deal in rows and items where they are constantly
accessed, modified, and removed.

##### Column Store Database (Redshift)

Instead of storing data in rows on disk, they store it based on columns.
The data is the same, but it's grouped together on disk, based on
column so every order value is stored together, every product item, color,
size, and price are all grouped together.

This is **VERY VERY INEFFICIENT for transactional style processing**, but great for **reporting-style querying !!!** or when
all values for a specific column are required.
- e.g. collect data of all sizes (all `size` column values) to check which size was sold the most


Example: Redshift
- Redshift is a **data-warehousing product**
  - the name gives it away
- generally we would take data from Row Store database, shift it into Column Store database and perform **reporting or analytics !!!!!**

##### Graph

- Relationships between things are **formally defined** and stored along in the **database itself** with the data.
- They are **not calculated each and every time you run a query**.
- These are great for relationship driven data.
  - e.g. social media sites

Nodes are objects inside a graph database. They can have properties.

Edges are relationships between the nodes. They have a direction.

Relationships themselves can also have attached data, so name value pairs.
We might want to store the start date of any employment relationship.

Can store massive amounts of complex relationships between data or between
nodes in a database.

Is much faster compared to Relational Databases, because in Relational Databases the relations have to be retrieved and computed. Whereas in Graph databases the relations are stored directly with data, and the related data can be retrieved right away.

Exam:
- when implementing social media
- when implementing complex relationships


### Databases on EC2

It is always a bad idea to do this.

- Splitting an instance over different AZs
  - Adds reliability consideration between the AZs
  - Adds a cost to move the data between AZs

#### Reasons EC2 Database might make sense

- Need access to the OS of the Database.
  - You should question if a client requests this, it rarely is needed.
- Advanced DB Option tuning (DBROOT)
  - AWS provides options to tune many of these parameters anyways.
  - Can be a vendor that is asking for this.
- DB or DB version that AWS doesn't provide.
- You might need a specific version of an OS and DB that AWS doesn't provide.

#### Reasons why you really shouldn't run a database on EC2

- **Admin overhead** is intense to manage the EC2 host.
- Backup and Disaster Management adds complexity.
- EC2 is running in one AZ. If the zone fails, access to the database fails.
- Will miss out on features from AWS DB products.
- EC2 is ON or OFF, there is no way to scale easily.
- **Replication** can be tricky to manage on your own.
- Performance will be slower than other AWS options.

### Relational Database Service (RDS)

- Database-as-a-service (DBaaS)
  - Not entirely true more of DatabaseServer-as-a-service.
  - Managed Database Instance for one or more databases.
- No need to manage the HW or server itself.
- Handles engines such as MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL.

Amazon Aurora. This is so different from normal RDS, it is a separate product.

#### RDS Database Instance

Runs one of a few types of database engines and can contain multiple
user created databases. Create one when you provision the instance, but
multiple ones can be created after.

When you create a database instance, the way you access it is using a database
host-name, a CNAME, and this resolves to the database instance itself.

RDS uses standard database engines so you can access an RDS instance using the
same tooling as if you were accessing a self-managed database.

The database can be optimized for:
- db.m5 general
- db.r5 memory
- db.t3 burst

There is an associated size and AZ selected.

When you provision an instance, you provision dedicated EBS storage to that instance.
- This EBS storage is located in the same AZ.
- RDS is vulnerable to failures in that AZ.


The storage can be allocated with SSD (io1, gp2) or magnetic.
- io1 - lots of IOPS and consistent low latency
- gp2 - same burst pool architecture as it does on EC2, used by default
- magnetic - compatibility mostly for long term historic uses


Billing is per instance and hourly rate for CPU and memory. You are also billed for storage allocated.

**Rules for RDS Instance in a VPC !!!**:
- the VPC we use for our RDS Instance must have **at least two subnets**
- these subnets must be in two different AZs
- if we want our RDS Instance to be publicly accessible, we need to enable VPC attributes: **DNS hostnames** and **DNS resolution**
- **DB subnet group** is a collection of subnets (typically private) that we create in a VPC and that we then designate for our DB instances
- each DB subnet group should have **subnets in at least two AZs** (to avoid losing everything on one AZ failure)
- if a primary DB instance of a Multi-AZ deployment fails, Amazon RDS can promote the corresponding **standby in another AZ** and then create a new standby using an IP address of the subnet in one of the other AZs
- when Amazon RDS creates a DB instance in a VPC, **it assigns a network interface to our DB instance by using an IP address from our DB subnet group !!!**
  - however, we should use DNS name to connect to our database because the underlying IP address changes during failover


How it works (migration from self-managed EC2 database server to RDS)?
- go to **RDS**
  - go to **Subnet Groups** & create new DB Subnet Group
    - select your VPC
    - in subnet section, pick all subnets that you have created for DB purposes (i.e. one subnet in each AZ)
  - go to **Databases** & create new Database
    - pick database server (e.g. MariaDB)
    - pick template (Production, Dev/Test, Free tier)
    - set username and password
    - pick instance size
    - pick allocated storage size (e.g. 20 GiB), check if "Enable storage autoscaling" is enabled if it's needed
    - set Multi-AZ deployment if needed (creates a standby instance in a **different AZ**) - this is not covered under free tier
    - select your VPC
    - under "Additional connectivity configuration", pick the Subnet Group created in previous step
    - we can set if the instance will be publicy accessible or not (default is no)
  - once Database is created:
    - go into details -> Security Groups -> edit SG
      - **by default, it will only allow traffic on DB's port for one source: our public IP (one we're logged in to AWS console)**
      - edit inbound rules
      - remove our public IP, instead set "Custom" and pick our VPC Security Group. Save changes
    - go into details and save the Endpoint (URL)
- SSH into our EC2 Instance
  - make a dbdump of database
  - connect to new RDS Instance (using the URL copied previously) and put dbdump in it
  - change configuration of your app to use this RDS Instance
- now we can get rid of EC2 database server


### RDS Multi AZ (High-Availability)

This is an option that you can enable on RDS instances.
Secondary hardware is allocated inside another AZ. This is referred to as
the standby replica or standby replica instance. The standby replica has
its own storage in the same AZ as it's located.

RDS enables synchronous replication from the primary instance to the
standby replica.

**RDS Access ONLY via database CNAME**. **The CNAME will point at the primary instance. You cannot access the standby replica for any reason via RDS.**


The standby replica cannot be used for extra capacity.

**Synchronous Replication** means:

1. Database writes happen.
2. Primary database instance commits changes.
3. **Same time as the write is happening, standby replication is happening**.
4. Standby replica commits writes.

If any error occurs with the primary database, AWS detects this and will
failover within 60 to 120 seconds and change to the new database.

This **does not provide fault tolerance** as there will be some impact during change.

#### RDS Exam PowerUp

- Multi-AZ feature is not free tier, **extra infrastructure for standby**.
  - **Generally two times the price**.
- The **standby replica cannot be accessed directly unless a fail occurs**.
- Failover is highly available, not fault tolerant.
- **Same region only** (others AZ in the VPC).
- Backups are taken from standby which removes performance impacts.
- Failover can happen for a number of reasons.
  - Full AZ outage
  - Primary RDS failure
  - Manual failover for testing
  - If you change the type of a RDS instance, it will failover as part of
  changing that type.

### RDS Backup and Restores


https://aws.amazon.com/rds/faqs/
- Q: What is the difference between automated backups and DB Snapshots?

**Automated backups are enabled by default. DB Snapshots are made manually !!!**


RPO - Recovery Point Objective !!!

- **Time between the last backup and when the failure occurred**.
- Amount of maximum data loss.
- The lower the value the better, but more expensive
- Influences technical solution and cost.
- Business usually provides an RPO value.

RTO - Recovery Time Objective !!!

- Time between the disaster recovery event and full recovery.
- The lower the value the better, but more expensive
- Influenced by process, staff, tech and documentation.

***RDS Backups**
- occur from the only instance if we have Single-AZ
- occur from the standby instance if we have Multi-AZ (Primary instance is never used for backups)

First snapshot is full copy of the data used on the RDS volume. From then on,
the snapshots are incremental and only store the change in data.

When any snapshot occurs, there's a brief interruption to the flow of data
between the compute resource and the storage. If you are using single AZ, this
can impact your application. If you are using Multi-AZ, the snapshot occurs
on the standby replica.

**Manual snapshots don't expire even after removal of RDS Instance, you have to clean them yourself !!!**


Automatic Snapshots can be configured to make things easier.
- In addition to automated backup, every 5 minutes database transaction logs are
saved to S3. 
- Transaction logs store the actual data which changes inside a
database so the actual operations that are executed. 
  - Backups are restored and transaction logs are replayed to bring DB to
desired point in time.
- This allows a database to be restored to a point in time often with 5 minute granularity.
- **therefore RPO = 5 minutes for Automatic Snapshots !!!**

Retention of automatic backups can be anywhere from 0 to 35 days.
This means you can restore to any point in that time frame.
This will use both the snapshots and the translation logs.

When you delete the database, they can be retained but they will expire
based on their retention period.

The only way to maintain backups is to create a final snapshot which will not
expire automatically.

#### RDS Backup Exam PowerUp

- **When performing a restore, RDS creates a BRAND NEW RDS with a new endpoint address !!!!!**
- When restoring a manual snapshot, you are setting it to a single point
in time. This influences the RPO value.
- Automated backups are different, they allow any 5 minute point in time.
- Backups are restored and transaction logs are replayed to bring DB to
desired point in time.
- **Restores aren't fast, think about RTO**.

### RDS Read-Replicas

Kept in sync using **asynchronous replication**
- term **asynchronous replication should always be associated with Read-Replicas !!!**
- term **synchronuous replication should always be associated with Multi-AZs (Standby replicas) !!!**

It is written fully to the primary and standby instance first.
Once its stored on disk, it is then pushed to the replica.
**This means there could be a small lag.**


**These can be created in the same region or a different region. !!!!!!!!!**
- This is known as **cross region replication**. AWS handles all of the
encryption, configuration, and networking without intervention.

#### Why do Read Replicas matter

READ performance

- 5 direct read-replicas per DB instance.
- Each of these provides an additional instance of read performance.
- This allows you to scale out read operations for an instance.
- Read-replicas can chain, but lag will become a problem.
- Can provide global performance improvements.
- Provides global resilience by using cross region replication.
- **Snapshots and backups don't help much with RTO**

- **Read Replicas provide near 0 RPO and low RTO, because they can be promoted quickly !!!**

- If the primary instance fails, you can promote a read-replica to take over.
- Once it is promoted, it allows for read and write.
- Promotion only happens when there's a failure
  - Keep in mind, Read-replicas will also replicate data corruption.
  - if it happens, you can't use them for recovery
  - In this case you must default back to snapshots and backups.
- **Promotion cannot be reversed !!!**
  - we have to delete it and create a new Read-Replica
- Global availability improvements, global resilience

### Amazon Aurora

Aurora architecture is VERY different from RDS !!!.

It uses a **cluster** which is:

- A single primary instance and 0 or more replicas.
- The replicas within Aurora can be used for reads during normal operation.
  - Provides benefits of RDS multi-AZ and read-replicas.
- Aurora doesn't use local storage for the compute instances.
  - An Aurora cluster has a shared cluster volume.
  - Provides faster provisioning.
  - Improved availability.
  - Better performance.

Aurora cluster functions across a number of availability zones.

There is a primary instance and a number of replicas.
The read queries from applications can use the replicas.

There is a shared storage of **max 64 TiB** across all replicas (compared to 16 TiB for non-Aurora) !!!!!.
**Cluster volume uses 6 storage replicas across multiple AZs**.

All instances have access to these storage nodes. This replication
happens at the storage level. No extra resources are consumed during
replication.

By default the primary instance is the only one who can write. The replicas
will have read access.

Aurora automatically detect hardware failures on the shared storage. If there
is a failure, it immediately repairs that area of disk and
recreates that data with no corruption.

**With Aurora you can have up to 15 replicas and any of them can be a failover target**. The failover operation will be quicker because
it doesn't have to make any storage modifications.

- Cluster shared volume is based on SSD storage by default.
  - Provides high IOPS and low latency.
  - No way to select magnetic storage.
- Aurora cluster does not specify the amount of storage needed.
  - This is based on what is consumed.
- High water mark billing or billed for the most used.
  - Storage which is freed up can be re-used.
  - If you reduce a lot of storage, you will need to create a brand new
  cluster and migrate data from the old cluster to the new cluster.
- Storage is for the cluster and not the instances which means Replicas can be
added and removed without requiring storage, provisioning, or removal.

#### Aurora Endpoints

Aurora clusters like RDS use endpoints, so these are DNS addresses which
are used to connect to the cluster. Unlike RDS, Aurora clusters have
multiple endpoints that are available for an application.

Minimum endpoints

- **Cluster endpoint** always points at the primary instance.
  - This is used for read and write applications.
- **Reader endpoint**
  - Will point at primary instance if that is all there is.
  - Will load balance across all available replicas for read operations.
  - Additional replicas which are used for reads will be load balanced
  automatically.

Custom endpoints can be created as well, pointing at particular primary/read-replica instances.
- **Therefore Aurora allows much more customization in terms of endpoints compared to regular RDS !!!**

#### Costs

- No free-tier option
- Aurora doesn't support micro instances
- **Beyond RDS singleAZ (micro) Aurora provides best value**
- Compute is billed per second with a 10 minute minimum.
- Storage is billed using the high watermark for the lifetime using GB-Month.
  - Additional IO cost per request made to the cluster shared storage.
- 100% DB size in backups are included for free.
  - 100 GB cluster will have 100 GB of storage for backups as part of what we pay

#### Aurora Restore, Clone and Backtrack

- Backups in Aurora work in the same way as RDS.
- **Restores** create a brand new cluster.
  - similar to how restoring a snapshot creates a new RDS Instance

- **Backtrack** must be enabled on a per cluster basis. 
  - This allows you to roll back your database to a previous point in time. This helps for **data corruption**.
  - You can adjust the window backtrack will work for.

- **Fast clones** make a new database much faster than copying all the data.
  - It references the original storage and only write the differences between the two. 
  - It uses a tiny amount of storage and only stores data that's changed in the clone or changed in the original after you make the clone.

### Aurora Serverless

Provides a version of Aurora database product without managing the resources.
You still create a cluster, but it uses ACUs or Aurora Capacity Units.

For a cluster, you can set a min and max ACU based on the load and can even
go down to 0 to be paused. In this case you would only be billed for storage
consumed.

Billing is based on resources used on a per-second basis.

Same resilience as Aurora (6 copies across AZs).

ACUs are stateless and shared across many AWS customers and have no local
storage. They can be allocated to your Aurora Serverless cluster rapidly
when required. Once ACUs are allocated to a cluster, they have access to cluster
storage in the same way as an Aurora Provisioned cluster.

There is a shared proxy fleet. When a customer interacts with the data
they are actually communicating with the proxy fleet. The proxy fleet
brokers an application with the ACU and ensures you can scale in and out
without worrying about usage. This is managed by AWS on your behalf.

#### Aurora Serverless - Use Cases

- Infrequently used applications.
  - Low volume blog site.
  - You only pay for resources as you consume them on a per second basis.
- New applications with unpredictable workloads.
- Great for variable workloads such as sales cycles.
It can scale in and out based on demand
- Good for development and test databases, can scale back when not needed.
- Great for multi-tenant applications.
  - Billing a user a set dollar amount per month per license.
  - If your incoming load is directly tied to more revenue this makes sense.

### Aurora Global Database

Introduces the idea of secondary regions with up to 16 read only replicas.
Replication from primary region to secondary regions happens at the storage
layer and typically occurs within one second.

- Great for cross region disaster recovery and business continuity.
- Global read scaling
  - Low latency performance improvements for international customers.
- The application can perform read operations against the read replicas.
- There is ~1s or less replication between regions.
- It is one way replication.
- No additional CPU usage is needed, it happens on the storage layer.
- Secondary regions can have 16 replicas.
  - All can be promoted to Read or Write in a DR situation.
- Maximum of 5 secondary regions.

### Aurora Multi-Master Writes

Allows an aurora cluster to have multiple instances capable of reads and writes.

Single-master Mode

- one R/W and zero or more read only replicas
- Cluster endpoint is normally used to write
- Read endpoint is used for load balancing

Aurora Multi-master has no endpoint or load balancing. An application
can connect with one or both of the instances inside a multi-master
cluster.

When one of the R/W nodes receives a write request from the application, it
immediately proposes that data be committed to all of the storage notes in that
cluster. At this point, each node that makes up a cluster either confirms
or rejects the proposed change. It will reject if this conflicts with something
already in flight.

The writing instance is looking for a bunch of nodes to agree. If the group
rejects it, it cancels the write in error. If it commits, it will replicate
on all storage nodes in the cluster.

This also ensures storage is updated on in-memory cache's of other nodes.

If a writer goes down in a multi-master cluster, the application will shift
all future load over to a new writer with little if any disruption.

### Database Migration Service (DMS)

A managed database migration service.
Starts with a replication instance which runs on top of an EC2 instance.
This replication instance runs one or more replication tasks.
This is where the configuration is defined for the migration of databases.
This runs using a replication instance.

Need to define the source and destination endpoints.
These point at the physical source and target databases.
One of these end points must be on AWS.

Full load migration is a one off process which transfers everything at once.
This requires the database to be down during this process. This might
take several days.

Instead Full Load + CDC allows for a full load transfer to occur and it
monitors any changes that happens during this time. Any of the captured
changes can be applied to the target.

CDC only migration is good if you have a vendor solution that works quickly
and only changes need to be captured.

Schema Conversion Tool or SCT can perform conversions between database types.

---

## Network-Storage-EFS

### EFS Architecture

EFS moves the instances closer to being stateless.

- **runs inside VPC**
- EFS is an implementation of NFSv4
- EFS file systems are created and mounted in Linux.
- EFS storage exists separately from an EC2 instance like EBS does.
  - EBS is block storage
  - EFS is file storage
- Media can be shared between many EC2 instances.
- EFS is a **private service**.
  - Isolated to the VPC its provisioned into.
  - Access is via mount targets inside the VPC.
- EFS access outside of the VPC with
  - VPC peering
  - VPN connections
  - AWS direct connect

#### Elastic File System Explained

EFS runs inside a VPC. Inside EFS you create file systems and these use POSIX
permissions. EFS is made available inside a VPC via mount targets.
Mount targets have IP addresses taken from the IP address range of the
subnet they're inside. **For HA, you need to make sure that you put mount targets in each AZ the system runs in.**

You can use hybrid networking to connect to the same mount targets.

#### EFS Exam PowerUp

- EFS is **Linux Only**
- Two performance modes:
  - **General purpose** is good for latency sensitive use cases.
    - General purpose should be default for 99.9% of uses.
  - **Max I/O performance** mode can scale to higher levels of aggregate t-put
  and IOPS but it does have increased latencies.
- Two t-put modes:
  - Bursting works like GP2 volumes inside EBS with a burst pool.
  **The more data you store in the FS, the better performance you get**.
  - Provisioned t-put modes can specify t-put requirements separately from size.
- Two storage classes available:
  - Standard
  - IA - Infrequent Access
  - Can use lifecycle policies to move data between classes.

---

## HA-and-Scaling

### Load Balancing Fundamentals

Using one server is risky because that server can have performance issues
or be completely unavailable, thus bringing down an application.

A better solution is to use multiple servers.
Without load balancing, this could bring additional problems.

- The servers can end up with uneven load.
  - Some requests take more CPU than others.
- Failed instances will still show up in DNS cache.
  - Due to TTL values, a user can be directed toward a dead server.

#### Load Balancers Architecture

The user connects to a load balancer that is set to listens on port 80 and 443.

Within AWS, the configuration for which ports the load balancer listens on is
called a **listener**.

**The user is connected to the load balancer and not the actual server !!!**

Behind the load balancer, there is an application server.
At a high level when the user connects to the load balancer, it distributes
that load to servers on the application server. The users client thinks it is
talking directly to the application server.

LB will run health checks against all of the servers. If one of the servers
does fail, the load balancer will realize this and stop sending connections
to that server. From the users client, the application always works.

As long as 1+ servers are operational, the LB is operational.
Clients shouldn't see errors that occur with one server.

#### LB Exam PowerUp

- Clients connect to the Load Balancer
  - specifically to the **listener** of the Load Balancer
- The load balancer connects to one or more **targets** or servers.
- Two connections in play.
  - Listener connection: one connection between the client and listener.
  - Backend connection: one connection between load balancer and target.
- The LB abstracts the client away from individual servers.
- Used for high availability, fault tolerance, and scaling

### Application Load Balancer (ALB)

ALB is a layer 7 or Application Layer Load Balancer.
It is capable of inspecting data that passes through.
It can understand the application layer `http` and `https` and
take actions based on things in those protocols like paths, headers,
and hosts.

Capacity that you have as part of an ALB increases automatically based
on the load which passes through that ALB. This is made of multiple ALB nodes
each running in different AZs. This makes them scalable and highly available.

Load balancing can be **internet facing** or **internal.**
The difference is whether the nodes of the LB, the things which
run in the AZs have public IP addresses or not.
- Internet facing LB is designed to be connected to, from public internet based
clients, and load balance them across targets.
- Internal load balancer is not accessible from the internet and is used to load
balance inside a VPC only.

Load balancer sits between a client and one or more servers.
Front end or listening side, accepts connections from a client.
Back end is used for distribution to the targets.

LB billed on hourly rate and **Load Balancer Capacity Unit** (LCU).
LCU that you consume is based on the highest value for all of the
individual measurements. You pay a certain number of LCUs based on your
load over that hour.

#### ALB health checks

- can check protocols: HTTP or HTTPS
- can specify port
- can specify path (e.g. /path?query)


#### Cross zone load balancing

Each node (which reside in an AZ) that is part of the load balancer is able to distribute load
across all instances across all AZ that are registered with that LB,
even if its not in the same AZ. It is the reason we can achieve a balanced
distribution of connections behind a load balancer.

It can also provide health checks on the target servers.
If all instances are shown as healthy, it can distribute evenly.

ALB can support a wide array of targets. Targets are grouped within **target groups** and an individual target can be a member of multiple groups !!!. 
- **It's the groups which ALBs distribute connections to**. 
- You could create rules to direct traffic to different Target Groups based on **host rules** and **path rules**.
  - host rules use different **DNS names**
  - path rules can interpret different paths in the HTTP address

#### ALB Exam PowerUp

- Targets are one single compute resource that connections are connected
towards.
- Target groups are groups of targets which are addressed using rules.
- Rules are
  - path based `/cat` or `/dog`
  - host based if you want to use different DNS names.
- Support EC2, EKS, Lambda, HTTPS, HTTP/2 and Websockets.
- ALB can use SNI (Server Name Indication) for multiple SSL certs attached to that LB.
  - LB can direct individual domain names using SSL certs at different target
  groups.
- AWS does not suggest using Classic Load Balancer (CLB), these are legacy.
  - This can only use one SSL certificate.

### Launch Configuration and Templates

They are documents which allow you to config an EC2 instance in advance.
Anything you usually define at the point of launching an instance can be
selected with a Launch Configuration (LC) or Launch Template (LT).


Key concepts:
- allow you to define the configuration of an EC2 instance **in advance**
  - AMI, Instance Type, Storage, Key pair
  - Networking and Security Groups
  - Userdata & IAM Role
- both LC and LT are **NOT editable** - defined once
  - you define them once and that configuration is locked
  - if you need to adjust a configuration, you must make a new one and launch it
- LT has versions
- LT provide **newer features** (everything that LC provide plus more)
  - T2/T3 Unlimited
  - Placement Groups
  - Capacity Reservations
  - Elastic Graphics
- LC have **one use** - they are used as part of **Autoscaling Groups !!! EXTREMELY IMPORTANT**
  - cannot be used to directly launch instances
  - not editable, no versioning
- LT also can be used for **Autoscaling Groups !!!**
  - but they can **also be used to provision EC2 instances from the console UI or CLI** (in contrary to LC)
- we define VPC for a LT, but not subnet
  - LT is WHAT, subnet is WHERE
  - we choose subnet when we launch LT, or subnet is chosen by autoscaling groups


### Autoscaling Groups

- **Automatic Scaling** and **Self-Healing** for EC2
- **They make use of LCs or LTs to know what to provision**
- Autoscaling group uses one LC or one version of a LT which it's linked with.
- Three values to control
  - minimum
  - desired
  - maximum

Provision or terminate instances to keep at the **desired** level.
- **Scaling Policies** automaticcaly adjust the **Desired Capacity** between the MIN and MAX values

Autoscaling Groups will distribute EC2 instances to try and keep the AZs equal.
- i.e. if there are 3 subnets and desired capacity is 3, then most likely Autoscaling Groups will provision one EC2 instance in each subnet

#### Scaling Policies

- **Manual Scaling** - manually adjust the desired capacity
- **Scheduled Scaling** - time based adjustments
- **Dynamic Scaling**
  - Simple: If CPU is above 50%, add one to capacity
  - Stepped: If CPU usage is above 50%, add one, if above 80% add three - bigger +/- based on difference. Usually preferred over Simple
  - Target: Desired aggregate CPU = 40%, ASG will handle this

**Cooldown Period** is how long to wait at the end of a scaling action before
scaling again. There is a minimum billable duration for an EC2 instance.
Currently this is **300 seconds**.

**Self Healing** occurs when an instance has failed and AWS provisions a new
instance in its place. This will fix most problems that are isolated to one
instance.

Autoscaling Groups can work together with Load Balancers
- ASG instances are automatically added to or removed from the Load Balancer's target group
- AGS can use the Load Balancer **health checks** rather than EC2
  - those health checks can be much richer than EC2 health checks, i.e. do HTTP/HTTPS requests (**Application Awareness**)


- **Autoscaling Groups are free**, only billed for the resources deployed.
- **Always use cooldown periods** to avoid rapid scaling.
- Try and use **more smaller instances** to allow **granularity**.
- You should use **Application Load Balancer** with **Autoscaling Groups**
- Autoscaling Groups defines **WHEN** and **WHERE**, Launch Template / Configuration defines **WHAT** !!!


#### Demo - Elastic Wordpress Architecture

- Using:
  - Launch Template: describe EC2 Instances which will be provisioned by Autoscaling Groups
  - Autoscaling Group: upsize/downsize instances according to load
  - Load Balancer: route traffic from clients
  - infrastracture used in previous demos (VPC, EFS for static webapp contents, RDS for app database)

How to tackle this:
1. first, implement Launch Template
2. then, implement the ASG
3. finally, add a Load Balancer and integrate it with ASG


When doing it in CloudFormation, we'd need to have LoadBalancer implemented before Autoscaling Groups.
- that's because we want to configure Autoscaling Groups to launch instances into Target Group (which is a part of LoadBalancer, in which we specify our VPC's public subnets)
- once Target Group is created as a part of LoadBalancer (in fact it's Listener), then we can setup our Autoscaling Groups to use this Target Group

### Network Load Balancer (NLB)

Part of AWS Version 2 series of load balancers.
NLBs are Layer 4, only understand **TCP and UDP** !!!.

Can't interpret HTTP or HTTPs, but this makes it much faster in latency.
**If you see anything about latency and HTTP and HTTPS are not involved, this should default to a NLB**.

There is nothing stopping NLB from load balancing on HTTP just by routing data.
They would do this really fast and can deliver millions of requests per second.

Only member of the load balancing family that can be provided a static IP.
There is 1 interface per AZ. **Can also use Elastic IPs (whitelisting) and should be used for this purpose.**!!!
- if we see whitelisting IPs or static IPs in relation to load balancing, then we need to use Network Load Balancer !!!

Can perform SSL pass through.

**NLB can load balance non HTTP/S applications, doesn't care about anything above TCP/UDP** !!!
- it will work with Layer 4 or below
- this means it can handle load balancing for e.g. FTP

### SSL Offload and Session Stickiness

There are three ways Load Balancer can perform **secure connections**:
- Bridging
- Pass-through
- Offload

#### Bridging - Default mode of Application Load Balancer

**Client** ---secure-https--> **ELB (decrypts, analyses, encrypts again - needs SSL cert)** ---secure-https--> **EC2 Instances (decrypts - needs SSL cert and compute resources)**

- One or more clients makes one or more connections to a load balancer.
- The load balancer is configured so its **listener** uses HTTPS, SSL connections occur between the client and the load balancer.

- The load balancer then needs an **SSL certificate** which matches the domain name that the application uses. 
  - this is because SSL connections are decrypted (terminated) on the Load Balancer itself
  - decryption (termination) means that the SSL wrapper is removed from the unencrypted HTTP

- ... then Load Balancer initiates a **new SSL connection** to backend instances
  - this means it re-encrypts the HTTP with a secure SSL wrapper and deliver it to the EC2 instances
  - therefore instances also need **SSL certificates**, as well as the **compute** required for **cryptographic operations**

- AWS has access to this certificate.
  - If you need to be careful of where your certificates are stored, you may have a problem with this system.


The application local balancer requires a SSL certificate because it needs
to decrypt any data that's being encrypted by the client. Once decrypted, it
will interpret it then create new encrypted sessions between it and the back
end EC2 instances. The EC2 instance will need matching SSL certificates.

Needs the compute for the cryptographic operations. Every EC2 instance must
perform these cryptographic operations. This overhead can be significant.

The main benefit is the ELB gets to see the unencrypted HTTP and can take actions based on what's contained in this plain text protocol.

#### Pass-through - Network Load Balancer

**Client** ---secure-https--> **ELB (Listener configured for TCP, doesn't decrypt/encrypt, doesn't need SSL cert)** ---secure-https--> **EC2 Instances (decrypts - needs SSL cert and compute resources)**


- The client connects, but the load balancer passes the connection along **without decrypting the data at all.**
  - therefore Load Balancer doesn't need SSL certificates
  - the instances still need the SSL certificates
- With this architecture, there is **no certificate exposure to AWS**
  - all self-managed and secured


Specifically it's a **Network Load Balancer** which is able to perform this style of connection.

- The **Listener** is configured for **TCP**, it can see the source or destinations, but it never touches the encrypted connection. The certificate never
needs to be seen by AWS.

Negative is you **don't get any load balancing based on the HTTP part**
because that is never exposed to the load balancer. The EC2 instances
**still need the compute cryptographic overhead**.

#### Offload

**Client** ---secure-https-->  **ELB (decrypts, analyses, DOESN'T ENCRYPT AGAIN - needs SSL cert)** ---UNSECURE-HTTP--> **EC2 Instances (no need to decrypt)**

- Clients connect to the load balancer using **HTTPS** and **are terminated on the load balancer**.
- The LB needs an SSL certificate to decrypt the data, but **to the backend the data is sent via HTTP, not HTTPS !!!**. 
  - so the connections are never encrypted again

- From customer perspective, the data is **encrypted between them and the Load Balancer**
  - but in transit from the LB to EC2 instances it's in **plaintext form**

While there is a certificate required on the load balancer, this is not needed on the EC2 instances.


#### Connection Stickiness

- If there is no stickiness, each time the customer logs on they will have a stateless experience. 
  - **If the state is stored on a particular server, sessions can't be load balanced across multiple servers.**

- There is an option available within ELBs called **Session Stickiness**.
  - Within an application load balancer this is enabled on a **Target Group**. 
  - if enabled, the first time a user makes a request, the load balancer generates a cookie called AWSALB with a duration we set
  - A valid duration is between **one second and seven days**. 
  - **For this time, sessions will be sent to the same backend instance**. 
  - ... this will happen until one of two things occur:
    - A **server failure**, then the user will be moved to a different server.
    - The cookie **expires**, the whole process will repeat and will receive a new cookie

- the problem with **Session Stickiness** is this could cause **uneven load on backend servers**
  - one user will always be forced to the same server no matter what the distributed load is. 
  - **that's why, if possible, applications should be designed to hold Session Stickiness somewhere other than EC2 !!!**

---

## Serverless-and-App-Services

### Architecture Evolution

#### Monolithic

- Fails together.
  - One error will bring the whole system down.
- Scales together.
  - Everything expects to be running on the same compute hardware
- Bills together.
  - All components are always running and always incurring charges.

This is the least cost effective way to architect systems.

#### Tiered

- Different components can be on the same server or different servers.
- Components are coupled together because the endpoints connect together.
- Can adjust the size of the server that is running each application tier.
- Utilizes **load balancers** in between tiers
  - allows **horizontal scaling**
- Tiers are still tightly coupled.
  - Tiers expect a response from each other. If one tier fails, subsequent
  tiers will also fail because they will not receive the proper response.
  - Back loads in one tier will impact the other tiers and customer experience.
- Tiers must be operational and send responses even if they are not processing
anything of value otherwise the system fails.

#### Queues

- Data no longer moves between tiers to be processed and instead uses a queue.
  - Often are **FIFO** (first in, first out)
- Data moves into a S3 bucket.
- Detailed information is put into the next slot in the queue.
  - Tiers no longer expect an answer.
- Upload tier sends an **async** message.
  - The upload tier can add more messages to the queue.
- **The queue will have an Autoscaling Group to increase/decrease processing capacity according to the Queue length !!!**
- The Autoscaling Group will only bring up servers as they are needed.
- The queue has the location of the S3 bucket and passes this onto the
processing tier.

#### Event Driven Architecture

- Event producers
  - Interact with customers or systems monitoring components.
  - Produce events in reaction to something.
  - Clicks, events, errors, actions
- Event consumers
  - Pieces of software waiting for events to occur.
  - Actions are taken and the system returns to waiting
- Services can be producers and consumers at once.
- Resources are not waiting around to be used.
- **Event Router** is needed for event driven architecture
  - has an **Event Bus**
  - Event Router decides which consumers to deliver events to and when that occurs, the actions are taken & **the system returns to waiting (doesn't consume resources)**
- **Only consumes resources while handling events !!!**

### AWS Lambda

- Function-as-a-service (FaaS)
  - key component of **serverless** architecture
  - Service accepts functions.
  - functions are executed and we're billed for duration of that execution
- **Event driven invocation** (execution) based on an **event** occurring.
- **Lambda function** is piece of code in one language.
- Lambda functions use a **runtime** (e.g. Python 3.6)
- Runs in a **runtime environment**.
  - Virtual environment that is ready to go to run code in that language.
  - You are billed only for the duration a function runs.
  - There is no charge for having lambda functions waiting and ready to go.

#### Lambda Architecture

- Best practice is to make it **very small** and **very specialized**
- Lambda function code, when executed is known as being **invoked**.
  - When invoked, it runs **inside a runtime environment** that matches the language the script is written in.
- The runtime environment is allocated a certain amount of memory and an appropriate amount of CPU. 
- The **more memory you allocate, the more CPU it gets**, and the **more the function costs** to invoke per second.

- Lambda functions can be given an **IAM role**, in this case it's called **Execution Role** (similar how there's Instance Role for EC2)
  - The execution role is passed into the runtime environment.
  - Whenever that function executes, the code inside has access to whatever permissions the role's permission policy provides.


- Lambda can be invoked in an **event-driven** or **manual** way.
  - Each time you invoke a lambda function, the environment provided is new.
  - **Never** store anything inside the runtime environment, it is ephemeral.

- Lambda functions by default are **public services** and can **access any websites**
  - By default they **cannot access private VPC resources**, but **can be configured to do so** if needed. 
  - Once configured, they can only access resources within a VPC.
  - Unless you have configured your VPC to have all of the infra/configuration needed
to allow resources to have public internet access or access to the AWS public space endpoints, then
the Lambda will not have that access.

- The Lambda runtime is **stateless**, so you should **always use AWS services for input and output**
  - e.g. DynamoDB or S3
  - If a Lambda is invoked by an event, it gets details of the event given to it at startup.

- Lambda functions can run **up to 15 minutes**. That is the **max limit**!!!!
  - if the computation is less than 15 minutes, default to using Lambda

#### Key Considerations

- Currently **15 min execution limit**
- Assume **each execution** gets a **new runtime environment**
- Use the **Execution Role** which is assumed when the function is executed
- Always **load data in and out of other services** (public APIs or AWS services)
- **Free tier: 1M requests and 400,000 GB-seconds of compute per month**
- Really **cost-effective**!!!

### CloudWatch Events and EventBridge

Delivers near real time stream of system events that describe changes in AWS
products and services. EventBridge will replace CW Events.
EventBridge can also handle events from third parties. Both share the same
underlying architecture. AWS is now encouraging a migration to EB.

#### CloudWatch Events and EventBridge Key Concepts

They can observe if X happens at Y time(s), do Z.

- X is a supported service which is a producer of an event.
- Y can be a certain time or time period.
- Z is a supported target service to deliver the event to.


- EventBridge is basically CloudWatch Events V2 that uses the same underlying APIs and has the same architecture, but with additional features.
  - Things created in one can be visible in the other for now.

- Both systems have a default **Event Bus** for a single AWS account.
  - A bus is a stream of events which occur for any supported service inside an AWS account.
- **In CW Events, there is only one bus (implicit), this is not exposed.**
- **CW EventBridge can have additional event buses for your applications or third party applications and services.**
  - These can be interacted with in the same way as the default bus.

- In both services, you create **rules**
  - these rules pattern match events which occur on the buses
  - when they see an event which matches, they deliver that **event** to a **target**. 
  - Alternatively you can have **schedule** based rules which match a certain date and time or ranges of dates and times.


- Architecturally at the heart of **Event Bridge** is the Default Account **Event Bus**.
  - This is a **stream of events** generated by supported services within the AWS account. 
  - Rules are created and these are linked to a specific **Event Bus** or the default Event Bus. 
  - Once the rule completes pattern matching, the rule is executed and moves that event that it matched through to one or more targets.
- **The events themselves are JSON structures and the data can be used by the targets.**

### Application Programming Interface API Gateway

- A way applications or services can communicate with each other.
- API gateway is an AWS managed service.
  - Provides **managed API Endpoint Service**.
  - Can also perform authentication to prove you are who you claim.
  - You can create an API and present it to your customers for use.
  - it's heavily used in Serverless architectures
- **Allows you to Create, Publish, Monitor, and Secure APIs ... as a service**
- Billed based on:
  - number of API calls
  - data transferred
  - additional performance features such as caching
- **Serve as an entry point for serverless architecture**
- If you have on premises legacy services that use APIs, this can be integrated.

Great during an architecture evolution because the endpoints don't change.

1. Create a managed API and point at the existing monolithic application.
2. Using API gateway allows the business to evolve along the way slowly.
This might involve moving some of the data to Microservice architecture, e.g. Fargate and Aurora.
3. Move to a full Serverless architecture with e.g. Lambda, DynamoDB.

### Serverless

- This is **not one single thing**
  - it's more about software than hardware
  - you manage few if any servers - **low overhead**
- related to Microservice architecture and Event-Driven architecture
- applications are a collection of small and specialized **functions that do one thing really well and then stop**

- these functions are **stateless** and run in **ephemeral environments**
  - every time they run, they obtain the data that they need, they do something and then optionally, they store the result persistently somehow or deliver the output to something else.

- generally, everything is Event Driven: **nothing is running until it's required**
  - while not being used, there should be little to no cost.

- should use **managed services** when possible
  - shouldn't re-invent the wheel
  - e.g. stick to S3 for any persistent object storage

- aim is to:
  - consume **as a service** whatever you can
  - **code as little as possible**
  - use **FaaS** for any general purpose compute needs
  - then use all of those building blocks together to create your application.

#### Example of Serverless

- A user wants to upload videos to a website for transcoding.
  - starting position is that we don't use any self-managed services

1. User browses to a S3-hosted static website running the Upload service. The JS runs directly from the web browser
2. Third party auth provider, Google in this case, authenticates via **token**.
3. AWS cannot use tokens provided by third parties. **Cognito** is called to
swap the third party token (Google) for AWS temporary credentials.
4. Service uses these temporary credentials to upload a video to S3 bucket.
5. Bucket will **generate an Event** once it has completed the upload.
6. A lambda function is triggered by the Event, and creates jobs in the **Elastic Transcoder service**. The
Elastic Transcoder, as part of the initation of the job, will get the original S3 bucket video location and will use
this for its workload.
7. Outputs (e.g. different resolution vids made from the original vid) will be added to a new transcode bucket and will put an entry into
DynamoDB.
8. User can interact with another Lambda function to pull the URL of the media from the
transcode bucket using the DynamoDB entry.


No self-managed services (e.g. EC2 instances) were used. The architecture is Serverless, but...
- generally, **API Gateway** would be used **between any client-side processing** and the **Lambda functions**

### Simple Notification Service (SNS)

- HA, Durable, **PUB/SUB** messaging service. (**remember that PUB/SUB == SNS !!!**)
- Public AWS service meaning to access it, you need network connectivity
with the Public AWS endpoints.
- Coordinates sending and delivering of messages up to 256KB in size.
  - Messages are not designed for large binary files.
- SNS **topics** are the base entity of SNS.
  - Permissions are controlled and configuration for SNS is defined.
- **Publisher** sends messages to a **topic**.
  - **Topics** have **subscribers** which receive messages.
- **Subscribers** receive all of the messages sent to the Topic.
  - Subscribers can be HTTP and HTTPS endpoints, emails, or **SQS queues**.
  - Filters can be applied to limit messages sent to subscribers.
- **SNS + SQS Fanout** allows for a single SNS topic with **multiple SQS queues as subscribers**.
  - Can create multiple related workflows.
  - Allows multiple SQS queues to process the workload in slightly different
  ways.

Offers:

- Delivery **Status** including HTTP, Lambda, SQS
- Delivery **Retries** which ensure **reliable delivery**
- HA and Scalable (**Regional**)
- SSE (server side **encryption**)
- Topics can be used **cross-account** via resource policy (**Topic Policy**)

### AWS Step Functions

- **Lambda** is FaaS
  - it runs small, very specific functions
  - we should never put whole apps in Lambda (bad practice, 15 minute execution limit)
  - gets messy at scale
  - runtime environments are **stateless**
- There are many problems with Lambda limitations that can be solved with a **State Machine**. 
  - a State Machine is a **workflow**. 
  - it has a **start point**, **end point** and in between there are **states**. 
    - states are things inside a State Machine which can do things. 
    - States can do things, and take in data, modify data, and output data.

- State machine is designed to perform an **activity or workflow** with **lots of individual components** and maintain the idea of data **between those states**
- Maximum duration for a state machine execution is **1 year**.

- **Two types** of workflow
  - **Standard**
    - Default
    - 1 year workflow
  - **Express**
    - Designed for IOT or other **high transaction uses**
    - **5 minute workflow**
    - Provides **better processing guarantees**

- Started via API Gateway, IOT Rules, EventBridge, Lambda. 
- Generally used for **backend processing**.

- With State machines you can use a **template** to create and export State Machines once they're configured to your liking
  - it's called **Amazon States Language** or **ASL**. It's based on JSON.

- State machines are provided **permission** to interact with other AWS services via **IAM roles**.

#### Step Function States

States are the things inside a workflow, the things which occur. START -> **STATES** -> END

- Succeed or Fail
  - the process will succeed or fail.
- Wait
  - will wait **for** a certain period of time
  - will wait **until** specific date and time
- Choice
  - different path is determined **based on an import**
- Parallel
  - will create parallel branches based on a choice
- Map
  - accepts a list of things
  - for each item in that list, performs an action or set of actions based on
  that particular item.
- Task
  - represents a single unit of work performed by the State Machine.
  - it allows the state machine to actually do things.
  - can be integrated with many different services such as lambda, AWS batch,
   dynamoDB, ECS, SNS, SQS, Glue, SageMaker, EMR, and lots of others.

### Simple Queue Service (SQS)

Public service that provides fully managed highly available message queues.

- Replication happens within a region by default.
  - Messages are guaranteed in the order they were received
  - Provides FIFO with best effort, but no guarantee
- Messages up to 256KB in size.
  - Should link to larger sets of data if needed.
- Polling is checking for any messages on the queue.
- **Visibility timeout**
  - The amount of time a client has to process a message in some way
  - When a client polls and receives messages, they aren't deleted from the
  queue and are hidden for the length of this timeout.
  - This is the amount of time that a client can wait to work on the messages.
  - If the client does not delete the message by the end, it will reappear in
  the queue.
- **Dead-letter queue**
  - if a message is received multiple times but is unable to be finished, this
  puts it into a different workload to try and fix the corruption.
- ASG can scale and lambdas can be invoked based on queue length.
- types of Queues:
  - **Standard Queue**
    - multi-lane highway
    - **guarantee at least once-delivery**
    - **no guarantee** on the **order** of that delivery
    - can scale to near infinite level (use for **decoupling super high volume apps!!!**)
  - **FIFO Queue**
    - single lane road with no way to overtake
    - **guarantee** the **order**
    - **guarantee** **exactly once delivery**
    - 3,000 messages p/s with batching or up to 300 messages p/s without


**Important for exam!!!: SNS + SQS Fanout**
- scenario: object being uploaded to an S3 bucket, and there's requirement for spawning **multiple jobs** to process that object
- S3 buckets are capable of generating an event when an object is uploaded to that bucket
  - but it can only generate **one event**!!!
- in order to take that one event and create multiple different events, we'd need to use **SNS + SQS Fanout design**
  - **SNS + SQS Fanout design** - taking one single SNS topic with multiple subscribers (generally multiple SQS queues) and then this message will be added into each of these **queues**
  - this would allow for **multiple jobs** to be started **per object upload**



Billed on **requests** not messages. A request is a single request to SQS.
One request can return 0 - 10 messages up to 64KB data in total.
Since requests can return 0 messages, frequently polling a SQS Queue, makes it
less effective.

Two ways to **poll**

- short (immediate) : uses 1 request and can return 0 or more messages. If the
queue is empty, it will return 0 and try again.

- long (waitTimeSeconds) : it will wait for up to 20 seconds for messages
to arrive on the queue. It will sit and wait for messages if none currently exist.
  - **we should use long polling, because it uses fewer requests = less expensive**



**Important for exam!!!: what services are commonly used within serverless architectures?**
- API Gateway
- Lambda
- Step Functions
- S3

trick answer: Kinesis is not commonly used with serverless architectures


---
- Messages can live on SQS Queue for up to 15 days. 
- They offer **KMS encryption at rest** (server side encryption)
- Data is **by default encrypted in transit** with SQS and any clients.

- Access to a queue is based on identity policies (**Queue Policy**). 
  - Queue policies only can allow access from an outside account. This is a resource policy.

### Kinesis

- Scalable **streaming** service. 
  - It is designed to inject data from lots of devices or lots of applications.
  - you can ingest **real-time data** such as video, audio, application logs, website clickstreams, and IoT telemetry data
  - ... for machine learning, analytics, and other applications.
  - enables you to process and analyze data as it arrives and **respond instantly instead of having to wait until all your data is collected** before the processing can begin
- Producers **send** data into a Kinesis **Stream**.
  - Stream is the basic entity of Kinesis
  - Stream can scale from low level of throughput to near infinite data rates.
- **Highly available**, **public service** by design.
- **Persistence** - Streams store a **24-hour** moving window of data by default (**similar to how dashboard cameras** store a window of video footage, with the oldest being removed, and the newest appended)
  - Can be increased to 7 days.
  - Data 24 hours + 1s is replaced by new data entering the stream.
- Kinesis includes the storage costs within it for the amount of data
that can be ingested during a 24 hour period. However much you ingest during
24 hours, that's included.
- **Multiple consumers can access data from that moving window.**
  - One might look at data points once per hour
  - Another looks at data once per minute.
- Kinesis Stream starts with 1 shard and **expands as needed**.
  - if **performance of Kinesis Stream** has to be improved, **Stream Shards have to be adjusted!!!** (i.e. more stream shards)
  - Each shard can have 1MB/s for ingestion and 2MB/s consumption.

- **Kinesis Data Record (1MB)** - stored across shards and are the blocks of data for a stream.

- **Kinesis Data Firehose** 
  - **connects** to a Kinesis **Stream**. 
  - it can **move the data** from a **stream** onto **S3 or another service**

### SQS vs Kinesis

**Make sure you know following differences between Kinesis and SQS for the exam !!!**

When answering exam questions, we need to ask ourselves:
- **is the exam question about the INGESTION of data?**
  - if it's about ingestion, e.g. large throughput or large number of devices, it's likely to be Kinesis
- **... or is it about worker pools DECOUPLING?**\
  - most likely SQS, unless strong reason for Kinesis
- **... or does it mention ASYNCHRONOUS communication?**
- most likely SQS, unless strong reason for Kinesis


Kinesis

- Large throughput or large numbers of devices
- **Huge scale** of data ingestion with multiple consumers
  - each of the consumers might be consuming data at different rates, either real time or periodically, and move backwards and forwards through time
- Rolling window for multiple consumers
- Designed for **data ingestion, analytics, monitoring, app clicks !!!**

SQS

- 1 production group, 1 consumption group
  - generally 1 thing/group sending messages to the queue, and 1 thing/group reading from the queue
- designed for **decoupling** application components, allow for **asynchronous** communications
  - sender and receiver don't need to be aware of each other and don't care about the functional state of each other
- once the message is processed, it is deleted - no concept of time window, **no persistence**

---

### Cognito

- terrible naming
- provides **authentication, authorization, user management** for web/mobile apps


- **USER POOLS** - sign-in and get a JSON Web Token (**JWT**)
  - most AWS services **cannot use JWT** - API Gateway is an exception
  - **BUT !!!!!!**, JWT can be used to access **self-managed server based resources**
  - **MAINLY** sign-up, sign-in experience with user directory and profile management
  - MFA, other security features
  - **ALSO ALLOWS SOCIAL SIGN-IN with e.g. Facebook, Google, etc. and SAML**
  - but users authenticated this way **cannot directly use AWS services**


- **IDENTITY POOLS** - exchange a type of **external identity** for a set of **temporary AWS credentials**
  - with these AWS credentials, we can then access AWS resources
  - used for swapping authenticated/unauthenticated identity for AWS credentials
  - **IDENTITY POOLS ALLOW SOCIAL SIGN-IN (e.g. Facebook), SAML, even USER POOL identities TO GET SHORT-TERM AWS CREDENTIALS**
    - this way Identity Pools allow e.g. users authenticated through Facebook to access AWS resources
  - how it works: IDENTITY POOLS work by **assuming an IAM role on behalf of the IDENTITY** - AWS temporary credentials are generated and provided in most cases to web/mobile app


In case of Identity Pools, our application/Identity Pool has to deal with many different ID tokens (each for any external provider).
- we could use User Pools to handle all the types of identities, and then use Identity Pools to swap the User Pool token (JWT) for AWS credentials
- **WEB IDENTITY FEDERATION** - swapping of any external ID provider token for AWS temporary credentials
- this way we standardise on a single token (User Pools token = JWT)


- TL;DR:
  - User Pools - **sign-up/sign-in**; JWT token that can be used for authentication in self-managed server based resources, or API Gateway. Cannot be used to access AWS resources
  - Identity Pools - **swapping external ID provider for temporary AWS credentials**; sign-in with external identity (e.g. Facebook/Google login page), Cognito assumes a Role and generates temporary AWS credentials, passes it back to application, then AWS resources can be accessed



## CDN-and-Optimization

### Architecture Basics 

- CloudFront is a **global** object cache (CDN)
  - **a global CDN capable of caching both static and dynamic content !!!**
- **Download caching only**
- Content is cached in locations close to customers.
- If the content is not available on the local cache when requested, CloudFront
will fetch the item and cache it and deliver it locally.
- This provides lower latency and higher throughput for customers.
- Can handle static and dynamic content.
- works with ACM and HTTPS
  - can provide HTTPS capability for websites statically hosted on S3
- **Origin** the original location of your content, can be an S3 bucket or LB.
- **Distribution** the configuration unit of CloudFront.
- **Edge locations** global infrastructure which hosts a cache of your data.
  - There are over 200 edge locations.
  - They can be one or more racks in a third party server system.
  - Normally 90% storage with some small compute.
- **Regional Edge Cache**
  - Larger version of an edge location.
  - Support a number of local edge locations.
  - Designed to hold more data to cache things which are accessed less often.
  - **Provides second layer of caching**.


**Scenario**:
- you upload original content to e.g. S3 bucket
- you configure Distribution
- you set origin of this Distribution to point to S3 bucket
- you deploy that Distribution to the CloudFront Network
 - push the Distribution configuration to all of the chosen Edge Locations
- these Edge Locations can be now used by your customers


**Architecturally**:
- on one side there is the bucket with original content
- on another side there are Edge Locations
- in between there are **Regional Edge Cache**
  - they support a number of Edge Locations located in the **same geographic area**
#### Caching Optimization


**How it works !!!**:
- if someone tries to access the resource, **first the Edge Location is checked**
  - if the object were to be locally cached at this Edge Location, it would be returned immediately
  - the request would be returned quickly, great performance
- if the resource is not present in the Edge Location, **then the Regional Edge Cache** is checked
  - it acts as a bigger cache for multiple smaller Edge Locations
  - if any other Edge Locations in the area have previously accessed this object, **it should also be stored at the Regional Edge Cache**
  - if it's present there, it's pushed back to the Edge Location which originally requested it, where it's also stored
  - then it's returned to the user that requested it
- if the resource is not present in the Regional Edge Cache, **it's returned from the origin**
  - it will be stored in the Regional Edge Cache
  - Regional Edge Cache pushes the object back to the Edge Location which originally requested it, where it's also stored
  - then it's returned to the user that requested it
- for future requests, the resource can be fetched from the Edge Location

Parameters can be passed on the url such as query string parameter.
An example is `?language=en` and `?language=es`

Caching will cache each string parameter storing two different objects.
You must use the same string parameters again to retrieve them. If you remove
them and the object is not caching it will need to be fetched first.
- this means that when we cache whiskers.jpg, we actually cache whiskers.jpg with all the query string parameters that were used
- to get cached copy, we need to use exactly the same query strings

**Cache Optimization !!!**:
- If we don't want string parameters to be used for caching, we can select to **not forward them to the origin.**
- If the application does use query string parameters, we can use **ALL** of them for caching or just **SELECTED** ones.

### AWS Certificate Manager (ACM)


- HTTP lacks encryption and is insecure
- HTTPS uses SSL/TLS layer of encryption added to HTTP
- Data is encrypted **in-transit**
- Certificates allow servers to prove their **identity**
- Signed by a **trusted authority (CA).**
- To be secure, a website generates a certificate, and has a CA sign it. The
website then uses that certificate to prove its identity.
- ACM allows you to create, renew, and deploy certificates.
- Supported AWS services **ONLY** (e.g. CloudFront and ALB, **NOT EC2 !!!**)
  - we cannot deploy a certificate to EC2 instances
  - we can deploy a certificate to an Application Load Balancer, which is itself load balancing across these EC2 instances
- If it's not a managed AWS service, ACM doesn't support it.
- **CloudFront must have a trusted and signed certificate. Origin certificates must be valid - can't be self signed. !!!**
  - deploying a certificate to CloudFront **Distribution** means that all the Edge Locations also get that certificate
  - Edge Locations are accessed using **HTTPS** and the **DNS name** on the certificate
  - Edge Locations are communicating with Origin (e.g. S3 or ALB) using HTTPS as well

IMPORTANT FOR EXAM !!!:
- **with ACM, certificates must be created in the region the service which uses them is in !!!**
- if you want to require HTTPS between **viewers** and **CloudFront**, you must change the AWS Region to **US East (N. Virginia)** in the **AWS Certificate Manager** console before you request or import a certificate.
  - so for **CloudFront**, we need to create an **ACM certificate** in **us-east-1 !!!**
- **ACM-integrated services** (services we can use ACM with):
  - Elastic Load Balancer
  - CloudFront
  - Elastic Beanstalk
  - API Gateway
  - CloudFormation


#### Demo: making S3-bucket-hosted static website publicly accessible using CloudFront

1. Create a S3 bucket and unset `Block public access`
2. Upload website files to the bucket
3. Enable `Static website hosting` in Permissions tab
4. Upload `Bucket policy` to enable public access
5. Go to CloudFront service and create a Distribution (Web type, the RTMP type will be deprecated soon)
- set origin (Origin Domain Name): it can be e.g. S3 buckets, ALB or resources outside of AWS which have public IP
  - here we point it to our S3 bucket with static website
- pick viewer policy (HTTP and HTTPS; Redirect HTTP to HTTPS; HTTPS Only)
- we can adjust TTL of cached objects (default is 86,400 seconds)
- we can set Query String Forwarding and Caching (default is none)
- **we can set Distribution to be private !!!**
  - this means that we would need a **signed URL** or **signed cookies** which provide authentication to CloudFront
- we can pick Price Class to all Edge Locations, or limit it to 2 other options containing the largest continents/countries
- we can set **Alternate Domain Names (CNAMEs)** for our Distribution
  - by default, each Distribution is given a generated DNS name (e.g. https://d11111abcef8.cloudfront.net/logo.jpg)
  - if we want to use Alternate Domain Name (our own domain name), we need to: add the **Route53** configuration and **point it** at the **CloudFront distribution**, then **use AWS Certificate Manager** to **generate an SSL certificate** that **matches that name**
- we can specify Default Root Object (so there's no need to e.g. specify /index.html)
- we can enable logging to S3 bucket
- we can enable IPv6
6. Once the Distribution is created, we can use its domain name to access our S3-hosted static website


### Origin Access Identity (OAI)

Remember that OAI is different than Identity Policies !!!.
- **Identity Polices** are what we attach to **IAM Users, Groups or Roles**

1. **Identity** (OAI) can be associated with a **CloudFront distribution**.
2. The Edge Locations gain this identity.
3. Create or adjust the **bucket policy on the S3 bucket (origin)**. 
- Add an **explicit allow for the OAI** . 
- We can remove any other **explicit allows** (public access) on the OAI. 
- This leaves the implicit deny.

At this point, as long as accesses are coming from the Edge Locations, it will know they
are from the OAI and allow them. Any direct S3 bucket attempts will not use the OAI and
will only get the implicit deny.
- **making a CloudFront Distribution private only affects the Edge Locations !!!**
  - therefore if we want to prevent direct S3 bucket access using the bucket's DNS name, we need to use OAI !!!

**Best practice is to create one OAI per CloudFront distribution to manage permissions !!!**


### CloudFront Lambda@Edge !!!

- you can run **lightweidght** Lambda functions at **edge locations**
  - because they run at **edge locations**, they don't have the full Lambda feature set
- **adjust** data between the **Viewer** and **Origin**
- currently supports **Node.js** and **Python**
- runs **only** in the **AWS Public Space** - doesn't run in **VPC** !!!
- Lambda **Layers** are **not supported**
  - layer is a ZIP archive that contains libraries, custom runtime, or other dependencies
  - with normal Lambda (not Lambda@Edge), we can pull in additional code and content in the form of these layers
- **different limits** vs normal Lambda functions

Architecture:
- individual components of the wider communication can run a Lambda function
  - this Lambda function can influence traffic as a part of that connection
- Request -> Request -> Response -> Response (see below)
- **Viewer request** -  customer -> Edge Location
  - function is run **after** CloudFront **receives** a request **from a customer** (1.)
- **Origin request** - Edge Location -> Origin
  - function is run **before** CloudFront **forwards** the request **to an origin** (2.)
- **Origin response** - Origin -> Edge Location
  - function is run **after** CloudFront receives a response **from an origin** (3.)
- **Viewer response** - Edge Location -> customer
  - function is run **before** a response is **forwarded to a customer** (4.)


- Viewer-side connections (request and response) have limit of **128 MB, 5 seconds** for a function
- Origin-side connections (request and response) have limit of **regular Lambda MB constraint, 30 seconds** for a function

**Some of Lambda@Edge use cases**:
- A/B Testing, using **Viewer request function**
  - used for comparing two versions of website and choosing one that fits more into requirements
  - e.g. comparing main functioning website vs its alernatives
  - Lambda@Edge function can modify the viewer request and change the URL (e.g. percent algorithm, random choice)
- Migration between S3 Origins, using **Origin request function**
- Different objects based on device, using **Origin request function**
- Content by country, using **Origin request function**

### AWS Global Accelerator !!!

- Move the AWS network closer to customers.
  - architecturally similar to CloudFront, BUT it **doesn't cache anything** and **doesn't understand HTTP/S**
  - **on exam, we need to be able to determine when to use CloudFront vs Global Accelerator**
- Designed to **optimize** the flow of **data from users** to your **AWS infrastructure**.
- Generally customers who are further away from your infrastructure go through
more internet based hops and this means a lower quality connection.
- Normal IP addresses are unicast IP addresses. These refer to one thing: one network device.
  - usually if we have two devices and only one unicast IP address, bad things may happen
  - **anycast** IP addresses are designed to allow this
- **Anycast IPs** allow a **single IP** to be in **multiple locations**
  - routing moves traffic to **closest location**
- Global Accelerator starts with 2 **anycast** IP address !!!!
  - Customer's traffic initially uses **public internet** until it enters Global Accelerator at
  the closest edge location.
  - **Traffic then flows globally across the AWS global backbone network**
  - ... it's AWS own dedicated network - much better performance



Key concepts:
- CloudFront moves the **content** closer by caching it on the Edge Locations
- Global Accelerator moves the **actual AWS network** closer
  - it **doesn't cache anything !!!**
  - the aim here is to get your customers **onto the global AWS network** as quickly as possible (using **anycast IP addresses**)
  - once the traffic reaches edge, it's transited over the AWS global network to 1 or more locations where we host our stuff
- Global Accelerator is a **network product**, can be used for **NON HTTP/S**, so TCP/UDP.
  - this is **different from CloudFront**
- if questions mention **caching**, it will most likely be **CloudFront**
- if questions mention **TCP/UDP**, and the requirement for **global performance optimization**, then it will most likely be **Global Accelerator**
---

## Advanced-VPC

### VPC Flow Logs

- Capture **packet METADATA !!!**, not packet contents.
  - Source IP, Destination IP
  - Source Port, Desination Port
  - Protocol (ICMP=1, TCP=6, UDP=17)
  - Action (ACCEPT, REJECT)
  - Packet size
  - Anything which could be observed from the outside of the packet.
- Capture data at various different monitoring points.
  - **VPC**: all interfaces in that vpc
  - **Subnets**: interfaces in that subnet only
  - **Interface** directly
- **when service (e.g. RDS) is put in a VP/subnet/interface, it can utilize VPC flow logs**
- VPC flow logs are **NOT REALTIME !!!**
- Destination can be **S3** or **CloudWatch logs**
- Flow log inheritance is downwards starting at the VPC.
- The packet will always have source, then destination, then response.

- **To and from following services are not recorded:**
  - 169.254.169.254
  - 169.254.169.123
  - DHCP
  - Amazon DNS Server
  - Amazon Windows license


### Egress-Only Internet Gateway

- it's an Internet Gateway that **only allows IPv6 connections to be initiated from inside a VPC to outside**
- IPv4 addresses are private or public
  - private addresses cannot communicate directly with public or public AWS
  - NAT allows private IPs to access public networks and receive responses.
  - **NAT will not allow externally initiated connections IN.**
- All IPv6 addresses in AWS are public.
- Internet Gateway (IPv6) allows all IPs **in** and **out**
- Egress-only Internet Gateway is **outbound only** for **IPv6**
- To configure the Egress-only gateway, you must add default IPv6 route `::/0`
added to RT with `eigw-id` as target.

### VPC Endpoints (Gateway)

- Allow a **private-only resource** inside a VPC or **any resource inside a private-only VPC** access to **S3 and DynamoDB.**
  - both of these (S3, DynamoDB) are public services
- Normally when you want to access a public service through a VPC, you need infrastructure. 
  - You would create an IGW and attach it to the VPC.
  - Resources inside need to be granted IP address or implement one or more NAT gateways which allow instances with private IP addresses to access these public services.
- **Gateway Endpoints** are created **per service, per region**
  - e.g. for S3 in us-east1
- When you allocate a **Gateway Endpoint** to a subnet(s), a **Prefix List** is added to the route tables for that subnet / these subnets. 
  - The target is the Gateway Endpoint.
  - Any traffic destined for S3, goes via the Gateway Endpoint.
  - Gateway Endpoint is highly available for all AZs in a region by default.

- With a Gateway Endpoint you set which subnet will be used with it and it will configure automatically.
- A Gateway Endpoint is a VPC gateway object.
- **Endpoint policy** controls what things can be connected to by that endpoint.
- **Gateway Endpoints can only be used to access services in the same region**.
  - Can't access cross-region services.

- S3 buckets can be set to private only by **allowing access ONLY from a Gateway Endpoint (defined in Bucket Policy)**. For anything else, the implicit deny will apply.
- **Gateway Endpoints are only accessible from inside specific VPC !!!**.
  - **not accesible outside of the VPC they'are associated with !!!**
  - **they are logical gateway objects created in a VPC**
  - **we can access such objects only from that VPC they are created in**

- **Region resilient** by default

### VPC Endpoints (Interface)

- Provide **private access to AWS Public Services**.
  - Anything EXCEPT S3 and DynamoDB (these are handled by Gateway Endpoints)
- Contrary to Gateway Endpoints, **Interface Endpoints are not HA by default and are added to specific subnets**.
  - For HA, add **one Interface Endpoint** to **each subnet**, **per AZ** used in the VPC
- **We can have maximum one Interface Endpoint per AZ in VPC !!!**
- Network access controlled via **Security Groups** (cannot do it in Gateway Endpoints)
- You can use **Endpoint policies** to restrict **what can be accessed** with
the endpoint.
- **ONLY TCP and IPv4** at the moment.
- Behind the scenes, **it uses PrivateLink**.
- Endpoint provides a **NEW** service endpoint DNS
  - e.g. `vpce-123-xyz.sns.us-east-1.vpce.amazonaws.com`
- **Regional DNS** is one single DNS name that works whatever AZ you're using to
access the interface endpoint. Good for simplicity and HA.
- **Zonal DNS** resolves to that one specific interface in that one specific AZ.
- Either of those two endpoints can be used by applications to
directly and immediately utilize Interface Endpoints.
- **PrivateDNS** associates R53 private hosted zone with your VPC. This private
hosted zone carries a replacement DNS record for the default service
endpoint DNS name. It overrides the default service DNS with a new version
that points at your interface endpoint. Enabled by default.

#### Gateway Endpoints vs Interface Endpoints

**Gateway endpoints** work using prefix lists and route tables so they do not
need changes to the applications. The application thinks it's communicating
directly with S3 or DynamoDB and all we're doing by using a gateway endpoint
is influencing the route that the traffic flow uses. Instead of using IGW,
it goes via gateway endpoint and can use private IP addressing.
**highly available**

**Interface Endpoints** uses DNS and a private IP address for the interface
endpoint. You can either use the endpoint specific DNS names or you can
enable PrivateDNS which overrides the default and allows unmodified
applications to access the services using the interface endpoint. This doesn't
use routing and only DNS.
**not highly available**

### VPC Peering

- **Direct**, **encrypted** network link between **two and only two VPCs**.
  - Peering connection can be in the same or cross region and in the same or across accounts.
- When you create a **VPC peer**, you can enable an option so that **public hostnames** of services in the peered VPC resolve to the **private internal IPs**.
  - You can use the same DNS names if its in peered VPCs or not. 
  - If you attempt to resolve the public DNS hostname of an EC2 instance, it will resolve to the private IP address of the EC2 instance.

- Same region **Security Groups** can reference **peer Security Groups**
- VPCs **in the same region can reference each other by using security group ID**.
- You can do the same efficient referencing and nesting of security groups that you can do if you're inside the same VPC. 
  - This is a feature that only works with **VPC peers inside the same region**.

- In **different regions**, you can utilize security groups, but **you'll need to reference IP addresses or IP ranges**.
- If VPC peers are in the same region, then you can do the logical referencing of an entire security group.

- VPC peering connects **ONLY TWO VPCs**

- VPC Peering does not support **transitive peering**.
  - it means that if we have A - B - C, there's no connection between A and C.
  - If you want to connect 3 VPCs, you need 3 connections (A - B - C - A).

- **Routing** Configuration is needed, **SGs and NACLs** can filter
- VPC Peering Connections **CANNOT be created with overlapping VPC CIDRs !!!!!!!!**.

- Communication is **encrypted** and trasits over the **AWS Global Network** when using cross-region peering connections

**How does it work?**
- we create 3 VPCs
- we create a VPC Peering between VPC-A and VPC-B, VPC-B and VPC-C
- route tables at **both sides** of the peering connection are needed
  - traffic directed for the remote CIDR: e.g. VPC-A has 10.16.0.0/16, and we route traffic to 10.17.0.0/16, which is VPC-B's, to **peer gateway object**
  - for this reason, the **VPC Peering cannot be created** where there's **overlap in the VPC CIDRs**
---

## Hybrid-and-Migration

### Border Gateway Protocol (BGP)

- utilized by AWS products like **Direct Connect** and **dynamic VPNs**
- made by **Autonomous Systems (AS)**
  - routers controllers by one entity - a network in BGP
- **Autonomous System Number (ASN)**
  - are unique
  - allocated by IANA (0-65535)
  - 64512-65534 are private - can be utilized by private peering arragemenets without being officially allocated
- BGP operates over **TCP port 179** - it's reliable
- **Not automatic** - peering is **manually configured**
- BGP is **path-vector protocol**
  - it exchanges the **best path** to a **destionation** between **peers**
  - the path is called **ASPATH**
- **account link speed or condition is not taken into account**
  - instead, ASPATH is considered
  - this means that e.g. when A - B and B - C is connected with 1Gbps fibre, and A - C is connected with 5Mbps satellite, A - C would still default to the A - C because it's the shortest ASPATH, even though A - B - C would provide better performance
  - **BUT...** we can use **AS Path Prepending** to artifically make the satellite path look longer, making the fibre path preferred
    - this works by artifically prepending ASPATHs, e.g. 202,i becomes 202,202,202,i
- two types of BGP:
  - **iBGP** - internal BGP, focuses on routing **within** an AS
  - **eBGP** - external BGP, focuses on routing **between** AS's
    - eBGP tends to be used most often with AWS
- 



### AWS Site-to-Site VPN

- A logical connection between a VPC and on-premise network encrypted in transit
using IPSec, running over the **public internet\***
- Full HA - if we design and implement it correctly
  - router on customer premises is a single point of failure
  - for full HA, we need more than one customer routers
- Quick to provision, **less than an hour**.
  - in contrast to physical connections like **DirectConnect which take much longer to provision**
- VPNs connect **VPCs** and **private on-prem networks**.
- **Virtual Private Gateway (VGW)** is the target on one or more route tables
  - it can be attached to only one VPC
- **Customer Gateway (CGW)**
  - logical piece of configuration on AWS
  - thing that configuration represents
- **VPN connection** itself **stores the config** and **links to one VGW and one CGW**


**Static vs Dynamic VPN (BGP)**
- Static VPN:
  - VPC's routes for remote side added to **route tables** as **static routes**
  - VGW to on-premises networks for remote side **statically** configured on the **VPN connection**
  - **NO LOAD BALANCING AND MULTI CONNECTION FAILOVER**
- Dynamic VPN:
  - we use BGP (Border Gateway Protocol)
    - it lets **routers exchange networking information**
    - it's configured on **customer and AWS side using Autonomous System Number (ASN)**
  - can communicate the state of links, adjust routing on the fly
    - **MULTIPLE VPN CONNECTIONS PROVIDE HA AND TRAFFIC DISTRIBUTION**
  - VPC's routes for remote side added to **route tables** can be **static**, but we can also make them **dynamic**
    - **Route propagation** means routes are added to the route table automatically (routes taken from VGW)


**VPN Considerations**
- **Speed cap on VPN with two tunnels of 1.25 Gbps !!!**
  - if we want more than 1.25 Gbps, we can't use VPNs
  - AWS limit, will need to check speed supported by customer router.
  - Will be processing overhead on encrypting and decrypting data.
  At high speeds, this overhead can be significant.
- **Latency is inconsistent** because it uses the **public internet**
  - something more reliable might be DirectConnect
- Cost
  - AWS charges hourly
  - GB transfer out cost
  - on-premises internet connection costs
- VPN setup - hours of work
  - still faster to setup than almost anything else
- Great as a backup especially for Direct Connect (DX)


### AWS Direct Connect (DX)

- in many ways similar to VPN, but it's an actual **physical connection**
- DX port is a **physical allocation** inside a data center
- DX Port operating at a certain speed which belongs to a certain AWS account.
- Allocated at a DX location which is a major data center.
- Two speeds of Network Point into AWS
  - **1 Gpbs**: 1000-Base-LX
  - **10 Gbps**: 10GBASE-LR
- This is a cross connect to your **Customer Router** (requires VLANS/BGP)
- ... or you can connect to a **Partner Router** if **extending** to your location.
  - this may take some additional time
- Think of this as a **single fiber optic cable** from the **AWS-managed DX port** to your **network !!!**.
- VIFS (Virtual Interfaces) are **virtual connections** that run over the top of **physical DX connection**
  - one physical connection can have multiple VIFS running over the top of it
  - each of these VIFS is a **VLAN and BGP connection** between your **router** and the **AWS DX router**
- two types of VIFS:
  - **Private VIF** (VPC)
    - associated with a Virtual Private Gateway (VGW) and **connect to a single VPC**
    - provide private network connectivity betwen a VPC and your on-premises network
    - Can have as many Private VIFS as you want.
  - **Public VIF** (Public Zone Services)
    - Only **public AWS services**, not public internet


**DX considerations:**
- takes **MUCH** longer to provision vs **VPN**
  - **DX port** provision **is quick**
  - it's the **cross-connect** that **takes longer**
  - extension to premises can take **weeks/months**
- Use **VPN first**, then **replace with DX** (or leave VPN as backup)
- **Faster: 40 Gbps with Aggregation !!!**
- **Low CONSISTENT latency, doesn't use business bandwidth !!!**
- EXAM: **NO BUILT-IN ENCRYPTION**
  - completely unecrypted connection !!!
  - things running on top of it might be encrypted
- ... BUT !!!:
  - public VIFS allow connection to public AWS services
  - inside the VPC we already have the **Virtual Private Gateway (VGW)**
  - ... because this is used for any **private VIFS** running over the Direct Connect
  - when we create a **Virtual Private Gateway** it actually creates **endpoints** inside the AWS public zone with public IP addresses
  - so we can create a **VPN** but as transit network use the **public VIF !!!** running over **Direct Connect** (fast physical connection), **instead of using public internet as in regular VPN**
  - in this case it would leverage the **already created endpoints** and run **IPSEC (encrypted) VPN** over a **public VIF**
  - this would all the benefits of DX: **speed, low latency** + benefits of IPSEC VPN: **encryption**


### AWS Transit Gateway (TGW)

- Network Transit Hub to **connect VPCs to on premises networks**
  - it uses site-to-site **VPNs** and **Direct Connect**
- Significantly **reduces** network **complexity**.
- single Network Gateway Object - so it's HA and scalable
- Create **attachments** to allow Transit Gateway to connect to other network objects.
  - **VPC** attachments
  - **Site to Site VPN** attachments
  - **Direct Connect Gateway** attachments
- Supports **transitive routing**. 
  - **No need to create a mesh topology**.
  - **Without it**, for mesh topology:
    - we'd need to create VPN connections for all VPCs to the Customer Gateway
    - for full HA we'd need to have at least two Customer Gateways, which means at least 2x VPN connections
  - **with** Transit Gateway:
    - we'd have a single Transit Gateway using a Site-to-Site VPN attachment
    - which means it becomes the AWS side termination point for the VPN
    - so instead of having connections from all the VPCs, we'd have connections from this one Transit Gateway to 1+ Customer Gateways
    - we'd have **VPC attachments (one for each VPC)** configured with a **subnet in each AZ** where service is required
    - then, **one single Transit Gateway would act as a HA inter-VPC router**
    - **since it's transitive, we wouldn't need peering - all of the VPCs can talk to each other through the Transit Gateway !!!**
- Can be used to connect to **Direct Connect Gateway**
- Transit Gateway comes with a default route table
  - but we can configure **multiple route tables** allowing complex routing architectures


**Transit Gateway considerations:**
- supports **transitive routing** - no need for mesh topology
- can be used to create **global networks**
- can share between accounts using AWS RAM (Resource Access Manager)
- can be used to **peer with different regions** 
  - in the same or cross-account
- **less complexity in comparison to without TGW**

### Storage Gateway

- Hybrid Storage Virtual Application (**On-premises**)
  - **extension, migration, backups**
- **Extension** of **File** and **Volume** Storage into AWS
  - technique called **Data Center Extension**
- **Volume** storage **backups into** AWS
- **Tape** backups **into** AWS
- **Migration** of existing infrastracture to AWS


Modes:
- **Tape Gateway Mode** (**VTL** - Virtual Tape Library) Mode
  - Virtual Tapes are stored on S3 and Glacier
- **File Gateway Mode** (**SMB !!!** and **NFS !!!**- common file sharing protocols)
  - **File Storage** backed by **S3** Objects
- **Volume Gateway Mode** (Gateway **Cache** or Gateway **Stored**) - iSCSI
  - **Block Storage** backed by **EBS Snapshots (Stored) or EBS Snapshots + S3 (Cache)**
  - two types:
    - Gateway **Stored**
      - **DATA IS HELD LOCALLY ON THE STORAGE GATEWAY !!!**
      - Great for **disaster recovery**
      - not for extensions
    - Gateway **Cache**
      - good for data centre extension into AWS
      - **DATA IS STORED IN AWS PRIMARLY, ONLY FREQUENTLY ACCESSED DATA IS DOWNLOADED INTO THE APPLIANCE !!!**
      - designed for extensions into AWS when our capacity is limited 
      - Backup to S3-backed volumes and EBS Snapshots
      - Great for **limited local storage capacity**.
        - and we want to extend this capacity in our data center using AWS and volume storage
        - similar to File Gateway Mode, but FGM uses file storage, not block storage like in Volume Gateway Mode


If:
- we need to replace a **tape backup system**: pick the Tape Gateway Mode
- we need to store **files** and have those transfer through to **S3**: pick the File Gateway Mode
- we need **block storage (volumes)**, decide between Volume Gateway: **Stored** or **Cache**
  - Stored: keep the storage locally, but asynchronously backup to AWS
  - Cache: extend into AWS while minimizing local footprint

### Snowball / Snowball Edge / Snowmobile

- Designed to **move large amounts of data IN and OUT of AWS**.
- Physical storage the size of a suitcase or truck.
- Ordered from AWS, use, then return.

#### Snowball

- a physical process, **NOT ELECTRONIC METHOD**
  - ordered from AWS
  - log a job
  - device delivered (**not instant**)
- any data on Snowball is stored with **KMS At Rest encryption**
- **1 Gbps** or **10 Gbps** connection
- **50TB** or **80TB** Capacity.
  - but we can order **multiple devices** and use them for more than 80 TB
  - **multiple devices** to **multiple premises !!!** (e.g. order 10 snowballs, one for each of our 10 business premises)
- **10TB** to **10PB** of data is economical range (**multiple devices**) !!!!
- Good for multiple locations
- **Only storage, No compute !!!**
- older generation than Snowball Edge, doesn't include compute

#### Snowball Edge

- Includes **both storage and compute**
  - like Snowball, but it comes with **compute** capability
- **Larger capacity** vs Snowball.
- **Faster networking** vs Snowball
  - 10 Gbps, 10/25 Gbps, 45/50/100 Gbps
- **Three types** of Snowball Edge
  - **Storage** optimized
    - 80TB storage, 24 vCPU, 32 GiB RAM
    - **but with EC2, includes 1TB SSD**
  - **Compute** optimized
    - 100TB storage, 7.68 GB NVME (fast PCI bus storage), 52 vCPU, 208 GiB RAM
  - **Compute** with **GPU**
    - Same as Compute optimized, but **with GPU**
- newer generation than Snowball, does include compute

#### Snowmobile

- **Portable data center** within a **shipping container on a truck**
- This is a special order and is not available in high volume, and not available everywhere
- ideal for **single location** where **huge amounts of data ingestion** (**10 PB+**) is required.
- **max is 100 PB per Snowmobile !!!**
- NOT ECONOMICAL FOR **MULTI-SITE (UNLESS HUUGE)** OR **SMALLER THAN 10 PB** !!!

### AWS Directory Service

- provides a managed directory, a store of **objects**:
  - users
  - groups
  - computers
  - servers
  - file shares 
- ... with a **structure** called a domain / tree. 
  - multiple trees can be grouped into a forest.
- commonly used in **Windows environments**
- devices can join a directory
  - so laptops, desktops, and servers can all have a **centralized management and authentication**
  - you can sign into multiple devices with the same username and password.

- one common directory is **Active Directory** by Microsoft
  - its full name is **Microsoft Active Directory Domain Services** (AD DS)
- AD DS is mort popular, but there are **open-source** alternatives (e.g. **SAMBDA**)

**AWS Directory Service**:
- AWS-managed implementation of directory
  - equivalent of Active Directory, like RDS is of databases
- **no admin overhead of running our Directory service !!!**
- runs within a **VPC** as a **private service**
- provides **HA** by deploying into **multiple AZs**
- certain services in AWS **require** a directory, e.g. **Amazon Workspaces** (equivalent of Citrix)
- to join EC2 instances to a domain you need a directory.
- can be:
  - **isolated** inside AWS only, or ...
  - **integrated** with existing **on-prem directory**, or ...
  - act as a **proxy** back to on-premises

#### Directory Modes

- **Simple AD**: should be default. Designed for simple requirements.
  - open source directory
  - based on **Samba 4**
  - if open-source / Samba / Samba 4 is mentioned, most likely it'll be **Simple AD**
  - up to 500 users (small) or 5,000 users (large)
  - integrates with AWS services - EC2, Workspaces
- **Microsoft AD**: 
  - Windows
  - trust relationship with on-prem over private networking (either Direct Connect or VPN)
  - **primary** running location is in **AWS**
    - **resilient** if the VPN fails - services in AWS will still be able to access the directory running in Directory Service
- **AD (Active Directory) Connector**: 
  - allows AWS services which NEED a directory to use an existing on-premises directory
  - it **proxies to your on-premises directory**
  - if private connectivity fails, the AD proxy won't function
    - services at AWS side will be interrupted

When to choose what:
- **Simple AD**
  - default
  - simple requirements
  - a directory in AWS
- **Microsoft AD**
  - apps in AWS which need Microsoft AD DS
  - need to **trust** AD DS
- **AD Connector**
  - AWS services which need a directory **without storing any directory info in the cloud**
  - **proxy** to our on-premises Directory

### AWS DataSync

- data transfer service **TO** and **FROM** AWS
  - migrations
  - data processing transfers
  - archival / cost effective storage
  - Disaster Recovery / Business Continuity planning
- designed to work at **huge scales**
  - each **Agent** can handle 10 Gbps
  - each **Job** can handle 50 million files
- very few scenarios will require that level of capacity / performance
- integrates with **EFS, FSx, S3**
- keeps **METADATA** (e.g. permissions, timestamps)
- built-in **DATA VALIDATION** enabled by default
  - data after arrival matches the original data
- **SCALABLE** - 10 Gbps per Agent (100~ TB per day), can add additional Agents
- **BANDWIDTH LIMITERS** to avoid customer impact of transfering the data
- **INCREMENTAL** and **SCHEDULED** transfer options
- **COMPERSSION** and **ENCRYPTION** in transit is also supported
- **AUTOMATIC RECOVERY** from transit errors
- **AWS SERVICE INTEGRATION** with S3, EFS, FSx for Windows servers
  - for some services, it supports service-to-service transfer
  - so moving data from EFS to EFS inside AWS, even across regions
- pay-as-you-use product - **cost per GB of data moved**


- DataSync **Agent has to be installed on on-premises**
  - it runs on a virtualisation platforms, e.g. VMWare
  - **Agent communicates over NFS or SMB with on-premises storage**
  - transfers the data to AWS DataSync **Endpoint** which runs within AWS (communication is encrypted in transit - TLS)
- once data arrives at **Endpoint**, it can store it in a number of different types of locations


#### AWS DataSync Components

- **Task**
  - job within DataSync
  - defines **what** is being synced and **how quickly**
  - defines **two locations** involved in the job (**FROM** where and **TO** where)
- **Agent**
  - software to read and write to on-prem using **NFS or SMB**
  - used to pull data off that store and move into AWS or vice versa
- **Location**
  - every task has two locations `FROM` and `TO`
  - example locations:
    - network file systems (NFS), common in Linux or Unix
    - server message block (SMB), common in Windows environments
    - AWS storage services (EFS, FSx, and S3)

### FSx for Windows File Server

- Fully managed **Windows-native file system, accessed over SMB !!!**
  - architected similarly to RDS, but instead of databases, we get file shares
- Designed for **integration** with **Windows environments**
  - native Windows file system, not emulated server
- Integrates with **Directory Service** or **Self-Managed AD (on-premises)**
- **Resilient** and **HA**
- **Single** or **Multi-AZ** within a VPC.
  - This controls the network interfaces (ENI) that are available.
  - Single mode use **replication** in the AZ to ensure **resiliency**.
  - Multi-AZ provides fully HA solution
- Can perform full range of different backups
  - **Client side** and **AWS side**
  - Can perform **scheduled** and **on-demand** backups
- Accessible using **VPC, Peering, VPN, Direct Connect**
- KMS at-rest encryption and enforced enryption in-transit


#### FSx: words to look for in exam !!!!

- **VSS**: file- and folder-level restores
  - unique to FSx
  - users can view different versions of file, restore from a user-driven perspective without having to engage admin
- Native File System (**NFS**) accessible over **SMB !!!**
  - if SMB is mentioned, it most likely will be FSx
  - **NFS** is **accessible only** from **EC2 instances** or **Linux on-premises** services
- **Windows permissions model**
- supports **Distributed File System (DFS) !!!**: scale-out file share structure
- **already managed** service, **no admin overhead**
- integrates with **DS** and **your own** on-premises directory service (e.g. AD)



### FSx for Lustre

- Lustre: file system designed for various **high performance computing** workloads (a portmanteau word derived from Linux and cluster)
- managed Lustre - designed for **HPC (High Performance Computing) - Linux** Clients (**POSIX-style permissions for file systems !!!**)
- Machine Learning, Big Data, Financial Modelling
- **100's GB/s througphut**
- **sub millisecond latency**
- deployment types:
  - **Scratch**
    - highly optimised for **short term storage**
    - **no HA, no replication**
    - fast
    - base **200 MB/s per TiB of storage**
  - **Persistent**
    - **longer term storage**
    - HA **but in one AZ only !!! - self healing**
    - **50 MB/s, 100 MB/s, 200 MB/s per TiB storage**
- for both Scratch and Persistent, we can **burst** up to **1300 MB/s per TiB of storage**
  - based on **Credit System**
- for both Scratch and Persistent, we can **backup to S3** (manual or automatic 0-35 day retention)
- accessible over **VPN** or **Direct Connect**
- S3 Bucket can be attached as a repository
  - when the S3 Bucket-hosted data is accessed in the FSx file system, it's lazy loaded from S3
- data can be exported back to S3 at any point using **hsm_archive** command
- Lustre **splits data** up **when it's storing it** to disks:
  - metadata (filenames, timestamps, permissions) stored on **Metadata Targets (MST)** 
    - Lustre filesystem has **one MST**
  - objects stored over **multiple Object Storage Targets (OSTs)**
    - each OST is **1.17 TiB**
- baseline **performance based on size**
- size: min **1.2 TiB**, then increments of **2.4 TiB**


**On exam !!!**, pick FSx Lustre when following keywords are mentioned:
- Lustre
- really high-end **performance** requirements
- POSIX
- Machine Learning, SageMaker, Big Data, etc.

---

## Security-Deployment-Operations

### AWS Secrets Manager

- this is **NOT SSM (Systems Manager) Parameter Store**
  - just like Parameter Store, it lets us store secrets
  - but it focuses on **storage and ROTATION of secrets**
- **share functionality** with **Parameter Store**. 
  - sometimes both are appropriate.
- designed specifically for **secrets**: **passwords, API keys**
- usable via Console, CLI, API, or SDK (integration)
- supports the **automatic rotation** of secrets using **Lambda**
- directly **INTEGRATES** with some AWS products (e.g. **RDS !!! watch out for RDS on exam**) 
- if Lambda is invoked and **changes (rotates)** a secret, the **password can automatically change** in RDS.
- secrets are **encrypted at rest**.
- integrates with **IAM**, can use **IAM permissions** to **control access** to secrets.

#### Secrets Manager Example

1. The Secrets Manager **SDK** retrieves database credentials.
2. SDK uses **IAM credentials** to retrieve the secrets.
3. Application uses the secrets to access the database.
4. Periodically, a lambda function is invoked to **rotate** the secrets.
5. The Lambda uses an **execution role** to get permissions.

Secrets are secured using KMS so you never risk any leakage via physical access
to the AWS hardware and KMS ensures role separation.

### AWS Shield and WAF (Web Application Firewall)

Both provide **global perimeter protection.**

#### AWS Shield

- provides AWS resources with **DDoS protection**
- normally not possible to block them by blocking individual IP addresses. 
  - without detailed analysis, the traffic looks like normal requests to your website.

- Shield **Standard**
  - **Free** with **Route53** and **CloudFront (CDN)**, enabled by default
  - Provides **Layer 3** and **Layer 4 protection** against **DDoS** attacks.
- Shield **Advanced**
  - $3000 per month
  - Includes **EC2, ELB, CloudFront, Global Accelerator and Route53 !!!!**
  - Provides access to DDoS advanced **response team** and **financial insurance**

#### WAF (Web Application Firewall)

- **Layer 7 !!!** firewall (HTTP/s) firewall
  - normally firewalls operate on Layers 3/4/5 (they understand IP, TCP, UDP and session - but can't interrogate packets)
- Protects against **complex Layer 7 attacks**:
  - SQL injections
  - Cross-Site Scripting
  - Geo Blocks
  - Rate Awareness
- Web Access Control List (**WEBACL**) integrates with **ALB, API Gateway, and CloudFront**
  - so we'd integrate e.g. CloudFront Distribution to use WEBACL
- **Rules** are added to **WEBACL** and **evaluated when traffic arrives**

#### Example of Architecture

Points of filtering:

1. **Shield Standard** automatically looks at the request when it arrives at **Route53**.
2. The user is directed to the closest **CloudFront** location. 
  - Again, **Shield Standard** looks at the request again before it moves on.
3. **WAF Rules** are defined and included in a **WEBACL** which is associated to a
CloudFront Distribution and deployed to the edge.
  - the WEBACL filters traffic at the edge
4. **WAF** can be also used on the **Application Load Balancer**
  - by associating WEBACL with the load balancer


For the exam !!!:
- link the term `DDoS` with AWS Shield
  - AWS Shield comes as **Standard** when using **Route53 and/or CloudFront**
  - AWS Shield **Advanced** comes with extra cost, provides **financial insurance** against increased resource costs in response to DDoS attack. Provides access to **24/7, 365, DDoS response team**
- link the terms `Layer 7 filtering`, `HTTP(S) filtering` with WAF
  - WAF can be linked to **CloudFront, API Gateway, Application Load Balancer**

### CloudHSM

- KMS is the key management service within AWS. 
  - It is used for encryption within AWS and it integrates with other AWS products. 
  - Can generate keys, manage keys, and can integrate for encryption. 
  - **The problem is** this is a **shared service**. 
  - You're using a **service** which **other accounts** within AWS **also use**.
  - Although the **permissions are strict**, **AWS still does manage the hardware** for KMS.
- KMS behind the scenes uses **Hardware Security Module** (HSM). 
  - These are industry standard pieces of hardware which are **designed to manage keys** and **perform cryptographic operations**
- You can run your own **Hardware Security Module** on premise. 
- **CloudHSM** is a true **"single tenant"** Hardware Security Module (HSM) that's **hosted within the AWS Cloud**
- the term HSM could refer to both CloudHSM, or an on-premise HSM device, or both
- AWS **PROVISIONED**, but fully **CUSTOMER MANAGED**
  - AWS has **no access** to **secure area** where key material is held
  - HSMs operate in **AWS managed HSM VPC**
  - **interfaces (ENIs) are added to the customer VPC**
  - AWS provisions the HW, but it is **impossible for AWS to help** if we lose access by **accident**. 
  - There is no way to recover data from them if access is lost.

- Fully **FIPS 140-2 !!!** Level 3 (KSM is L2 overall, but some is L3)
  - if you require **Level 3** overall, you MUST use **CloudHSM**.

- all **KMS** actions are performed with AWS CLI and IAM roles.
- but **HSM will not integrate with AWS** by design. instead it **uses industry standard APIs !!!**
  - PKCS#11
  - Java Cryptography Extensions (JCE)
  - Microsoft CryptoNG (CNG) libraries

- KMS can use CloudHSM as a **custom key store**
  - CloudHSM integrates with KMS

- HSM is **not highly available and runs within one AZ !!!**. 
  - To be HA, we need **at least two HSM devices**, **one in each AZ** we use. 
  - once HSM is in a **cluster**, it **replicates all keys, policies, configuration** automatically.
- HSM needs an endpoint in the subnet of the VPC to allow resources access to the cluster.


#### Cloud HSM Use Cases

- **No native AWS integration** with AWS products 
  - e.g. can't use S3 SSE with CloudHSM.
- Can **offload the SSL/TLS processing** from webservers. 
  - CloudHSM is **much more efficient to do these encryption processes**
  - KMS cannot do it
- **Oracle** Databases can use CloudHSM to enable **Transparent Data Encryption (TDE)**
  - AWS has no ability to decrypt that data
  - useful for **highly restricted environments**
- **Protect** the **private keys** for an **Issuing Certificate Authority (CA)**
- Anything that needs to **interact with non-AWS products** - use HSM !!!
- Anything that needs to interact with AWS products - don't use HSM

---

## NoSQL-and-DynamoDB

### DynamoDB Architecture

- NoSQL **Public** Database as a Service (**DBaaS**) - **Key/Value & Document**
  - accesible anywhere with access to public endpoints of DynamoDB
  - ... so public internet or VPC with **Internet Gateway** or **Gateway Endpoint** (which gives access only to S3 and DynamoDB)
- handles simple **Key/Value** data or **DocumentDB** modeled data
  - strictly speaking, it's a **wide-column Key/Value database**
- Not like **RDS/Aurora/Aurora Serverless** which are **Database Server as a Product**.
  - DynamoDB has **no self-managed servers or infrastructure !!!**
  - with DynamoDB, we get the actual database delivered as a service
- **Manual / Automatic / On-Demand** provisioned performance IN / OUT
- **Highly Resilient** across **multiple AZs** (as a region)
  - optionally **Globally resilient**.
- Data is **replicated** across multiple storage nodes **by default**.
- Really **fast, single-digit millisecond access** to data.
- Supports **backups** with **point-in-time recovery** 
- Any data is **encrypted at rest**.
- Allows **Event-Driven integration**
  - do things when data changes.

- more like **Database Table** as a Service product

#### Dynamo DB Tables

- tables are **base entity** inside DynamoDB
- **table** a **grouping of items** which **share** the **same primary key**
  - tables can have **infinite number** of **items** within them
  - there is **no limit** to the number of items
- **items** within a table are how you manage the data.
- **two types** of primary key:
  - **simple** (**PARTITION** key)
  - **composite** (**PARTITION** key and **SORT** key)
- notice that **PARTITION** key doesn't always mean **PRIMARY KEY**
  - if primary key is **composite**, then this assumption is not true
- **every item** in the table has to use the **same primary key**
  - it has to have an unique value for that primary key
  - if the primary key is **composite**, then the **combination** of **PK** (Partition Key) and **SK** (Sort Key) has to **be unique**
- each **item** can have:
  - no attributes
  - all attributes
  - mixed attributes
  - different attributes
- DynamoDB has **no rigid attribute schema**
- items can be **max 400 KB !!!** in size. 
  - This includes the primary key and attributes.

- In DynamoDB, **CAPACITY means SPEED !!!**. 
  - in this case: SPEED, not SPACE
- **two types** of capacity models: **ON-DEMAND, PROVISIONED**
  - can switch between them
  - if you choose **on-demand capacity** model you don't have to worry about capacity. 
  - you only pay for the operations for the table.
  - if you choose **provisioned capacity**, you must set this on a **per table basis**.

- Capacity is set per **WCU (Write Capacity Unit)** or **RCU (Read Capacity Unit)**:
  - 1 WCU means you can **write 1KB/s** to that table
  - 1 RCU means you can **read 4KB/s** for that table

#### Dynamo DB Backups

**On-demand Backups**: 
- Similar to **manual RDS snapshots**. 
  - **Full backup** of the table that is **retained until you manually remove** that backup. 
  - This can be used to **restore data and CONFIGURATION** in the **same region or cross-region**. 
  - You can restore **with or without INDEXES**, or **adjust encryption** settings.

**Point-in-time Recovery**: 
- **Must be enabled** on each table and is **off by default**. 
- This allows **continuous record of changes** over **35 days window**
- Allows to **replay** any point in that window to a **1 second granularity**.

#### Dynamo DB Considerations

- **NoSQL** -> you should jump towards DynamoDB.
- **Key/Value** -> you should jump towards DynamoDB.
- access to DynamoDB is via **Console, CLI, or API**
- SQL / Relational data -> this is **NOT** DynamoDB.

- **Billing** based on:
  - **RCU and WCU**
  - **Storage** on that table
  - Additional **features** on that table

- Can purchase **reserved allocations for capacity** with a **cheaper rate** for a **longer term**

### DynamoDB Operations, Consistency, and Performance

#### DynamoDB Reading and Writing

**On-Demand capacity**: 
- Don't have to explicitly set capacity settings - all handled by DynamoDB
- **Unknown or unpredictable load** on a table.
- Priority on as **little admin overhead** as possible
- Pay a **price** per **million Read or Write units**. 
- This can be as much as 5 times the price vs provisioned
  - we reduce overhead and are able to cope with unknown level of demand, BUT we are **paying more** for that privilege

**Provisioned capacity**: 
- **RCU and WCU** set on a **per table basis**.
- **Every** operation consumes **AT LEAST 1 RCU\*/WCU**
  - there's a way to get cheaper RCU: eventually-consistent reads are half the price (8 KB/s)
- 1 RCU = 1 x **4KB read operation per second**. This rounds up to at least 1 RCU\*
- 1 WCU = 1 x **1KB write operation per second**.
- Every table has a **WCU and RCU burst pool**. 
  - This is **300 seconds of RCU or WCU** set on the table
- we should rely on the number of RCU/WCU we provision, not the burst pool

#### Query

- when performing query operation, we need to **pick ONE Partition Key (PK) value** 
  - ... and **OPTIONALLY** a **Sort Key (SK) value** or **RANGE of Sort Key values !!!**
- query may return no items, one item, or multiple items
  - ... but regardless, we provide only one Partition Key to the query

- **Capacity** consumed is the **SIZE of ALL RETURNED items**. 
  - further filtering discards data, but **Capacity is still consumed**.

- If you query a PK it can return all fields items that match. 
- It is **always more efficient to pull as much data** as needed per query to save RCU
  - splitting the queries to separate ones may cause unnecessary rounding up of RCUs 

You have to query for at least one item of PK and are charged for the
response of that query operation.

If you filter data and only look at one attribute, you will still be
charged for pulling all the attributes against that query.

#### Scan

- **LEAST EFFICIENT** when pulling data from DynamoDB, but the **MOST FLEXIBLE**
- Scan **moves through the ENTIRE table item by item** 
  - ... consuming the capacity of every item. 
  - even if you consume less than the whole table (by using **attributes and filters**) after using scan, it will charge based on **every ITEM scanned through** anyway
- It adds up and will charge rounding up.

#### DynamoDB Consistency Model

- **Consistency** - how when data is updated, and then immediately read, is that read data **immediately** the same as the recent update, or is it **eventually** the same?
- DynamoDB can operate using two different **consistency modes** for **read operations**
  - **Eventually** Consistent
    - easier to implement
    - scales better
    - RCUs are 50% of the Strongly consistent, when **Eventual Consistency is enabled !!!** (8 KB/s, same price)
    - can be unlucky with **stale data** if a node is checked **before replication is completed**
  - **Strongly (Immediately)** Consistent
    - more **costly** to achieve
    - strongly consistent read **always uses the Leader node**
    - **less scalable**.

- Every piece of data is **replicated** multiple times **between separate AZs** 
  - a point in each AZ is called **Storage Node**. 
- There is one **Leader storage node** and every other node follows.
  - **writes are always directed to the Leader node !!!**. 
  - once the Leader is complete, it's **consistent**. 
  - it then **starts** the process of **replication**.
  - length of this replication is usually in miliseconds, but it **depends on the load** on these **Storage Nodes**
  - this assumes the lack of any faults on the storage nodes.


Not every application can tolerate eventual consistency. If you have a stock
database or medical information, you must use strongly consistent reads.
If you can tolerate the cost savings you can scale better.

#### WCU Example Calculation !!!!

- Store 10 items per second with 2.5K average size per item.
- Calculate:
  - **WCU per item**: in this case, each item is 2.5KB / 1 KB (WCU rate) = 2.5
  - **round up**: 2.5 => 3 WCU
  - multiply by **items per second**: 3 WCU * 10 items/s = 30 WCU


#### RCU Example Calculation

- Retrieve 10 items per second with 2.5K average size per item.
- Calculate:
  - **RCU per item**: in this case, each item is 2.5KB / 4 KB (RCU rate) = 0.625
  - **round up**: 0.625 => 1 RCU
  - multiply by **items per second**: 1 RCU * 10 items/s = 10 RCU for strongly consistent
    - for eventually consistent, 5 RCU

### DynamoDB Streams and Triggers

**Streams concepts**:
- time ordered list of **ITEM CHANGES** in a table
- **24-hour** rolling window
- enabled on a **per table** basis
  - it uses Kinesis Streams on the backend
- records **INSERTS, UPDATES** and **DELETES**
- different **view types** influence what is in the stream
- four **view types**:
  - KEYS_ONLY : only shows the **Partition** and optionally **Sort keys** for the item that has changed
  - NEW_IMAGE : shows the entire item with its **state after the change**
  - OLD_IMAGE : shows the entire item with its **state before the change**
  - NEW_AND_OLD_IMAGES : shows the entire item **both before and after the change**

- Pre or post change state might be empty if you use **insert** or **delete**.

#### Trigger Concepts

- ITEM **changes** generate an **event**
- that event **contains the data** which changed
- an **action is taken** using **that data**
- to implement Trigger architecture for DynamoDB, we use **Streams + Lambda**
- **Reporting** and **Analytics**
- **Aggregation**, **Messaging** or **Notifications**
- Eliminates the need to poll databases.

### DynamoDB Local (LSI) and Global (GSI) Secondary Indexes

- ways for **improving data retrieval** operations in DynamoDB.
  - they provide alternative view of the table
- Query is the most efficient operation in DDB
  - ... but it can only work on 1 Partition Key value at a time
  - ... and optionally a single, or range of Sort Key values
- **Indexes** are **alternative views** on table data
- two types of indexes:
  - **Local Secondary Index** (LSI) - different **SK**
  - **Global Seconday Index** (GSI) - different **PK and SK**


#### Local Secondary Indexes (LSI)

- LSI is an alternative view for a table
  - alternative **Sort key**
  - what does it mean: **we can choose an attribute and use LSI to create an alternative view of the table, in which the chosen attribute is Sort key**
  - but the same **Partition key**
- **MUST be created with a table !!!**
  - cannot be created after the table has been already created
- limit: **max 5 LSIs per base table !!!**
- **shares** the **RCU and WCU** with the **table**
  - IMPORTANT: LSI provides **Shared Capacity Settings with the table !!!**
- attributes:
  - ALL
  - KEYS_ONLY
  - INCLUDE
- indexes are **sparse**
  - it means that only items that **have the attribute (which we chosen as the new SK)** are present in the index !!!
  - so if we choose an attribute, the items that don't have this attribute will be omitted !!!
- LSIs are **strongly consistent !!!**




#### Global Secondary Index (GSI)

- **Can be created at any time** - they are **much more flexible**
- default limit: **max 20 GSIs** for each table
- allows for **alternative Partition key and Sort key**
- GSI will have **their own RCU and WCU allocations if we're using Provisioned capacity!!!**
- attributes:
  - ALL
  - KEYS_ONLY
  - INCLUDE
- GSIs are **always EVENTUALLY consistent**. 
  - replication between base and GSI is **asynchronous**
- just like LSIs, GSIs are also **sparse**
  - only items which have values in the new PK and optional SK are added

#### LSI and GSI Considerations

- Use indexes for **alternative access patterns**
  - create perspectives for different types of queries
  - different teams/requirements can access the same data just using the different perspective
- Careful with what attributes we choose to project into the index (KEYS_ONLY, INCLUDE, ALL)
  - Queries on attributes, which are not projected, are expensive
  - If you don't project a specific attribute, then you require the attribute when
  querying data, it will then fetch the data later in an inefficient way.
  - This means you should try to plan what will be used on the front.
- Use **GSIs as default**, LSI only when **strony consistency** is required


### DynamoDB Global Tables

- Global tables provide **multi-master**, **cross-region** replication.
  - no single table is viewed as master
  - all tables are the same
  - global, allow for read-write replicas
- Tables are created in multiple AWS regions and added to the same **Global Table**
  - tables become **table replicas** of a **Global Table**
- Between the tables, **last writer wins** in conflict resolution.
  - DynamoDB will pick the most recent write and replicate that.
  - this means that in the event of the same piece of data being written to two different tables at the same time, then DynamoDB will pick the most recent one and replicate it
- **Reads** and **Writes** can occur to **any region** and are **replicated** within **< 1 second**
- **Strongly Consistent READS** can be enabled, but **only in the same region as writes**.
  - for anything else, it's always **Eventual consistency**
  - Application should allow for eventual consistency where data may be stale.
  - Replication is generally sub-second and depends on the region load.
- Provides **Global HA** and **Global Disaster Recovery / Business Continuity** easily
- **LAST WRITER WINS** conflict resolution !!!!

### DynamoDB Accelerator (DAX)

- **In-memory cache for** DynamoDB
  - substantially improves performance
  - **EVENTUALLY** consistent

**Traditional Cache**: 
- application needs to access some data and checks the cache
- if the cache doesn't have the data, this is known as a **cache miss**
- application then loads directly from the database
- then it updates the cache with the new data
- subsequent queries will load data from the cache as a **cache hit** and it will be faster
- **problem**: lack of **integration** between **cache and database**

**DAX**: 
- application instance has **DAX SDK** added on
  - it **takes away the admin overhead** from the application
- DAX and DynamoDB are one and the same. 
- application **uses DAX SDK** and makes **a single call for the data** which is **returned by DAX**
  - the rest is handled by DAX
  - if DAX has the data, then the data is returned directly
  - if not it will talk to Dynamo and get the data
  - it will then cache it for future use
- the benefit of this system is there is **only one set of API calls** using **one SKD**. 
  - **less complexity** for the app developer - tighter integration

#### DAX Architecture

- DAX runs from within a VPC
  - designed to be deployed to **multiple AZs** in that VPC
  - must be deployed across AZs to ensure it is **highly available**

- DAX is a cluster service where nodes are placed into different AZs. 
  - there is **primary node** which is the **READ AND WRITE** node. 
  - this **replicates** out to other nodes which are **replica nodes** and function as **READ REPLICAS**. 
- with this architecture, we'd have an EC2 instance running an application and the DAX SDK. 
  - this will communicate with the cluster. 
  - on the other side, the cluster communicates with DynamoDB.

- DAX maintains **two different caches**. 
- First is the **ITEM CACHE** and this caches individual items which are retrieved via the **GetItem** or **BatchGetItem** operation. 
  - These operate on **single items** and **must specify** the items **Partition and Sort key** if it's there.
- Second is a **QUERY CACHE** which holds data of the **query or scan** based on query/scan **parameters**. 
  - Whole **query or scan** operations can be rerun and return the same cached data.

- Every **DAX cluster** has an **endpoint** which will **load balance** across the cluster.
  - If data is retrieved from DAX directly, then it's called a **cache hit** and the
results can be returned in **microseconds** (e.g. 400 microseconds)
  - Any **cache misses**, so when DAX has to consult DynamoDB, are generally returned in **single digit milliseconds**. 
- When **writing data** to DynamoDB, DAX can use **WRITE-THROUGH CACHING**
  - data is written into DAX **at the same time** as being written into the database. (**DDB then DAX**)
- If a **cache miss occurs while reading**, the database-retrieved data is also written to the **primary node** of the cluster
  - then it's **replicated** from the primary node to the **replica nodes**


#### DAX Considerations

- Primary node supports **WRITES** and Replicas support **READ** operations.
- Nodes are **HA**, if the primary node fails there will be an election and one of the replica nodes will be made primary.
- **In-memory cache** allows for **much faster read operations** and **significantly reduced costs**. 
  - If you are performing the same set of read operations on the same set of data over and over again, you can achieve performance improvements
by implementing DAX and caching those results.
- With DAX you can **scale up or scale out** (BOTH ARE ABOUT MAKING DAX BIGGER)
  - scale **UP** - bigger instances !!!
  - scale **OUT** - more instances !!!
- DAX supports **write-through**. 
  - If you write data to DynamoDB, you **can use the DAX SDK**. 
  - DAX will handle that data being committed to DynamoDB and also storing that data inside the cache.
- while DynamoDB is public AWS service, DAX is **NOT AN AWS PUBLIC SERVICE !!!** - it's deployed **inside a VPC**. 
  - anything that uses DAX will have to be **deployed into that VPC**
- any questions which talk about **caching** with DynamoDB, assume it is DAX !!!
- **EXAM**: 
  - performance issues during sale periods or have specific tables or items in a table, with heavy read workloads against that area of data -> DAX
  - the same set of data being read again and again -> DAX
  - very low response times -> DAX
  - apps that require **STRONGLY CONSISTENT Reads** -> **NOT** DAX
  - app is **write-heavy** and very infrequently uses read operations -> **NOT** DAX

### Amazon Athena

- **Serverless** Interactive Querying Service
  - it means we can **take data stored in S3** and **perform AD-HOC QUERIES** on that data. 
  - pay only for the **data consumed**: no base cost or setup time
- Start off with **structured, semi-structured and even unstructured data** that is stored in its **raw form** on **S3**.
- Athena uses **Schema-on-read**
  - schema translated data => **relational-like** when read
- the **original data** is **never changed** and **remains on S3** in its original form.
- Normally with databases, you need to make a table and then load the data in.
- With Athena you create a schema and load data on this schema on the fly in
a relational style way without changing the data.
- The output of a query can be sent to other services and can be
performed in an event driven fully serverless way.
 
#### Athena Explained

- inside Athena we create a **SCHEMA** (Schema-on-read)
- inside this **SCHEMA** we define **TABLES**
- these **TABLES** define how to get **from the format of original source data** to a **table structure**
  - this is **unlike** to **Relational Databases** where **table is the final structure**
- data is projected through the tables when read
- it **allows SQL-like queries** on data without **transforming** source data
- output can be sent to visualisation tools
  - we can optimize the original data set to reduce the amount of space used



### ElastiCache

- **in-memory** database with **high performance**
- managed **Redis** or **Memcached** as a service
- can be used to cache data - for **READ HEAVY** workloads with **low latency** requirements
- **reduces** database **workloads** (COST EFFECTIVE)
- can be used to store **SESSION Data** (**STATELESS** Servers)
  - Session State is written to ElastiCache
  - if the instance the user was using fails, the session state is still kept in the cache and the session continues with no interruption
- **requires application code changes !!!**
  - app needs to understand caching architecture


#### **Redis** vs **Memcached**:
- both engines offer < 1ms access to data
- both support range of programming languages
- support of data structures:
  - Memcached - **simple** data structures (strings)
  - Redis - **advanced** data structures (lists, sets, sorted sets, hashes, bit arrays, and more)
- replication:
  - Memcached 
    - **NO REPLICATION !!!**
    - multiple nodes (**sharding**)
  - Redis 
    - **Multi-AZ !!!**
    - replication (Scale Reads)
- backups:
  - Memcached - **no backups**
  - Redis - **backup & restore**
- performance:
  - Memcached - **Multi-thread !!!** - super fast
  - Redis - **Transactions !!!** (treating operations as one, i.e. either all of the operations work or none work at all)