# System Architecture Overview
- The system is designed with a microservices architecture to ensure modularity, resilience, and ease of scaling. It leverages AWS-managed services to minimize operational overhead and maximize uptime.
- The system can also include component where is should be serverless, event driven architecture  for better scalability, cost-effectiveness, and reduced operational overhead.
# When to choose microservices and serverless.
- Chooses serverless if:
  + traffic is low, unpredictable, or spiky
  + Event-Driven Workloads (e.g., trades, payments, logs)
- Chooses microservices if:
  + Long-Running, Compute-Intensive, High-frequency, Consistent workload.
  + Complex Stateful Applications: Serverless is stateless by design, making stateful applications harder to manage
  + ACID transactions at high scale (SQL database)
 
=> depend on the trading application underline logic (which i am not familiar with), the architecture can involve both microservices and serverless
# Architecture Component
### 1. Hosting Frontend
- Use cloudfront + s3 to host and cache frontend asset (html, css, js, static files,..)
- Can also use cloudfron to cache backend response (eg, chache response from ALB)
  
=> reduce latency
### 2. Hosting Backend
#### 2.1 Loadbalancing
- for microservice we can use ALB ingress, or ALB + nginx ingress
- for serverless we can use API Gateway
  
=> ALB offer request handling + load balancing, API Gateway offer request handling + rate limmiting + caching

=> both option support authentication/authorization (cognito user pool, lambda authorizer)
#### 2.2 backend platform / engine
- for microservice we can use services like EKS (more operation overhead, complex), ECS (easier to scale with Auto Scaling, fargate less complex).
- for serverless we can use lambda
### 3. Hosting Database
#### 3.1 Databse services
- for microservice Amazon Aurora for high availability and low-latency trading data storage.
- for serverless use DynamoDB, a serverless NoSQL data store.
#### 3.2 Caching for database
- for Amazon Aurora use ElasticCache or DynamoDB
- for DynamoDB use Amazon DynamoDB Accelerator (DAX)
  
### 4. Security & Compliance
- AWS WAF for L7 protection (sql injection, cross site scripting, ...)
- Firewall: security group, NACL, AWS Network Firewall.
- IAM & Cognito for authentication and API access management.
- KMS (Key Management Service) for encrypting sensitive data.
- Shield Advance for DDos protection
- AWS Config for configuration compliance
- Amazon GuardDuty for thread detection
  
### 5. Monitoring & Logging
- Amazon CloudWatch for real-time monitoring, alerts, and logging.
- AWS X-Ray for distributed tracing of microservices.

### 6. Application internet exposure
- Route 53 as domain name registra
- Route 53 public hosted zone as DNS service
- AWS ACM for cert management
- An alias DNS record point to Cloudfront + ALB or API gateway
  
# Conclusion
- Each mentioned services have HA (High Availability) and sacling feature embedded to it, and beacause we use load balancing in our architecture, our system is HA and scalable.
- With caching of cloudfront and database cache layer, the response time of <100ms is achievable
- initial scale  should be choose (ram, cpu, ...) to fit 500 Requests Per Second (RPS), if serverless lambda can serve upto 1000 RPS > 500 RPS
- Scaling Beyond Initial Setup, we can setup auto scale for ECS, Cluster Autoscaler for EKS. For serverless we don't need to care about scaling, but we still need to aware of the services quotas.
- Scale the system to globally:
  + for microservice architecture we can use AWS global accelerator on top of ALB.
  + for serverless use DynamoDB global table + deploy lambda in multiple regions + API Gateway in multiple regions + route 53 latency base policy
