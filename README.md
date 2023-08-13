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

Route 53 Supports the following Routing Policies
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




