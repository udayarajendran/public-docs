**Route 53**

**DNS**

- Domain name system to convert human friendly hostname to IP address

**DNS Terminologies**

- Domain Registrar - Amazon route 53, Google domains, GoDaddy
- DNS Records A, AAAA, CNAME, NS
- Zone file - Contains DNS Records
- Top Level Domain - .com, .us, .in, .gov, .org
- Second Level Domain - amazon.com, google.com

**How DNS Works**


**Route 53 Overview**

- Highly available, scalable, fully managed and authoritative DNS
- Route 53 is also a Domain Registrar
- 100% of SLA compared to all AWS services
- 53 is a traditional DNS port


**Route 53 - Records**

- How you want to route traffic
- Each record has
    - Domain/subdomain Name
    - Record Type A or AAAA or CNAME
    - Value
    - Routing Policy
    - TTL - record caching time
- **Records supported**
    - A/ AAAA/ CNAME/ NS
    - Advanced - CAA/ DS/ MX/ NAPTR/ PTR/ SOA/ TXT/ SPF/ SRV
- **Record Types**
    - A - Maps hostname to IPv4
    - AAAA- maps hostname to IPv6
    - CNAME - hostname to hostname and target hostname with A record
    - NS - Name servers for the Hosted Zone

**Route 53 - Hosted Zones**

- Container for records that define how to route traffic to a domain and its subdomains
- Public Hosted Zones - contains record on how to route traffic over the internet (Public Domain Names)
- Private Hosted Zones - contains record specify how you route traffic within one or more VPCs (Private domains - within an organization)
- $0.50 cents per month per hosted zone
    

**Route 53 - EC2 Setup**

- Create EC2 Instance on 3 different region
- ALB - Application Load Balancer

**Route 53 - Records TTL (Time to Live)**



**Route 53 - CNAME Vs Alias**

- CNAME points to hostname to any other hostname
- Only works for non root domain.

**Alias**

- Hostname to other AWS resources
- Works for root domain and non root domain
- Free of charge
- Native Health check

 

**Route 53 Routing Policies** 

- Scalable DNS web service
- Used to control how Route 53 responds to DNS queries
- Routes it to resources based on location of the user, health of resources and other factors

**Routing Policies**

- **Simple**
    - Route traffic to single resource
    - A record
    - Multiple values in same record
    - Replies with all 3 records and user chooses on what to route to

- **Weighted**
    - % of resources can be controlled to go to each specific resources
        
 
    - DNS record must have same name and type
    - Can be associated with Health Checks
    - Use cases - load balancing between regions, testing new application versions
    - Assign a weight of 0 to a record to stop sending traffic to a resources
    - If all records have weight of 0, then all records with be returned equally
- **Latency**
    - This policy allows user to lowest network latency for end user
    - Latency record can be set for each AWS region
- **Health Checks**
    - Only for public resources
    - Health check = Automated DNS Failover
        - Monitor Endpoints
            - About 15 health checkers globally
            - Healthy / Unhealthy Threshold 3
            - Interval - 30 sec
            - Supported Protocol - HTTP, HTTPS and TCP
            - If>18% health checkers reports the endpoint is healthy, considers its healthy or unhealthy
            - Passes only when the end points responds with 2xx or 3xx status codes
            - Health checks can be setup to pass/fail based on the text in the first 5120 bytes of response
            - Configure firewall to allow incoming request form Route 53 Health Checkers
        - Monitor other health checks
        - Monitor CloudWatch
    - Health checks are integrated with CW metrics
- **Failover**
    

- **Geolocation**
    - Can set to have traffic routed to set resources based on user location/IP.
- **Geo-proximity Routing Policy**
    - Route traffic based on the resources based on the geographic location of users and resources
    - This can be achieved by changing the bias value
- **Multi-value**
    - used when routing traffic to multiple resources
    - Route 53 return multiple values/resources
    - can associate with Health checks
    - up to 8 healthy records are returned for each multi-value query
    - not a substitute for having ELB

3rd Party Domains and DNS Services

- Even when you purchase your domain name from 3rd party you can still get the domain name services on Amazon Route 53
    - Create a hosted zone in Route 53
    - Update NS record on 3rd party website to use Route 53 Name servers
    - $.50 every month

**Solution Architectures**

**Stateless Web App: WhatIsTheTime.com**

**Simple use case:**



**Vertical Scaling:**


**Horizontal Scaling:**


**Route 53:**


**Load balancer:**

**Auto-scaling:**


**Multi-AZ:**

**Reserve Capacity:**


**Stateful Web App: MyClothes.com**

- Shopping cloths
- Shopping carts
- Hundreds of users
- Scale maintain horizontal scalability/stateless as possible
- Users should not lose their shopping carts
- Users should have details in their database

**Session Affinity(Stickiness)**


**User Cookies**

**Server Sessions - Server ID**


**Storing User Data in Database -  Amazon RDS**



**Scaling Reads - When users only wants to browse and read data**


**Lazy Loading - Using Cache (ElastiCache)**

Reduces CPU stress by maintaining Cache Maintenance on application side


**Multi AZ - Survive disaster**


**Security Groups**


**3-tire architecture for web applications**


**Stateful Web App: MyWordPress.com**

- fully scalable wordpress websites
- Access and correctly display picture uploads
- User data, and blog content should be stored in MySQL Database

**RDS Layer**


**With Aurora: Multi AZ & Read Replicas**



**Storing Images with EBS**


**EFS**

EBS is good **until** when we decide to scale - each Instance will have its own EBS Volume and hence if user goes to different instance while load balancing might not see the the image on other instance

How EFS helps- **Creating a EFS will create ENI’s - Elastic Network Interface for each instances** for it to connect to EFS



**Instantiating Application Quickly**

- Install application
- Insert initial (or recovery) data
- Configure everything
- Launch the application

**To speed up these process**

- EC2 Instance
    - Golden AMI - install your application, OS dependencies etc.. beforehand and launch your EC2 instance from the Golden AMI
    - Bootstrap using User Data - for dynamic configuration, use User Data scripts
    - Hybrid - Mix of Golden AMI and user Data (Elastic Beanstalk)
- RDS Databases
    - Restore from Snapshot - database will have schemas and data ready
- EBS Volumes
    - Restore from Snapshot - disk will have already be formatted and have data

**Elastic Beanstalk**

**Before that - Developer problems**

- Managing infrastructure
- Deploying code
- Configuring all the databases, load balancer, etc.
- Scaling concerns

**Common points** 

- All the webapps have the same architecture (ALB+ASG)
- All developers want is for their code to run
- Possibly, consistently across different applications and environments

Overview

- developer centric view
- Handles all the components we’ve seen before - EC2, ASG, ELB, RDS.
- Managed services
    - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration
    - Just the code for developer
    - Beanstalk is free - But you pay for underlying instances
    

**Elastic Beanstalk Components**

- Application - Collection of Elastic Beanstalk components (Environments, Versions, Configurations)
- Application version - An iteration of the code
- Environment
    - Collection of AWS resources running application versions
    - Tires Web Server Environment Tier and worker environment tier
    - Multiple environment - dev, test, production

Create application - Upload version - Launch Environment - Manage Environment



**Web Server Tier vs Worker Tier**


**Worker Tier**

- Scaled based on number of SQS messages
- Can push messages to SQS queue from another Web Server Tier

**Beanstalk Deployment Modes**

- Single instance - for dev
- High availability with load balancer - for prod



**Elastic Beanstalk** 


**Overview:**

- Developer Centric View
- Uses all the components that we have seen before EC2 ASG ELB RDS….
- Managed services
    - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration…
    - Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Beanstalk is free but you have to pay for the underlying instances

**Components:**

- Application - Collection of Elastic Beanstalk components (environments, versions, configurations,…)
- Application Version - iteration of your application code
- Environment
    - Collection of AWS resources running an application version (only one application version at a time)
    - Tires: Web Server Environment Tier & worker Environment Tier
    - You can create multiple environments (Dev,test,prod..)


**Supported platform**

- Go
- Java SE
- Java with Tomcat
- .NET Core on Linux
- .NET Core on Windows Server
- Node.js
- PHP
- Python
- Ruby
- Packer Builder
- Single Docker Container
- Multi Docker Container
- Preconfigured Docker

**Web Server Tier / Worker Tier**


- SQS Queue to save all the messages and worker will process the messages
- More the messages more scaling

**Beanstalk Deployment mode**

Single Instance / High Availability with load balancer


**Amazon S3 Section**

Introduction

Building blocks of AWS

Infinitely scaling storage

May website uses Aws S3 as backbone

Amazon S3 as integration

**Use Cases**

Backup and storage

Disaster Recovery

Archive

Hybrid Cloud Storage

Application Hosting

Media Hosting

Data lakes & big data analytics

Software delivery

Static Websites

**Amazon S3 buckets**

- Stores objects (files) in “buckets” (directories)
- Globally unique name (Across all regions / all accounts)
- Defined on a region level
- Naming convention - no upper case, no underscore, 3-63 characters, Not IP, starts with lowercase letter or a number, must not start with prefix xn—, must not end with suffix -s3alias

**S3 Objects**

- have keys
- key is the full path, example

    
- Key is composed of Prefix + Object name

    
- There is no concept of directories within buckets
- Just keys with very long names that contain slashes/
- contents of the body
- Max 5TB (5000GB)
- Object more than 5GB should be uploaded into multi part upload
- Metadata (List of text key / value pairs - system or user metadata)
- Tags (Unicode key / Value pair - up to 10) - useful for security / life cycle
- Version ID

 **Amazon S3 Security**

- User Based
    - IAM policy
- Resource Based
    - S3 bucket policy - allows cross connect
    - Object Access Control List
    - Bucket Access Control List
- IAM principal can access an S3 object if
    - IAM permission is allowed or resource policy is allowed
    - No Explicit deny
- Encryption - using encryption keys

**S3 bucket policy**

- **JSON based policy**
    - Resources - bucket and objects
    - Effect - Allow / Deny
    - Action - Set of API to Allow / Deny
    - Principal - Account or user to apply the policy to
