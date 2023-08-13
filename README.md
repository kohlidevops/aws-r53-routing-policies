# aws-r53-routing-policies

**To configure Health checks and routing policies in AWS Route 53**

I assuming below points are created in AWS account before starting

1. Launch one instance in Mumbai region
2. Launch one instance in Ohio region
3. Launch one instance in Singapore region
4. Add user data for all 3 instances using below link
   https://github.com/kohlidevops/aws-solution-architect/blob/main/ec2-user-data.sh
5. Create ALB and associate with Instance which is launched in Mumbai region
6. Create CNAME record and associate with ALB URL
7. Create A record with Alias for ALB URL
8. Try to create a CNAME for root domain (example.com) with ALB URL – Shouldn’t work
9. Try to create a Alias for root domain (example.com) with ALB URL – should work

**Route 53 Supports the following Routing Policies**

• Simple
• Weighted
• Failover
• Latency based
• Geolocation
• Multi-Value Answer
• Geoproximity (using Route 53 Traffic Flow feature)

**Routing Policies – Simple**
1. Typically, route traffic to a single resource
<img width="306" alt="image" src="https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/ab28e24b-ed22-43cb-a9b7-3208f6b3b6c7">

2. Can specify multiple values in the same record
<img width="313" alt="image" src="https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/08c1de40-360a-488a-b876-28511d982ed0">

3.  If multiple values are returned, a random one is chosen by the client
4.  When Alias enabled, specify only one AWS resource
5.  Can’t be associated with Health Checks

**Handson**

Create A record with one instance IP and TTL 20 sec

Associate one more instance IP to same A record

Associate one more instance IP to same A record

Now 3 IP’s are associated with one A record

Dig it help of online or command line

**Routing Policies – Weighted**

1. Control the % of the requests that go to each specific resource
2. Weights don’t need to sum up to 100
3. DNS records must have the same name and type
4. Can be associated with Health Checks
5. Use cases: load balancing between regions, testing new application versions…
6. Assign a weight of 0 to a record to stop sending traffic to a resource
7. If all records have weight of 0, then all records will be returned equally

**Handson**

Create A record with weighted policy for 3 instance IP like below

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/d698f4a4-0353-4302-93ce-b57ebf4f9a08)

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/8a7cc35a-1df0-48a2-8855-d6d1e97bcab2)

Most of the traffic served to us-east-1 instance, which is weighted 70 percent.
![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/0b47c422-710a-4fa9-8fca-981caea86fd3)

**Routing Policies – Latency-based**

1. Redirect to the resource that has the least latency close to us
2. Much Helpful when latency for users is a priority
3. Latency is based on traffic between users and AWS Regions
4. Germany users may be directed to the US (if that’s the lowest latency)
5. Can be associated with Health Checks (has a failover capability)

**Handson**
1. Create A record with latency for 3 instances IP’s
2. Ohio instance should associate with us-east-2 region
3. Singapore instance should associate with ap-southeast-1 region
4. Mumbai instance should associate with ap-south-1 region

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/eaa111e8-ce34-4755-8ac7-f1fc7669c8a0)

If you hit the URL from Mumbai region CloudShell, traffic served to Mumbai based instance
![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/fa43c83b-ab1e-4507-91a0-8a88b6edb82f)

Likely, Ohio and Singapore region
![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/4329d4f4-54bc-4e23-97d4-0d63737f22cc)
![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/b935929b-ef7a-4533-bb52-dec8c5a0ce43)

**Route 53 – Health Checks**

1. HTTP Health Checks are only for public resources
2. Health Check => Automated DNS Failover:
   a. Health checks that monitor an endpoint such as application, server, other AWS resource
   b. Health checks that monitor other health checks such as Calculated Health Checks
   c. Health checks that monitor CloudWatch Alarms – e.g., throttles of DynamoDB, alarms on RDS, custom metrics. It helpful for private resources.
3. Health Checks are integrated with CW metrics

**Health Checks – Monitor an Endpoint**

1. 15 global health checkers will check the endpoint health
   a. Healthy/Unhealthy Threshold – 3 (default)
   b. Interval – 30 sec (can set to 10 sec – higher cost)
   c. Supported protocol: HTTP, HTTPS and TCP
   d. If > 18% of health checkers report the endpoint is healthy, Route 53 considers it Healthy. Otherwise, it’s Unhealthy
   e. Ability to choose which locations you want Route 53 to use
2. Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes
3. Health Checks can be setup to pass / fail based on the text in the first 5120 bytes of the response
4. Configure you router/firewall to allow incoming requests from Route 53 Health Checkers

**Route 53 – Calculated Health Checks**

1. Combine the results of multiple Health Checks into a single Health Check
2. You can use OR, AND, or NOT
3. Can monitor up to 256 Child Health Checks
4. Specify how many of the health checks need to pass to make the parent pass