- **S3 bucket for policy to**
    - Grant public access to the bucket
    - Force objects to be encrypted at upload
    - Grant access to another account (Cross Account)

Block public access - Default to ON - Turn it off to be accessible over the internet

AWS policy generator - to generate JSON file

Get-Objects - Applies objects within your buckets

**Amazon S3 - Static Website Hosting**

- To host static websites and have them accessible on internet
- URL depends on the region
  
- **403 - Forbidden error - Might need to check on read access**

**S3 Versioning**

- Can be versioned
- Enabled at bucket level
- Same key overwrite will change the Version 1,2,3…
- Best practice to Version your buckets
    - Protects against unintended deletes
    - Easy roll back
- Any file that is not Versioned prior to enabling Versioning will have null
- Suspending versioning does not delete the previous versions

**Amazon S3 - Replication (CRR & SRR)**

- Must enable versioning in source and destination
- Cross Region replication
- Same Region replication
- Buckets can be in different AWS accounts
- Copying is asynchronous
- Must give proper IAM permissions to s3
- Use Case
    - CRR - Compliance, lower latency access, replication across accounts
    - SRR - log aggregation, live replication between production and test accounts
- After you enable replication, only new objects are replicated
- Optionally you can replicate existing objects using Batch replication
- For Delete operations
    - Can replicate delete markers from source to target
    - Deletion with a version ID are not replicated
- No “Chaining” of replication
    - if bucket 1 has replication into bucket 2, which has replication into bucket 3
    - Then objects created in bucket 1 is not replicated on bucket 2

**S3 Storage Classes**

- Amazon S3 Standard - General Purpose
- Amazon S3 Standard - Infrequent Access IA
- Amazon S3 One Zone - Infrequent Access
- Amazon S3 Glacier Instant Retrieval
- Amazon S3 Glacier Flexible Retrieval
- Amazon S3 Intelligent Tiering
- Can move between classes manually or using S3 Lifecycle configurations

**S3 Durability and Availability**

- Durability - 11 9’s.
- Availability - 99.99% availability - 53 minutes a year not available

**S3 - Standard - General purpose**

- 99.99% available
- used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures
- use case - big data analytics, mobile & gaming applications, content distribution…

**S3 Storage class - Infrequent access**

- Less frequently accessed - requires rapid access when needed
- lower cost than standard
- 99.9% available - standard IA
- use case - Disaster recovery , backups
- one zone IA - 99.99999999% in a single AZ, data lost when az is destroyed
- 99.5% Availability
- use Cases - storing secondary backup copies of on-premise data, or data you can recreate

**S3 Glacier Storage classes**

- Low-cost object storage meant for archiving / backup
- Pricing price for storage + object retrieval
- Instant retrieval
    - Millisecond retrieval, great for data accessed once a quarter
    - Minimum storage duration of 90 days
- Flexible retrieval
    - Expedited (1 to 5 min) standard (3 to 5 hours), Bulk (5 to 12 hours) - free
    - Minimum storage duration of 90 days
- Deep Archive - for long term storage
    - standard 12 hours , bulk 48 hours
    - Minimum storage duration of 180 days

**S3 intelligent-Tiering**

- Small monthly monitoring and auto-tiering fee
- Moves object automatically between access tiers based on usage
- There are no retrieval charges in s3 Intelligent - Tiering
- Frequent access tier - default tier
- Infrequent access tier - objects not accessed for 30 days
- archive instant access tier - not accessed for 90 days
- Archive access tier - configurable from 90 days to 700+ days
- Deep Archive access tier - config. from 180 to 700+ days

**Advance Amazon S3 tire**

Amazon S3 - Moving between Storage Classes using Lifecycle Rules

Amazon S3 - Lifecycle Rules 

- Transition actions
- Expiration actions

S3 Requester Pays

- Usually owner pays for both storage cost and network transfer cost
- When Requester pays bucket the requester will pay for the networking transfer cost

**S3 Event Notifications**

- can have notification sent out when below events takes place
    - object created
    - removed
    - restored
    - Replication
- Objects name can be filtered
- use case - general thumbnail of images uploaded to S3
- Can create as many “s3 events” as desired
- S3 events notification delivery can take about a min longer in some case

**S3 Event Notification with Event Bridge**

**S3 Baseline Performance**

- automatically scales to high request rates, latency 100-200 ms
- Application can achieve at lease 3500 put/copy/post/delete or 5500 get/head requests per second per prefix in a bucket
- No limits of prefixes in a bucket
- bucket/folder1/sub1/file = /folder1/sub1

S3 Performance

Multi-part upload

- Recommended for files >100 Mbps
- must use for files > 5GB
- Can help parallelize uploads


S3 Transfer Acceleration

- Increase transfer speed by transferring file to an aws edge location which will forward data to the s3 bucket in the target region
- Compatible with multi-part upload
    


S3 Byte-Range Fetches

- parallelize GETs by requesting specific byte range
- Better resilience in case of failures
- Can be used to speed up downloads
    
- Can be used to retrieve only partial data
    


**S3 select and glacier select**

- Retrive less data using SQL by performing server -side filtering
- Can filter by rows and columns (Simple SQL statements)
- Less network transfer, less CPU cost client-side


**S3 Batch Operations**

- Perform bulk operations on existing S3 objects with a single request
    - Modify object metadata & properties
    - copy objects between s3 buckets
    - Encrypt un-encrypted objects
    - Modify ACLs, tags
    - Restore objects from s3 glacier
    - invoke lambda function to perform custom action on each object
- A job consist of a list of objects, the action to perform and operational parameters
- S3 batch operation managers retries, tracks progress sends completion notifications, generates reports…
- You can use s3 inventory to get object lists and use s3 select to filter your objects
    

    

**Amazon Security**

**Amazon S3 - Object encryption**

- 4 methods
    - SSE - Server-Side Encryption
        - SSE-S3 - Server-Side Encryption with Amazon S3 managed Keys
        - SSE-KMS - Server-Side encryption with KMS Keys stored in AWS KMS
        - SSE-C - Server-Side encryption with Customer-Provided key
    - Client side encryption
- Important to understand which ones are for which situation for the exam

**SSE-S3** 

- Envryption using keys handled, managed, and owned by AWS
- Object is encrypted server-side
- Encryption type is AES-256
- Must set header “X-amz-server-side-encrytion”:”AES256”
- Enabled by default for new buckets and new objects

**SSE-KMS**

- Encryption using keys handled and managed by AWS KMS (Key Management Service)
- KMS advantages: user control + audit key usage using cloudtrail
- object is encrypted server side
- Must set header “x-amz-server-side-encryption”: “aws:kms”
    


**Limitations**

- if you use sse-kms you may be impacted by the kms limits
- When you upload, it calls the GeneratedDataKey KMS API
- When you download, it calls Decrypt KMS API
- Count towards the KMS quota per second 5500.10000, 30000 req/s based on region
- You can request a quota increase using the Service Quota Console

    

**SSE-C**

- Server side encryption using keys fully managed by the customer outside of AWS
- Do not store the encryption keys that you provide
- HTTPS must be used
- Encryption key must provide in HTTP headers, for every HTTP request mode
    

**Client Side Encryption**

- Uses Client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt data themselves before sending to amazon S3
- Clients must decrypt data themselves when retrieving from Amazon s3
- Customer fully manages the Keys and Encryption cycle


Amazon S3 - Encryption in Transit (SSL/TLS) 

- Encryption in flight is also called as ssl/tls
- Amazon S3 exposes two endpoints
    - Http Endpoint - non encrypted
    - HTTPS Endpoint - Encrypted
- HTTPS is mandatory for SSE-C
- Most client would use HTTPS endpoint by default

Forcing encryption in transit

- aws:SecureTransport


Default encryption vs Bucket policy

CORS - Cross Origin Resource Solution

- Origin  = Scheme(protocol) + host (domain) + port
- Web Browser based mechanism to allow request to other origins while visiting the main origin

    
- If client makes a cross origin request on our s3 bucket we need to enable the correct CORS headers
- you can allow specific origin or all origins *


**Amazon S3 - MFA Delete**

**Multi Factor Authentication**

- Forces user to generate a code on a device before doing important operation on S3
- MFA will require to
    - permanently delete an object version
    - Suspend versioning on the bucket
- MFA wont be required to
    - Enable versioning
    - List deleted versions
- To use MFA Delete, Versioning must be enabled on the bucket
- Only root account / owner of the bucket can enable or disable it

S3 Access Logs

- For audit purpose, you may want to log all access to S3 buckets
- Any request made to S3, from any account, authorized or denied, will be logged into another s3 bucket
- The data can be analyzed using data analysis tool
- The target logging bucket must be in the same AWS region

Warning

- Do not set your logging bucket to be the monitored bucket
- Logging bucket will grow exponentially and cause a loop

Amazon S3 - Pre-Signed URLs

- s3 console, aws cli or sdk
- URL Expiration
    - S3 Console - 1 min upto 720 min (12 hours)
    - AWS CLI - configure expiration with —expires-in parameter's in seconds(default 3600 secs, max. 604800
     
    
    **S3 Glacier Vault Lock**
    
    - Adopt a WORM (Write once and read Many) model
    - Create a Vault Lock Policy
    - Lock the policy for future edits (can no longer be changed or deleted)
    - Helpful for compliance and data retention
   
    
    S3 object Lock (Versioning must be enabled)
    
    - Adopt a WORM (Write Once Read Many)
    - Block an object version deletion for a specified amount of time
    - Retention mode - Compliance:
        - Object versions can’t be overwritten or deleted by any user, including the root user
        - Objects retention modes cant’ be changed, and retention periods can’t be shortened
    - Retention mode - Governance:
        - Most users can’t overwrite or delete an object version or alter its lock settings
        - Some users have special permissions to change the retention or delete the object
    - Retention Period:
        - Protect the object for a fixed period, it can be extended
    - Legal Hold
        - Protect the object indefinitely, independent from retention period
        - Can be freely placed and removed using the s3:PutObjectLegalHold IAM permission
    
    **S3 Access Points**
    
    - Access points simplify security management for S3 Buckets
    - Each Access Point has:
        - its own DNS name (Internet origin or VPC Origin)
        - an access point policy (Similar to bucket policy) - manage security at scale
    
    **VPC Origin**
 
    
- We can Define the access point to be accessible only from within the VPC
- You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
- The VPC Endpoint policy must allow access to the target bucket and Access Point

S3 Objects Lambda

- Use Aws Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Points
- Use Cases
    - Redacting personally identifiable information for analytics or not production environments
    - Converting across data formats such as converting XML to JSON.
    - Resizing and watermarking images on the fly using caller specific details, such as the user who requested the object.

AWS CloudFront

- Content Delivery Network (CDN)
- Improves read performance, content is cached at the edge
- Improves user experience
- 216 Point of presence globally (Edge Location)
- DDoS Protection (because worldwide), integration with Shield, Aws Web Application Firewall

CloudFront Origins

S3 Bucket

- For distributing files and caching them at the edge
- Enhanced security with CloudFront Origin Access Control(OAC)
- OAC is replacing Origin Access Identity (OAI)
- CloudFront can be used as an ingress (To up load files to S3)

Custom Origin (HTTP)

- Application Load Balancer
- EC2 instance
- S3 website (Must first enable the bucket as a static S3 website)
- Any HTTP backend you want

CloudFront at a high level


CloudFront - S3 as an Origin

**CloudFront VS S3 Cross Region Replication**

**CloudFront**

- Global Edge network
- Files are cached for a TTL (maybe a day)
- Great for static content that must be available everywhere

**S3 Cross region replication**

- Must be setup for each region you want to replication to happen
- Files are updated in near real time
- Read only
- Great for dynamic content that needs to be available at low latency in few regions

**CloudFront - ALB or EC2 as an origin**

**CloudFront Geo Restriction**

- You can restrict users accessing your S3 based on their location

**CloudFront - Pricing**

- All around world price differs

**Cache Invalidations**