**Health Checks – Private Hosted Zones**

1. Route 53 health checkers are outside the VPC
2. They can’t access private endpoints (private VPC or on-premises resource)
3. You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a Health Check that checks the alarm itself

**Handson**

1. Create 3 Health checks for 3 region instances
2. For example, us-east-1 Healthcheck should have us-east-1 instance IP
3. For example, ap-southeast-1 Healthcheck should have ap-southeast-1 instance IP
4. For example, ap-south-1 Healthcheck should have ap-south-1 instance IP

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/4ed59663-f934-4f92-9c6c-1158071025b2)

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/2ee5ce75-d158-448f-b29c-b630f7542ea3)

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/cc94c5ef-e2ef-4acb-8bb9-d13ff6e5060c)

After some time, All the Health checks is Healthy.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/2cfe45c4-6f08-44f8-97ea-20383ca90332)

I will purposedly make unhealthy for ap-southeast-1 with help of ap-southeast-1 instance security group – to remove Port 80. So, automatically ap-southeast-1 should be Unhealthy.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/ef1c737b-c044-4a99-9f0c-eeb366b08f38)

Perfect, Now ap-southeast-1 is Unhealthy as we expected

**Create one more Healthcheck for Calculated Healthcheck**

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/59f2d0f7-5aef-4b3d-adb8-150fc5061b91)

My CalculatedHealthcheck is Unhealth, because one of my region Healthcheck is Unhealthy

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/e15b8e9e-e27f-4dab-815c-15e60516d773)

**Routing Policies – Failover (Active-Passive)**

If Primary instance goes down, then secondary instance will response to the user request. Its a DR method (Disaster Recovery).

**Handson**

1. Create A record for us-east-2 instance for failover as primary
2. IP should be us-east-2 instance IP
3. Healthcheck id should be us-east-2 Healthcheck

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/269d0c07-9e61-4eb8-a330-210c7d4b32ec)

4. Create one more record for ap-south-1 instance for failover as secondary
5. IP should be ap-south-1 instance IP
6. Healthcheck id should be ap-south-1 Healthcheck

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/e318950a-e3e2-47c1-b21e-422232d05af6)

When I hit my failover URL, traffic sent to my primary region instance

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/36ad1f48-cfcd-43ca-accc-9bc211561ffb)

If I purposedly make us-east-2 instance as Unhealthy with the help of removing Port 80 from instance security group, then my traffic should sent to ap-south-1 instance.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/cc7ad0a4-b84c-43f1-a26a-c86e6dbdb5b8)

us-east-2 healthcheck is unhealthy, so request passed to ap-south-1 instance.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/c338360c-33ba-4a0d-adbf-533bbbb98942)

**Routing Policies – Geolocation**

1. It's different from Latency-based policy
2. This routing is based on user location
3. Specify location by Continent, Country or by State
4. Should create a “Default” record (in case there’s no match on location)
5. Use cases: website localization, restrict content distribution, load balancing
6. It can be associated with Health Checks

**Handson**

1. Create 3 A record with geolocation for 3 region instances
2. United states associated with us-east-2 instance
3. Asia associated with ap-southeast-1 instance
4. Default associated with ap-south-1 instance
5. If any thing doesn’t match, traffic is going to ap-south-1 instance

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/586caf8e-11f2-4bd6-a0de-8ab4f554f403)

My traffic is goes to ap-southeast-1 instances, which is closer to me.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/11df0bed-9c40-447f-9435-11397bda7246)

**Routing Policies – IP-based Routing**

1. Routing is based on clients’ IP addresses
2. You provide a list of CIDRs for your clients and the corresponding endpoints/locations (user-IP-to-endpoint mappings)
3. Use cases: Optimize performance, reduce network costs
4. Example: route end users from a particular ISP to a specific endpoint

**Routing Policies – Multi-Value**

1. Use when routing traffic to multiple resources
2. Route 53 return multiple values/resources
3. Can be associated with Health Checks (return only values for healthy resources)
4. Up to 8 healthy records are returned for each Multi-Value query
5. Multi-Value is not a substitute for having an ELB

**Handson**

1. Create 3 A records with Multivalue answer for 3 region instances.
2. Routing policy should be Multivalue answer.
3. Healthcheck ID mapped to their regarding Healthcheck.
4. Record ID should be meaningful.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/c6cc9c70-729a-4a80-9934-11b16c9c23c8)

If you dig it, you can get all the values mean instances. Therefore, my request has been sent to all the resources mean all region EC2 instances.

![image](https://github.com/kohlidevops/aws-r53-routing-policies/assets/100069489/513d8688-a4c6-4514-aa3d-dcf81f1f5447)

Perfect, That’s it.