- Incase you update the back-end origin, CloudFront doesn’t know about it and will only get the refreshed content after the TTL has expired
- However, you can force an entire or partial cache refresh (Thus invalidating the TTL) by performing a CloudFront Invalidation
- You can invalidate all files or special path /images/*
  
Global users for our applications

- You have deployed an application and have global users who want to access it directly.
- They go over the public internet, which can add a lot of latency due to may hops
- We wish to go as fast as possible through AWS network to minimize latency
    
   

Unicast IP and Anycast IP

- Unicast IP - One server holds one IP address
- Anycast IP - All server holds same IP and client is routed to the nearest one

AWS Global Accelerator

- Leverage internal AWS network to your application
- 2 Anycast IP are created for your application
- The anycast IP send traffic directly to Edge Locations
- The Edge locations send the traffic to your application
    
- Works with -Elastic IP, Ec2 instances, ALB NLB, public or private
- Consistent performance
    - Intelligent routing to lowest latency and fast regional failover
    - No issues with client cache - cause IP doesn’t change
    - Internal network
- Health Checks
    - Global accelerator performs a health check of your applications
    - Helps make your application global (Failover is less than 1 minute for unhealthy)
    - Great for disaster recovery (thanks to the health checks)
- Security
    - Only 2 external IP needs to be whitelisted
    - DDoS Protection thanks to AWS Shield

AWS Global Accelerator vs CloudFront

- Both use the AWS global network and its edge location around the world
- Both services integrate with AWS Shield and DDoS Protection.

CloudFront

- Improves performance for both cacheable content
- Dynamic content (API acceleration and dynamic site delivery)
- Content is served at the edge

Global Accelerator 

- Improves performance for a wide range of applications over TCP or UDP
- Proxying packets at the edge to application running in one or more AWS regions
- Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT) or Voice over IP
- Good for HTTP use cases requiring static IP, deterministic, fast regional failover

AWS Snow Family 

- Highly secure portable device, to collect and process data at the edge and migrate into and out of AWS
 
- Data migration
- Edge computing

Why Snow Family?

- Challenges?
    - Limited connectivity
    - Limited bandwidth
    - High network cost
    - Shared bandwidth (Can’t maximize the line)
    - Connection stability
- AWS Snow family to perform data migrations
- Rule of thumb - If it takes more than a week to transfer data over network, use SNOWBALL DEVICES!
   
Snowball Edge (for data transfers)

- Physical data transport solution: move TBs or PBs of data in or out of AWS
- Alternative is moving data over the network (and paying for the network fees)
- Pay per data transfer job
- Provide block storage and Amazon S3-compatible object storage
- Snowball Edge Storage Optimized
    - 80 TB of HDD capacity for block volume and S3 compatible object storage
- Snowball Edge Compute Optimized
    - 42 TB of HDD or 28 TB NVMe capacity for block volume and S3 compatible object storage
- Use Cases: Large data cloud migrations, DC decommission, disaster recovery

AWS Snowcone & Snowcone SSD

- Small, portable computing anywhere, rugged & Secure, withstands harsh environments
- Light (4.5 pounds, 2.1 Kg)
- Device used for edge computing, storage, and data transfer
- Snowcone - 8TB of HDD Storage
- Snowcone SSD - 14 TB of SSD Storage
- use Snowcome where snowball doesn’t fit (Space constrain environment)
- Must provide your own battery / cables
- Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data

Aws Snowmobile

- Transfer exabytes of data (1EB = 1000 PB = 1000000 TB
- Each snowmobile has 100 PB of capacity (use multiple in parallel)
- High Security: temperature controlled, GPS, 24/7 video surveillance
- Better than Snowball if you transfer more than 10 PB

Snow Family - Usage Process

- Request Snowball devices from the AWS console for delivery
- Install the snowball client / AWS OpsHub on your servers
- Connect the snowball to your servers and copy files using the client
- Ship back the device when you’re done (goes to the right AWS facility)
- Data will be loaded into an S3 bucket
- Snowball is completely wiped

Snow  Family - Edge computing

- Process data while it’s being created on an edge location
    - A truck on the road, a ship on the sea, a mining station underground
- These location may have
    - Limited to no internet access
    - Limited to easy access to computing power
- We setup a snowball edge / snowcone device to do edge computing
- Use cases of Edge Computing
    - Preprocessed Data
    - Machine Learning at the edge
    - Transcoding media streams
- Eventually we can ship back the device to AWS (for transferring data for example)
  

AWS OpsHub

- Software to install and use on snow devices

Solution Architecture : Snowball into Glacier

- Cannot import to Glacier directly
- You must use S3 first and in combination with an S3 Lifecycle policy

Amazon FSx

- launch 3rd party high performace file systems on AWS
- Fully managed services
    - FSx for Lustre
    - FSx for NetApp ONTAP
    - FSx for Windows File Server
    - FSx for OpenZFS

Amazon FSx for Windows (File Server)

- FSx for Windows is a fully managed Windows file System share drive
- Supports SMB protocol and windows NTFS
- Microsoft Active Directory integrations, ACLs, user quotas
- Can be mounted on Linux EC2 instances
- Supports Microsoft’s Distributed File Systems (DFS) Namespaces (group files across multiple FS)
- Scales up to 10s of GBs, millions of IOPS, 100s PB of Data
- Storage options:
    - SSD- Latency sensitive workloads
    - HDD - Broad spectrum of workload
- Can be accessed from your on-premises infrastructure (VPN or direct connect)
- Can be configured to be Multi-AZ (High availability)
- Data is backed-up daily to S3

Amazon FSx for Lustre

- distributed file system for large scale computing
- Linux cluster
- Machine learning HPC
- Video processing, financial modeling, electronic design automation
- Scales up to 100s GB/s , millions of IOPS, sub-ms latencies
- Storage options
    - SSD - low latency, IOPS intensive workloads small, random file operations
    - HDD, throughput-intensive, large, sequential file operations
- Seamless integration with S3
    - Can read s3 as a file system through FSx
    - Can write the output of the computation back to S3 through FSx
- Can be used from on-premises servers VPN or direct connection

FSx File system deployment options

- Scratch File System
    - Temporary storage
    - Data is not replicated (Doesn’t persist if file server fails)
    - High burst (6x faster, 200 MBps per TiB)
    - Usage: short-term processing, optimize costs
- Persistent File Systems
    - Long term storage
    - Data is replicated within same AZ
    - Replace failed files within minutes
    - Usage - Long term processing, sensitive data
   

Amazon FSx for NetApp ONTAP

- Manged NetApp ONTAP on AWS
- File System compatible with NFS, SMB, iSCSI protocol
- Move workloads running on ONTAP or NAS to AWS
- Works with
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspace & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Storage shrink or grows automatically
- Snapshots, replication, low-cost, compression and data de-duplication
- Point-in-time instantaneous cloning (Helpful testing new workloads)

Amazon FSx for OpenZFS

- Manged openZFS file systems on AWS
- File System compatible with NFS (V3, V4, V4.1, V4.2)
- Move workloads running on ZFS to AWS
- Works with
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspace & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Upto 1000000 IOPS with <0.5 ms latency
- Snaphots, compressed and low cost
- Point-in-time instantaneous cloning (helpful for testing new clones/workloads)

Hybrid Cloud for storage

- Using AWS storage Gateway

AWS Storage Gateway

- bridge between on-premises data and cloud data
- Types of Storage Gateway
    - S3 File Gateway
    - FSx File gateway
    - Volume gateway
    - Tape gateway

Amazon S3 File Gateway

- cannot use s3 glacier bucket others can be used
   
Amazon FSx file gateway

- Native access to Amazon FSx for windows file server
- Local cache for frequently accessed data
- Windows native compatibility (SMB, NTFS, Active Directory…)
- Useful for group file shares and home directory
  

Volume Gateway

- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes
- Cached volumes: low latency access to most recent data
- Stored volumes: entire dataset is on premise, scheduled backups to S3
    
 
Tape Gateway

- Physical tape
- Same protocol but in cloud
- VTL Virtual Tape Library backed by Amazon S3 and Glacier
- backup data using existing tape-based processes (and iSCSI interface)
- Works with leading backup software vendors
    

Storage gateway - Hardware appliance

- Using storage gateway means you need on-premises virtualization
- Otherwise you can use a storage gateway hardware appliance
- You can buy it on Amazon.com
- Works with File gateway, Volume gateway, Tape gateway
- has required CPU, memory, network, SSD cache resources
- Helpful for daily NFS backups in small data centers
    
  

AWS Storage Gateway 

AWS Transfer Family

- Fully managed services for file transfers into and out of amazon s3 or amazon efs using FTP Protocol
- Supported Protocols
    - Aws Transfer for FTP
    - for FTPS
    - for SFTP
- Managed infrasturucture, scalable, reliable, highly available multi AZ
- Pay per provisioned endpoint per hour + data transfers in GB
- Store and manage users credentials withing the service
- Integrate with existing authentication system (Microsoft AD, LDAP, Okta, Amazon Cognito, custom)
- Usage - Sharing files, public datasets, CRM, ERP, ….

AWS DataSync

- Move large amount of data to and from
    - on-premises / other cloud to aws - needs agent
    - AWS to AWS (Different storage services) no agent needed
- Can synchronize to
    - Amazon S3 (Any storage classes including Glacier)
    - Amazon EFS
    - Amazon FSx
- Replication tasks can be scheduled hourly, daily, weekly
- File permissions and metadata are preserved (NFS POSIX, SMB …)
- One agent task can use 10 Gbps, can setup a bandwidth limit

**Decoupling applications: SQS, SNS, Kinesis, Active MQ**

**AWS Integration and Messaging**

**SQS, SNS & Kinesis**

- Multiple application should be able to communicate to each other
- There are few ways
    - Synchronous Communication
    - Asynchronous / Event based Communication
  
- When there is sudden spike of traffic bet one application and other there might be a problem in how the second application might be able to process the info
- Hence there is Decouple
    - using SQS - queue model
    - using SNS - pub/sub model
    - using Kinesis - real time streaming model
- These services can scale independently from our application!

**SQS - Standard queue**

- Oldest
- Fully managed service used to decouple application
- Attributes
    - unlimited throughput, unlimited no of messages in queue
    - Default retention of messages 4 days maximum of 14 days
    - Low latency
    - Limitation of 256KB per message
    - Can have duplicate
    - Can have out of order message

SQS Producing Message

SQS Consuming Message

**Amazon SNS**

Simple Notification topic

- Has SNS topic to do the work - send out message for all services at once
- Event producer sends message to one SNS topic
- As many event receivers as we want to listen to SNS topic notification
- Each subscriber to the topic will get all the message
- upto 12500000 subscriptions per topic
- 100000 topics limit
   

SNS can be integrated within a lot of AWS services

SNS - How to publish

- Topic publish
    - create a topic
    - Create a subscription or many
    - Publish to the topic
- Direct Publish
    - Create a platform application
    - Create a platform endpoint
    - Publish to the platform endpoint
    - Works with Google GCM, Apple  APNS, Amazon ADM,…

Amazon SNS Security

- Encryption
    - in flight encryption using HTTPS API
    - At rest encryption using KMS Keys
    - Client side encryption if the client wants to perform encryption / decryption itself
- Access control
    - IAM policy to regulate access to the SNS API
- SNS Access Policies (Similar to S3 bucket policy)
    - Useful for cross account access to SNS topics
    - Useful for allowing other services (S3…) to write to an SNS topic

SNS + SQS - FAN out

- Push once in SNS, receive in all SQS queues that are subscribers
- Fully decoupled, no data loss
- SQS allow for data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue access policy allows for SNS to write
- Cross region delivery - works with SQS in other regions

Application - S3 events to multiple queues

- For the same combinations of : event type and prefix you can only have one S3s event rule
- If you want to send the same s3 event to many sqs queues use fan out
  
Application - SNS to Amazon S3 through Kinesis Data Firehose

- SNS can send to Kinesis and there fore we can have the following solutions architecture
    
 

Amazon SNS - FIFO Topic

- First in First Out

- Incase if we want to do fan out

SNS - Message Filtering

- JSON policy filters message to SNS topic’s subscription
- if doesn't have filter if receives every message
   
Kinesis Overview

- Makes it easier to collect process and Analyze streaming data in real time
- Ingest real time data such as - Application logs Metrices, website clickstreams, IoT telemetry data
- Kinesis Data Streams - Captures, processes and store data streams
- Kinesis Data Firehose - load data streams into AWS data store
- Kinesis Data Analytics - analyze data stream with SQL or Apache Flink
- Kinesis Video Streams - capture, process and store video streams

Kinesis data Streams

- retention bet  1 day to 365 days
- Ability to reprocess data
- Once data is inserted in kinesis, it can be deleted
- Data that shares the same partition goes to the same shard
- Producers - AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
- Consumers
    - Write your own - Kinesis Client Library KCL, AWS SDK
    - Managed - Lambda, Kinesis Data Firehose, Kinesis Data Analytics

Kinesis Data Stream - Capacity Mode

- Provisioned Mode
    - You choose the number of shards provisioned, scale manually or using API
    - Each shard gets 1 MB/s in (or 1000 records per second)
    - Each shard gets 2 MB/s out (classic or enhanced fan-out consumer)
    - You pay per shard provisioned per hour
- On-demand mode
    - No need to provision or manage the capacity
    - Default capacity provisioned (4MB/s in or 4000 records per second)
    - Scales automatically based on observation through put peak during the last 30 days
    - Pay per stream per hour & data in / out per GB

Kinesis data Firehose

- Fully managed service, no administration, automatic scaling, serverless
    - AWS Redshift/ Amazon S3 / Open Search
    - 3rd party partner : Splunk / Mongo DB / Datadog / NewRelic
    - Custom - Send to any HTTP Endpoint
- Pay for real time
    - 60 seconds latency minimum for non full batches
    - or minimum 1MB of data at a time
    - Supports many data formats, conversions, transformations, compression
    - Supports custom data transformations using AWS Lambda
    - Can send failed or data to a backup S3 bucket

SQS vs SNS vs Kinesis

Amazon MQ

- SQS and SNS are Cloud Native,
- Traditional applications running from on-premises may use open protocols such as MQTT, AMWP, STOMP, Openwire, WSS
- When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- MQ is a managed message broker service for RabbitMQ and ACTIVEMQ
- Doesn’t scale as much as SQS/SNS
- MQ runs on server, can run in multi AZ with failovers
- MQ has both queue feature ~SQS and topic feature ~SNS

Amazon MQ - HIGH AVAILABILITY

Container Section

Docker

- software dev platform to deploy apps
- Apps are packaged in containers and can be run on any OS
- Apps run the same regardless of where they’re run
    - Any machine
    - No compatibility issues
    - Predictable behavior
    - Less work
    - Easier to maintain and deploy
    - Works with any language, any OS, any technology
- use cases, microservices architecture, lift and ship apps from on-premises to AWS cloud

Docker on OS

- Docker images are stored on Docker Repository
- Docker Hub
    - Public repository
    - Find base images for many technologies or OS (Ubuntu MySQL)
- Amazon ECR (Amazon Elastic Container Registry)
    - Private repository
    - Public repository

Docker VS Virtual Machine

- Docker is sort of Virtualization but not exactly
- Resources are shared with host on virtualization = may containers runs on one server
    
  
Docker containers Management on AWS

- Amazon Elastic Container Service (Amazon ECS)
    - Amazon’s own container platform
- Amazon Elastic Kubernetes Services (Amazon EKS)
    - Amazon’s managed Kubernetes (Open Source)
- AWS Fargate
    - Amazon’s own serverless container platform
    - Works with ECS and with EKS
- Amazon ECR
    - Store container images

Amazon ECS - EC2 Launch Type

- ECS - Elastic Container Services
- Launch Docker containers on AWS = Launch ECS Tasks on ECS Cluster
- EC2 launch Type: you must provision & maintain the infrastructure
- Each EC2 instance must run the ECS Agent to register in the ECS Cluster
- AWS takes care of starting / stopping containers
  

Amazon ECS - Fargate Launch Type

- Launch Docker containers on AWS
- You do not provision the infrastructure(no Ec2 instances to manage)
- Its all serverless
- You just create tasks definitions
- AWS just runs ECS Tasks for you based on CPU / RAM you need
- To Scale, just increase the number of tasks. Simple - no more EC2 instances
   

Amazon ECS - IAM roles for ECS

- EC2 instance profile (Ec2 launch type only)
    - Used by ECS agent
    - Makes API calls to ECS service
    - Send container logs to CloudWatch Logs
    - Pull Docker image from ECR
    - Reference Sensitive Data in Secrets Manager or SSM Parameter store
- ECS Task Role
    - Allow each task to have a specific role
    - Use different roles for the different ECS services you run
    - Task Role is defined in the task definition
 
- Application Load Balancer supported and works for most use cases
- Network load balancer recommended only for high througput / high performance use cases, or to pair with AWS private link
- Classic Load balancer supported but not recommended (no advance features - no fargate)

Amazon ECS - Data Volumes (EFS)

- Mount EFS file systems on to ECS tasks
- Works for both EC2 and Fargate lauch types
- Tasks running in any AZ will share the same data in the EFS File system
- Fargate + EFS = Serverless
- Use cases : Persistent multi - AZ shared storage for your container
- Amazon s3 cannot be mounted as a file system
  

ECS Service Auto Scaling

- Automatically increase / Decrease the desired number of ECS tasks
- Amazon ECS Auto Scaling uses AWS Application Auto Scaling
    - ECS Service Average CPU utilization
    - ECS Service Average memory utilization - Scale on RAM
    - ALB Request Count per Target - metric coming from ALB
- Target Tracking - scale based on target value for a specific CloudWatch metric
- Step Scaling -scale based on a specified CloudWatch Alarm
- Scheduled Scaling - scale based on a specified data/time (predictable chages)
- ECS Service Auto Scaling (task level) is not equal to EC2 Auto Scaling
- Fargate Auto scaling is much easier to setup (Because serverless)

EC2 Launch Type - Auto Scaling EC2 Instance

- Accommodate ECS Service Scaling by adding underlying EC2 Instances
- Auto Scaling Group Scaling
    - Scale your ASG Based on CPU utilization
    - Add EC2 instance over time
- ECS Cluster Capacity Provider
    - Used to automatically provision and scale the infrastructure for your ECS Tasks
    - Capacity Provider paired with an Auto scaling group
    - Add Ec2 instances when you’re missing capacity(CPU,RAM)
     
ECS tasks invoked by Event Bridge

ECS tasks invoke by Event Bridge Schedule

ECS - Intercept Stopped Tasks using EventBridge

Amazon ECR

- ECR - Elastic Container Registry
- Store and manage Docker images on AWS
- Private and Public repository (Amazon ECR Public Gallery)
- Fully integrated with ECS, backed by Amazon S3
- Access is controlled through IAM (permission errors⇒ policy)
- Supports image vulnerability scanning, versioning, image tags, image lifecycle,…
  
Amazon EKS Overview

- Amazon EKS =  Amazon Elastic Kubernetes Services
- It is a way to launch managed Kubernetes cluster on AWS
- Kubernetes is an opensource system for automatic deployment, scaling and management of containerized (usually Docker) application
- It’s an alternative to ECS, similar goal but different API
- EKS supports EC2 if you want to deploy worker nodes or Fargate to deploy serverless containers
- Use case: if your company is already using Kubernetes on-premises or in another Cloud, and wants to migrate to AWS using Kubernetes
- Kubernetes is cloud-agnostic (can be used in any cloud - Azure, GCP…)
  

Amazon EKS - Node types

- Managed node Groups
    - Creates and manages Nodes (EC2 instances) for you
    - Nodes are part of an ASF managed by EKS
    - Supports on-Demand or spot Instances
- Self Managed Nodes
    - Nodes created by you and registered to the EKS cluster and Managed by an ASG
    - You can use prebuilt AMI - Amazon EKS optimized AMI
    - Supports on-Demand or Spot instance
- AWS Fargate
    - No maintenance required; no nodes managed

Amazon EKS - Data Volumes

- Need to specify Storage Class manifest on your EKS Cluster
- Leverages a Container Storage Interface (CSI) Compliant Driver
- Support for
    - Amazon EBS
    - Amazon EFS (works with fargate)
    - Amazon FSx for Lustre
    - Amazon FSx for NetApp ONTAP

AWS App Runner

- Fully managed services that makes it easy to deploy web applications and APIs at scale
- No infrastructure experience required
- Start with your source code or container image
- Automatically builds and deploy the web app
- Automatically scaling, highly available, load balancer, encryption
- VPC access Support
- Connect to database, cache, and message queue services
  
Serverless in AWS

- AWS Lambda
- DynamoDB
- AWS Cognito
- AWS API Gateway
- Amazon S3
- AWS SNS & SQS
- AWS Kinesis Data Firehose
- Aurora serverless
- Step Functions
- Fargate

Why AWS Lambda

EC2

- Virtual servers in the cloud
- Limited by RAM AND CPU
- Continuously running
- Scaling means intervention to add / remove servers

Lambda

- Virtual functions - no servers to manage
- Limited by time - short executions
- Run on demand
- Scaling is automated

Benefits of AWS Lambda

- Easy pricing
- Pay per req
- Free tier of 1000000 AWS Lambda requests 400000 GB’s of compute time
- Integrated with the whole AWS Suite of services
- Integrated with many programming languages
- Easy monitoring through CloudWatch
- Easy to get more resources per function (up to 10 GB of RAM)
- Increasing RAM will also improve CPU and network

AWS Lambda language support

- Node.js
- Python
- Java
- C#
- Golang
- C# / Powershell
- Ruby
- Custom Runtime API

- Lambda Container Image
    - Container image must implement the lambda runtime api
    - ECS / Fargate is preferred for running arbitrary Docker image

AWS Lambda Limits to know - pre region

- Execution
    - Memory allocation 128 MB to 10 GB
    - Maximum execution time: 900 Sec
    - Environment Variables 4KB
    - Disk capacity in the function container in /tmp 512MB to 10 GB
    - Concurrency executions: 1000 (can be increased)
- Deployment
    - Lambda function deployment size (compressed .zip): 50 MB
    - Size of uncompressed deployment (code+dependencies): 250 MB
    - Can use the /tmp directory to load other files at startup
    - Size of environment variables 4 KB

Customization At the Edge

- Many modern application execute some form of the logic at the edge
- Edge Function:
    - A code that you write and attach to cloudfront distributions
    - Runs close to you users to minimize late
- CloudFront provides 2 types”: CloudFront Functions and Lambda@Edge
- You don’t have to manage any servers, deploy globally
- Use case: customize the CDN content
- Pay only for what you use

**Use cases**

- Website security and privacy
- Dynamic web applications at the edge
- SEO
- Intelligently Route Across Origins and Data Centers
- Bot Mitigation at the Edge
- Real-time Image Transformation
- A/B Testing
- User Authentication and Authorization
- User Prioritization
- User Tracking and Analytics

**CloudFront Functions**

- Light weight functions written in JavaScript
- high-scale, latency-sensitive CDN customizations
- Sub-ms start up times, millions of requests /seconds
- Used to change Viewer request and responses
    - Viewer Request: after CloudFront receives a request from a viewer
    - Viewer Response: Before CloudFront forwards the response to the viewer
- Native feature of CloudFront (manage code entirely within CloudFront)
- CloudFront provides 2 types”: CloudFront Functions and Lambda@Edge
    
  
**Choosing Right DataBase**

**Databases Type**

- RDBMS
- NoSQL database
- Object Store
- Data Warehouse
- Search
- Graphs
- Ledger
- Time Series

**RDS**

Amazon RDS

- Managed Postgres SQL/MySQL/Oracle/SQL Server/Maria DB/Custom
- Provisioned RDS Instance Size and EBS Volume Type & Size
- Auto Scaling Capability for Storage
- Support for Read Replicas and Multi AZ
- Security through IAM, Security Groups, KMS, SSL in transit
- Automated backup with point in time restore feature (upto 35 days)
- Manual DB Snapshot for longer term recovery
- Managed and scheduled maintenance (with downtime)
- Support for IAM Authentication, integration and Secrets Manager
- RDS Custom for access to and customize the underlying instance (Oracle and SQL Server)

Use Case - Store relational datasets (RDBMS/OLTP), perform SQL queries, transaction

Athena

- Serverless query service to analyze data stored in S3
- uses Standard SQL Lanaguage
- Supports CSV, JSON, ORC, Avro, and Paquet
- $5 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards
- Use Cases - BI/ analytics/ reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrails Etc..
- Analyze data in S3 using serverless sql, use athena
    
  

Athena performance improvement

- columnar data for cost saving (less scaning)
    - Apache Parquet or ORC is recommended
    - Huge performance improvement
    - Use glue to convert your data to Parquet or ORC
- Compress data for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- Partition datasets in S3 for easy querying on virtual columns
    - S3://yourBucket/pathToTable
        - /<PartitionColumnName≥<Value>
    - Example: s3://athena-example/flight/parquet/year=1991/month=1/day=1/
- Use larger file (>128 MB) to minimize overhead

Athena - Federated Query

- Allows you to run SQL queries across data stored in relational, non-relational. object, and custom data source
- use Data source connectors that run on AWS Lambda to run Federated Queries (eg CloudWatch Logs, dynamodb, rds)
- Store result back in S3
    
 

Redshift

- Based on PostgreSQL but its not used for OLTP
- its OLAP - Online Analytical Processing (Analytics and data warehousing)
- 10X better performace than other data warehouse, scale to PBs of data
- Columnar storage of data (instead  of row based) & parallel query engine
- Pay as you go based on the instances provisioned
- Has a SQL interface for performing the queries
- BI tools such as Amazon QuickSight or Tableau integrate with it
- Vs Athena: faster queries / joins / aggressions thanks to indexes

Redshift cluster

- leader node - for query planning , results aggregation
- Compute node - for performing the queries, send result to leader
- You provision the node size in advance
- You can used reserved instances for cost saving
    
 
Redshift - Snap shots and DR

- Has Multi-Az mode for some Cluster
- Snapshot are point in time backups of a clusters, stored internally in S3
- Snapshots are incremental (only what changed is saved)
- you can restore a snapshot into a new cluster
- Automated: every 8 hours, every 5 gb, or on a schedule. Set retention.
- Manual: Snapshot is retained until you delete it
- You can configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region.
    

Redshift Spectrum

- Query Data that is already in S3 without loading it
- Must have a redshift cluster available to start the query
- The query is then submitted to thousands of redshift spectrum node
    
   

Amazon Opensearch Service

- is successor to ElasticSearch
- IN dynamodb, queries can only exist by primary key or indexes..
- With opensearch you can search any field, even partially matched ones
- its common to use opensearch as complement to other database
- two modes - managed cluster or serverless cluster
- does not natively support SQL can use plugin to enable it
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Congnito and IAM, KMS encryption, TLS
- Comes with opensearch dashboards (visualizations)
    
   

Amazon EMR - Elastic Map Reduce

- Helps creating Hadoop Clusters (Big Data) to analyze and process vast amount of data
- The cluster can be made of hundreds of EC2 instances
- EMR comes with Apache Spark, HBase, Presto, Flink,,,
- EMR takes care of all the provisioning and configuration
- Auto-scaling and integrated with Spot instance
- Use cases - Data processing, machine learning, web indexing, big data…

Node  type and Purchasing

- Master node - Manage the cluster, coordinate, mange health, long running
- Core node - run tasks and store data - long running
- Task Node (optional) - just to run tasks - usually spot
- Purchasing options:
    - On demand - reliable, predictable, wont be terminated
    - Reserved - cost savings
    - Spot instance - cheaper, can be terminated, less reliable
- Can have long running cluster or transient (temporary) cluster

Quick Sight

- Server less machine power BI service creates interactive dashboards
- Fast automatically scalable
- Embedded, per-session pricing
- Use cases -
    - Business analytics
    - Building Visualizations
    - Perform Ad-Hoc analysis
    - Get business insights using data
- INtegrated with RDS, Auraora, Athena, Redshift, S3
- In Memory computation using SPICE engine if data is imported into QuickSight
- Enterprise edition - Possibility to Setup Column - Level Security (CLS)
    
   

Dashboards and Analysis

- Define users (Standard Versions) and Groups (Enterprise versions)
    - within Quicksight not IAM
- Is a read only snapshot of an analysis that you can share
- Preserves the configuration of the analysis (flitering, parameters, controls, sort)
- You can share the analysis or the dashboard with users or groups
- To share a dashboard, you must first publish it
- Uses who see the dashboard can also see the underlying data

GLUE

- Managed extract, transform, and Load ETL service
- Useful to prepare and transform data for analytics
- Fully serverless service
  

Glue - things to know at a high level

- Glue Job Bookmarks - prevent re-processing old data
- Glue Elastic views
    - Combine and replicate data across multiple data stores using SQL
    - No custom code, Glue Monitors for change in the source data, serverless
    - Leverages a “Virtual Table” (Materialized View)
- Glue DataBrew - Clean and normalize data using pre-built transformation
- Glue Studio -  new GUI to create, run and monitor ETL jobs in GLUE
- Glue Streaming ETL (built on Apache Spark Structured Streaming) compatible with Kinesis Data Streaming, Kafka, MSK (Managed Kafka)

Lake Formation

- Data lake - central place to have all your data for analytics purposes
- Fully managed services that makes it easy to setup a data lake in days
- Discover, cleanse, transform, and ingest data into your Data Lake
- It automates many complex manual steps (Collecting, cleansing, moving, cataloging data) and de-duplicating (using ML transforms)
- Combine structured and unstructured data in the data lake
- Out of the box source blueprints - S3 RDS Relational NoSQL DB
- Fine Grained access control for your applications (row and column level)
- Built on top of AWS Glue
    
   
Kinesis Data Analytics

For SQL

- Real time analytics on Kinesis Data Streams & Firehose Using SQL
- Add reference data form S3 to enrich streaming data
- Fully managed, no servers to provision
- Automatic scaling
- Pay for actual consumption rate
- Output
    - Kinesis data streams - Create streams out of real time analytics queries
    - Kinesis data Firehose - send analytics query results to destinations
- Use Cases
    - Time-series analytics
    - Real-time dashboards
    - Real-time metrics

For Apache Flink

- Use flink (Java, scala or sql) to process and analyze streaming data
- Run any Apache Flink application on a managed cluster on AWS
    - Provisioning compute resources, parallel computation, automatic scaling
    - Application backups (implemented as checkpoints and snapshots)
    - Use Apache Flink programming features
    - Flink doesn't read from Firehose

Amazon Managed Streaming for Apache Kafka (Amazon MSK)

- Alternative to Amazon Kinesis
- Fully managed Apache Kafka on AWS
- Allow you to create,update delete clusters
- MSK creates and manages kafka brokers nodes and zookeeper nodes for you
- Deploy the MSK cluster in your VPC, multi-AZ (upto 3 HA)
- Automatic recovery from common Apache Kafka failures
- Data is stored on EBS Volumes for as long as you want
- MSK serveless
- Run Apache Kafka on MSK without managing the capacity
- MSK automatically provisions resources and scales compute and storage
    
 
Big Data Ingestion Pipeline

- To want to ingest pipeline to be fully serverless
- To want to collect data in real time
- To want to transform the data
- To want to query the transformed data using SQL
- The reports created using the queries should be in S3
- To want to load that data into a warehouse and create dashboards
    

- IoT Core allows you to harvest data form IoT devices
- Kinesis is great for real time data collection
- Firehose helps with data delivery to S3 in real time
- Lambda can help Firehose with data transformation
- Amazon S3 can trigger notifications to SQS
- Lambda can subscribe to SQS (we could have connector S3 to Lambda)
- Athena is a serverless SQL service and results are stored in S3
- The reporting bucket contains analyzed data and can be used by reporting tool such as AWS Quicksight, RedShift, etc…

Amazon Rekognition

- Find objects, people, text, scenes in images and videos using ML
- Facial analysis and facial search to do user verification, people counting
- Create a database of Familiar faces or compare against celebrities
- Use cases
    - Labeling
    - Content Moderation
    - Text detection
    - Face detection and Analysis
    - Face search and verification
    - Celebrity recognition
    - Pathing

Content Moderation

- Detect content that is inappropriate unwanted or offensive
- Used in social media, broadcast media, advertising, and ecommerce situations to create a safer user experience
- Set a minimum confidence threshold for items that will be flagged
- Flag sensitive content for manual review in Amazon Augmented AI (A2I)
- Help comply with regulations

Amazon Transcribe

- Deep learning process called ASR (Automatic Speech recognition)
- Removes PII using Redaction
- Automatic language identification

Polly

- Turns text to lifelike speech
- Uses Lexicon and SSML to customize pronunciation
- SSML - Speech Synthesis Markup language - for making it sound different

Amazon Translate

- Language translation
- Allows you to localize content for international users and translate large volume of text efficiently

Amazon Lex & Connect

- Same tech in Alexa
- Automatic Speech recognition
- used to build chat bots

Connect 

- Receive calls, create contact flows, cloud-based virtual contact center
- Can integrate with CRM systems or AWS
- No upfront payments 80% cheaper than traditional contact center solutions

Amazon comprehend

- For natural Language processing _ NLP
- Fully managed and serverless services
- Use machine learning to find insights and relationships in text

Amazon comprehend Medical

- Unstructured Clinical text
- Physician’s notes
- Discharge summary
- Test results
- Case notes
- NLP to detect protected Health Information
- Stored in S3, real time data with Kinesis Data Firehose or use Amazon Firehose

Amazon Sage Maker

- Fully managed services for developers /data scientists to build ML models
- Typically difficult to do all processes in one places + provision servers
- Machine Learning process (Simplified): predicting your exam score

Amazon Forecast

- Highly accurate forecast using ML

Amazon Kendra

- Fully managed document search service powered by ML

Amazon Personalize

- Fully managed ML service to build apps with real time personalized recommendations

Amazon Textract

- Automatically extracts text, handwriting and data from any scanned documents using AI and ML

Monitoring

Amazon CloudWatch Metrics

- CloudWatch provides metrics for every services in AWS
- Metric is variable to monitor (CPU utilization, NetworkIN,..)
- Metrics belong to namespace
- Dimension is an attribute of a metric (instance ID, environment etc)
- Up to 30 dimensions per metric
- Metrics have timestamps
- Can create CloudWatch Dashboards of metrics
- Can create CloudWatch Custom Metrics RAM

CloudWatch Logs

- stores application logs
- log group
- log streams
- can define expiration policy
- send logs to s3 or so
- encrypted

Source 

- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
- Elastic beanstalk - collection of logs from application
- ecs- collection from containers
- aws lambda - collection of function logs
- vpc flow logs - vpc specific logs
- api gateway
- cloudtrail based on filter
- route53 - logs dns queries

CloudWatch Logs Insights

- can view all the logs
- search and analyze logs stored
- Provided purpose built queries
- can query multiple logs

CloudWatch logs for EC2

- By default, no logs from your EC2 machine will go to CloudWatch
- You need to run a CloudWatch Agent on EC2 to push the log files
- Needs IAM permissions
- CloudWatch log agent can be set up on premises too

2 Different Agent

- Log Agent and Unified Agent
- Log Agent
    - old version
    - can only send to cloudwatch logs
- Unified Agent
    - collects additional system level metrices such as RAM, processes, etc..
    - Collects logs to send to CloudWatch Logs
    - Centralized configurations using SSM Parameter Store

Cloud Watch Alarms

- triggers notification
- various options(sampling, %, max, min, etc..)
- Alarm States
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: 10 sec, 30 sec or multiple of 60 sec

Amazon EventBridge

- schedule cron jobs
- Event Pattern
- Trigger Lambda functions - send SNS/SQS messages

CloudWatch Lambda insights

- Monitoring and troubleshooting solutions for solutions for serverless applications running on AWS lambda
- Collects, aggregates, and summarizes system-level metrices including CPU time, memory, disk and network
- Collects aggregates, and summarizes diagnostic information such as cold starts and Lambda worker shutdowns
- Lambda insights is provided as a lambda layer

AWS CloudTrail

- Provides governance, compliance and audit for your AWS account
- CloudTrail is enabled by default
- Get an history of events / API calls made within your AWS account by
    - console
    - SDK
    - CLI
    - AWS services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- A trail can be applied to All Regions (Default) or a single Region
- If a resource is deleted in AWS, investigate CloudTrail first!
    
 

Cloud trail Events

- Management Events
    - operations that are preformed in your aws account
    - Configuring security (IAM attachrolepolicy)
    - Configuring rules for routing data (Amazon EC2 CreateSubnet)
    - Setting up logging (AWS CloudTrail CreateTrail)
    - By Default, trails are configured to log management events
    - Can separate Read Events from Write Events
- Data Events
    - By default, data events are not logged
    - Amazon S3 Object level activity (GetObject, PutObject, DeleteObject) can separate Read and Write Events
    - AWS Lambda functions executions activity (the Invoke API)

CloudTrail Insights Events

- enable cloudtrail insights to detect unusual activity in your account
    - inaccurate resource provisioning
    - hitting service limits
    - Bursts of AWS IAM actions
    - Gaps in periodic maintenance activity
- CloudTrail Insights analyzes normal management events to create a baseline
- And then continuously analyzes write events to detect unusual patterns
    - Anomalies appear in the CloudTrail Console
    - Event is sent to Amazon S3
    - An EventBridge event is generated (for automation needs)
     
CloudTrail event retentions

- 90 days in cloudtrail
- Log them to s3 and use athena if want to be kept more than 90 days
  

Amazon Event Bridge - Intercept API Calls

Amazon EventBridge + CloudTrail

AWS config

- helps with auditing and recording compliance of your aws resources
- helps record configurations and changes over time
- Questions that can be solved by AWS Config:
    - Is there unresticted SSH access to my security groups?
    - Do my bucket have any public access?
    - How has my ALB configurations changed over time?
- You can receiver alerts SNS notifications for any changes
- AWS config is a per region service
- Can be aggregated across regions and accounts
- Possibilities of storing the configuration data into S3 (Analyzed by Athena)

CloudWatch vs CloudTrail vs Config

CloudWatch

- Performance monitoring
- events and alerting
- Log aggregation and analysis

CloudTrail

- Record API calls made within your account by everyone
- Can define trails for specific resources
- Global service

Config

- Record configuration changes
- Evaluate resources against compliance rules
- Get timeline of changes and compliance

AWS Organizations

- Global services
- Allows to manage multiple AWS accounts
- The main account is the management account
- Other accounts are member account
- Member accounts can only be part of one organizations
- Consolidated billing across all accounts - single payment method
- Pricing benefits from aggregated usage (volume discount for EC2, S3…)
- Share reserved instances and Savings Plans discount across accounts
- API available to automate AWS account creation
  

Advantages

- Multi Account VS One Account Multi VPC
- use tagging standards for billing purposes
- enable cloud trail on all accounts, send logs to central S3 account
- Send cloudwatch logs to central logging account
- establish cross account roles for admin purpose

Security - Service Control Policies (SCP)

- IAM policies applied to ou or accounts to restrict users and Roles
- They do not apply to the management account (full admin power)
- Must have an explicit allow (does not allow anything by default - like IAM)
  
AWS Control Tower:

- Easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices
- AWS Control Tower uses AWS Organizations to create accounts
- Benefits:
    - Automate the set up of your environment in a few clicks
    - Automate ongoing policy management using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through interactive dashboard

Encryption in Flight SSL

- Data is encrypted before sending and decrypted after receiving
- SSL certificates help with encryption (HTTPS)
- Encryption in flight ensures no MITM can happen

Server side encryption at rest

Client side encryption 

- Data is encrypted by the client and never decrypted by the server
- Data will be decrypted by a receiving client
- The server should not be able to decrypt the data
- Could leverage Envelope Encryption
   

KMS Overview

- Key Management Service
- Fully integrated with IAM
- Able to audit KMS key usage using CloudTrail
- Integrates to most AWS service
- Never store your secrets in plaintext, especially in your code!
    - KMS Key Encryption also available through API calls (SDK, CLI)
    - Encrypted secrets can be stored in the code / environment variable

KMS Keys types

- KMS Keys is the new name of KMS customer Master Key
- Symmetric (AES-256 keys)
    - Single encryption
    - aws services integrated with kms uses symmetric keys
    - You never get access to KMS Key unencrypted (must call KMS API to use)
- Asymmetric (RSA & ECC Key pairs)
    - Public(Encrypt) and Private(Decrypt) pair
    - Used for Encrypt/Decrypt or sign/verify operations
    - the public key is downloadable
    - use case: encryption outside of AWS by users who can’t call the kms API

AMI Sharing Process encrypted via KMS

- AMI in source account is encrypted with KMS Key from Source Account
- Must modify the image attribute to add a Launch Permission which corresponds to the specified target AWS account
- Must Share the KMS Keys sed t encrypted the snapshot the AMI references with the target account / IAM Role
- The IAM Role/user

SSM Parameter store

- Secure storage for configuration and secrets

VPC - Virtual Private Cloud

CIDR - IPv4

- Classless Inter Domain Routing - a method for allocating IP address
- Used in Security Groups rules and AWS networking in general
- They help define IP address range
    - 192.168.1.2/32 = one IP address
    - 0.0.0.0/0 = all IP address
    - 192.168.1.0/26 - 64 IP address in which 62 is usable

Understanding CIDR - IPv4

- 2 components
    - Base IP
    - Subnet mask

Subnet Mask

- The subnet mask basically allows the part of underlying IP address to get additional next values from base IP

Public vs Private IP (IPv4)

- IANA(Internet Assigned Numbers Authority) established certain blocks of IPv4 addresses for the use of private LAN and public internet IP address
- Private IP can only allow certain values
    - 10.0.0.0 - 10.255.255.255 /8 - Big networks
    - 172.16.0.0 - 172.31.255.255 /12 - AWS default VPC range
    - 192.168.0.0 - 192.168.255.255 /16 - home networks
- All the rest of the IP address on the Internet are Public

Default VPC Overview

- All new AWS accounts have default VPC
- New EC2 instances are launched into the default VPC if no subnet is specified
- Default VPC has Internet connectivity and all EC2 instances inside it will have public IPv4 addresses
- We also get a public and private IPv4 DNS names

VPC in AWS = IPv4

- VPC = Virtual Private Cloud
- You can have multiple VPC’s in an AWS region (max. 5 per region - soft limit - can be increased)
- Max CIDR per VPC is 5, for each CIDR
    - Min size is /28 (16 IP addresses)
    - Max size is /16 (65536 IP addresses)
- Because VPC is private, only the Private IPv4 ranges are allowed
    - 10.0.0.0 - 10.255.255.255
    - 172.16.0.0 - 172.31.255.255
    - 192.168.0.0 - 192.168.255.255
- Your VPC CIDR should NOT overlap with other networks (eg., corporate)
    

Internet Gateway (IGW)

- Allows resources in a VPC to connect to the internet
- It scales horizontally and is highly available and redundant
- Must be created separately from VPC
- One VPC can only be attached to IGW and vice versa
- Internet Gateway on their own do not allow internet access
- Route tables must also be edited

Bastion Hosts

- We can use a Bastion Host to SSH into our private EC2 instances
- The Bastion is in the public subnet which is then connected to all other private subnets
- Bastion Host security group must allow
- Security Group of the EC2 Instance must allow the Security Group of the Bastion Host, or the private IP of the Bastion host

NAT Instance (Outdated, but still at exam)

- NAT = Network Address Translation
- Allows EC2 instances in private subnets to connect to the internet
- Must be launched in a public subnet
- Must disable EC2 setting: Source/Destination Check
- Must have Elastic IP attached to it
- Route Tables must be configured to route traffic from private subnets to the NAT instance
    

NAT Instance Comments

- Pre-configured Amazon Linux AMI is available
    - Reached the end of standard support on December 31, 2020
- Not highly available / resilient setup out of the box
    - You need to create an ASG in multi-AZ + resilient user-data script
- Internet traffic bandwidth depends on EC2 instance type
- You must manage Security Groups & rules:
    - Inbound
        - Allow HTTP / HTTPS traffic coming from private subnets
        - Allow SSH from your home network (access is provided through Internet Gateway)
    - Outbound
        - Allow HTTP / HTTPS traffic to the Internet

NAT Gateway

- AWS-managed NAT, higher bandwidth, high availability, no administration
- Pay per hour for usage and bandwidth
- NATGW is created in a specific Availability Zone, uses an Elastic IP
- Can’t be used by EC2 instance in the same subnet (only from other subnets)
- Requires and IGW (Private Subnet ⇒ NATGW ⇒ IGW)
- 5Gbps of bandwidth with automatic scaling up to 100 Gbps
- No Security Groups to manage / required
    

NAT Gateway with High Availability

- NAT Gateway is resilient within a single availability zone
- Must create multiple NAT Gateways in multiple AZs for fault tolerance
- There is no cross AZ failover needed because if an AZ goes down it doesn’t need NAT
    

**Security groups & NACL**

**Network Access Control List NACL**

- NACL are like firewall which controls traffic from and to subnets
- Once NACL per subnet,  new subnets are assigned the Default NACL
- You define NACL Rules:
    - Rules have a number (1-32766), higher precedence with lower number
    - First rule match will drive the decision
    - Example: if you define #100 ALLOW 10.0.0.10/32 and #200 DENY 10.0.0.10/32, the IP address will be allowed because 100 has a higher precedence over 200
    - The last rule is an asterisk * and denies a request in case of no rule match
    - AWS recommends adding rules by increment of 100
- Newly created NACL’s will deny everything
- NACL are a great for blocking specific IP at subnet level
    

Default NACL:

- Allows everything inbound/outbound with the subnets its’ associated with
- Do NOT modify the Default NACL, instead create a custom one
    

Ephemeral Ports

- For any two endpoints to establish a connection, they must use ports
- Clients connect to a defined port, and expect a response on an ephemeral port
- Different operating systems use different port ranges, example
    - IANA & MS Windows - 49152 - 65535
    - Many Linux Kernels - 32768 - 60999

NACL with Ephemeral Ports

Security Group VS NACL

VPC Peering:

- Privately connect two VPC’s using AWS network
- Make them behave as if they were in the same network
- Must not have overlapping CIDRs
- VPC peering connection is NOT transitive (must be established for each VPC that need to communicate with one another)
- You must update route tables in each VPC’s subnets to Ensure EC2 instances can communicate with each other
    

VPC Peering - Good to Know

- Can peer between VPC is different accounts/region
- You can reference a security group in a peered VPC (work cross accounts - same region)

VPC Section Summary

- CIDR - IP Range
- VPC - Virtual Private Cloud - we define a list of IPv4 and IPv6 CIDR
- Subnets - tied to an AZ, we define a CIDR
- Internet Gateway - at the VPC level, provide IPv4 & IPv6 Internet Access
- Route Tables - must be edited to add routes from subnets to the IGW, VPC peering connections, VPC endpoints.
- Bastion Hosts - Public EC2 instance to SSH into, that has SSH connectivity to EC2 instances in private subnets
- NAT Instances - gives Internet access to EC2 instances in private subnets. Old, must be setup in a public subnet, disable source and destination check flag
- NAT Gateway - managed by AWS, provides scalable Internet access to private EC2 instances, IPv4 only
- NACL - Stateless, subnet rules for inbound and outbound, don’t forget Ephemeral ports
- Security Groups - stateful, operate at the EC2 instance level
- VPC Peering - connect two VPC with non overlapping CIDR, non-transitive
- VPC Endpoints - provide private access to AWS services (S3, DynamoDB, cloudformation, SSM) within a VPC
- VPC Flow Logs - can be set up at the VPC/ Subnet / ENI Level, for ACCEPT and REJECT traffic, helps identifying attacks, analyze using Athena or CloudWatch Logs Insights
- Site to Site VPN - setup a Customer Gateway on DC, a Virtual Private Gateway on VPC, and site to site VPN over public internet
- AWS VPN CloudHub - hub and spoke VPN model to connect your sites
- Direct connect - set up a virtual private gateway on VPC, and establish a direct private connection to an AWS Direct Connect Location
- Direct Connect Gateway - setup a Direct Connect to many VPCs in different AWS regions
- AWS PrivateLink / VPC endpoint services
    - Connect services privately from your service VPC to customers VPC
    - Doesn’t need VPC peering, public internet, NAT Gateway, Route Tables
    - Must be used with Network Load Balancer and ENI
- ClassicLink - Connect EC2 - Classic EC2 instance privately to your VPC
- Transit Gateway - transitive peering connections for VPC, VPN and DX
- Traffic mirroring - copy network traffic from ENIs for further analysis
- Egress-only Internet Gateway - like a NAT Gateway, but for IPv6

Network Protection on AWS

- To protect network on AWS
    - Network Access Control List (NACL)
    - Amazon VPC Security Groups
    - AWS WAF (protection against malicious requests)
    - AWS Shield and AWS Shield Advanced
    - AWS Firewall Manager (to manage them across accounts)

AWS Network Firewall

- Protect your entire Amazon VPC
- From layer 3 to 7
- Any direction, you can inspect
    - VPC to VPC traffic
    - Outbound to internet
    - Inbound from internet
    - To/from Direct Connect & Site-to-Site VPN
- Internally, the AWS Network Firewall uses the AWS Gateway Load balancer
- Rules can be centrally managed cross-accounts

Disaster Recovery Overiew

- Any event that has negaive impact on company’s business continuity or finances is a disaster
- Disaster Recovery DR is about preparing for and recovering from a disaster
- What kind of disaster recovery
    - On - premise - On-premise - Traditional DR, expensive
    - On-Premise - AWS Cloud - Hybrid recovery
    - AWS Cloud Region A = AWS Cloud Region B
- Need to define 2 terms:
    - RPO - Recovery Point Objective - From the time of disaster to the previous back up we have
    - RTO - Recovery Time Objective - time taken to restore the services form the point of disaster
        

Disaster Recovery Strategies:

- Back up and Restore
- Pilot light
- Warm Standby
- Hot site / Multi site approach
    

Backup and restore (High RTO)

Pilot Light

- A small version of app is always running in the cloud
- Useful for the critical core (pilot light)
- Very similar to Backup and Restore
- Faster than backup and restore as critical systems are already up

Warm Standby

- Full system is up and running at minimum size
- Upon disaster, we can scale to production load
    

Multi Site / Hot Site Approach

- Very low RTO (minutes or seconds) - expensive
- Full Production Scale is running AWS and On Premise
    

Disaster Recovery Tips

Backups 

- EBS Snapshots, RDS automated backups, snapshots, etc..
- Regular push to S3 / s3 IA / Glacier, Lifecycle Policy, Cross Region Replication
- From On-Premise - Snowball or Storage gateway

High Availability

- use route 53 to migrate DNS over from Region to Region
- RDS Multi-AZ, Elasticache Multi-AZ, EFS, S3
- Site to Site VPN as a recovery from Direct Connect

Replication

- RDS Replication (Cross Region), AWS Aurora + Global Database
- Database replication from on-premise to RDS
- Storage Gateway

Automation

- CloudFormation / Elastic Beanstalk to re-create a whole new environment
- Recover /Reboot EC2 instances with CloudWatch if alarm fail
- AWS Lambda functions for customized automations

Chaos

- Netflix has a “simiam-army” randomly terminating EC2

DMS - Database Migration Services

- Quickly and securely migrate databases to AWS, resilient, self healing
- The Source database remains available during migration
- Supports:
    - Homogeneous migrations - form oracle to oracle
    - Heterogeneous migrations - from oracle to Aurora
- Continuous Data Replication using CDC
- You must create an EC2 instance to perform the replication tasks

DMS - Sources and Targets

AWS Schema Conversion Tool (SCT)

- Convert your Database Schema from one engine to another
- Example OLTP - SQL or Oracle to MySQL, PostgreSQL, Aurora
- Example OLAP - Teradata or Oracle to Amazon Redshift
- You don’t need to use SCT if you are migrating the same DB engine
    

DMS continuous Replication

RDS & Aurora MySQL Migrations

On-Premise Strategy with AWS

- Ability to download Amazon Linux 2 AMI as a VM .iso format
    - VMWare, KVM, VirtualBox (Oracle VM), Microsoft Hyper-v
- VM Import / Export
    - Migrate existing applications into EC2
    - Create a DR repository strategy for your on-premise VMs
    - Can export back the VMs from EC2 to on-premise
- AWS Application Discovery Service
    - Gather information about your on-premise VMs
    - Server utilization and dependency mapping
    - Track with AWS migration hub
- AWS Database Migration Service (DMS)
    - replicate On-Premise = AWS, AWS = AWS, AWS = On-Premise
    - Works with various database technologies (Oracle, MySQL, DynamoDB, etc..)
- AWS Migration Service (SMS)
    - Incremental replication of on premise live servers to AWS

Transferring large amount of data into AWS

- Example: transfer 200 TB of data in the cloud. We have a 100 Mbps internet connection

Over the internet = 185 days

- 1 Gbps = 18.5 days

Over Snowball 

- 2 to 3 snowballs in parallel
- Takes about one week to end transfer
- Can be combined with DMS

VMware Cloud on AWS

- Some customers use VMWare Cloud to manage their on-premise Data Center
- They want to extend the Data Center capacity to AWS, but keep using the VMWare Cloud software
- Enter VMware cloud on AWS
- Use Cases
    - Migrate your VMware vSphere based workloads to AWS
    - Run your production workloads across Vmware vSphere based private, public and hybrid cloud environments
    - Have a disaster recovery strategy
        

What is CloudFormation

- Cloud Formation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported)
- For example, within a CloudFormation template, you say:
    - I want Security Group
    - I want two EC2 instances using this security Group
    - I want an S3 bucket
    - I want an load balancer (ELB) in front of these machines
- Then CloudFormation creates those for you, in the right order, with the exact configuration that you specify

Benefits 

- Infrastructure as a code
- Cost
- Productivity
- Don’t re-invent the wheel
- Supports almost all AWS resources

CloudFormation Stack Designer

Systems Manager - Run command

- Execute a document (= script) or just run a command
- Run command across multiple instances (using resources groups)
- No need for SSH
- Command output can be shown in AWS Console, sent to S3 bucket or Cloud Watch Logs
- Send notifications to SNS about command status (In progress, Success, Failed)
- Integrate with IAM & CloudTrail
- Can be invoked using EventBridge
    
  

Systems Manager - Patch Manager

- Automates the process of patching managed instances
- OS updates, application updates, security updates
- Supports EC2 instances and on

System managers - Run Command

- Execute a document = script or just run a command
- Run command across multiple instances
- No need for SSH
- Command output can be shown in the AWS Console set to S3 bucket or CloudWatch
- Send notifications to SNS about command status
- Integrate with IAM and CloudTrail
- Can be invoked using Eventbridge

Systems Manager - Maintenance Windows

- Defines a schedule for when to perform actions on your instaces
- Example: OS patching, updating drivers, installing software,..
- Maintenance window contains
    - Schedule
    - Duration
    - Set of registered instances
    - Set of registered tasks
        

Systems Manager - Automation

- Simplifies common maintenance and deployment task of EC2 instances and other AWS resources
- Examples : restart instances

Cost Explorer

- visualize, understand and manage your AWS cost and usage over time
- Create custom reports that analyze cost and usage data
- Analyze your data at high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal Saving Plan (to lower prices on your bill)
- Forecast usage up to 12 months based on previous usage
- Monthly
    
  
- Hourly
    
- Cost Explorer - Savings plan
    
- Forecast Usage


Amazon Elastic Transcoder

- Elastic transcoder is used to convert media files stored in S3 into media files in the format required by consumer playback devices (phones etc..)
- Benefits
    - Easy to use
    - highly Scalable
    - Cost effective
    - Fully managed and secure, pay for what you use
        
   
AWS Batch

- batch processing at any scale
- 100000s of computer job on AWS
- with start and end
- batch services with dynamically launch EC2 instances or spot instances
- AWS batch provisions the right amount of compute / memory
- You submit or schedule batch jobs and AWS batch does the rest
- batch jobs are defined as Docker images and run on ECS
- Helpful for cost optimizations and focusing less on the infrastructure

Batch VS Lambda

Lambda

- Time limit
- Limited runtimes
- Limited temporary disk space
- Serverless

Batch

- No time limit
- Any runtime as long as its packaged as a Docker image
- Rely on EBS / instance store for disk space
- Relies on EC2 - can be managed by AWS

Amazon App Flow

- Fully managed integration services that enables you to securely transfer data between SaaS applications and AWS
- Sources - Salesforce, SAP, Zendesk, Slack and Service Now
- Destinations - AWS Service like Amazon S3, Amazon Redshift or non-AWS such as SnowFlake and SalesForce
- Frequency - on a schedule, in response to events, or on demand
- Data transformation capabilities like filtering and validations
- Encrypted over the public internet or privately over AWS PrivateLink
- Don’t spend time writing the integrations and leverage APIs immediately

AWS Amplify - web and mobile applications

- A set of tools and services that helps you develop and deploy scalable full stack web and mobile applications
- Authentication, Storage, API (REST, GraphQL), CI/CD, PubSub, Analytics, AI / ML predictions, Monitoring
- Connect your source code from GitHub AWS code commit, BitBucket, GitLab, or upload directly
