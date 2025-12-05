# Lab 6 Microservices - Project Report

## 1. Configuration Setup

**Configuration Repository**: [Link to your forked repository]

Describe the changes you made to the configuration:

- What did you modify in `accounts-service.yml`?
```` yml
# HTTP Server
server:
  port: 5555   # HTTP (Tomcat) port
````
- Why is externalized configuration useful in microservices?
Externalized configuration is useful in microservices because it separates configuration from code, allowing dynamic updates without redeploying services. It ensures consistency across multiple instances and enables different configurations for different environments.

---

## 2. Service Registration (Task 1)
### Accounts Service Registration

![Accounts Registration Log](docs/screenshots/accounts-registration.png)

When the microservice starts:

- It loads the Eureka client configuration, where the service name, network address, port, and other relevant parameters are defined.
- It establishes a connection with the Eureka Server.
- It sends an initial registration request that includes the service’s status, metadata, and the URL where it can be reached.
- Once Eureka validates and accepts the registration, it marks the service as UP, indicating that it is operational and available to be discovered by other microservices in the system.

### Web Service Registration

![Web Registration Log](docs/screenshots/web-registration.png)

When the web service needs to communicate with the Accounts service, it requests from the Eureka Server the list of instances registered under that identifier (ACCOUNTS-SERVICE). Eureka returns the list of registered instances. The client then selects an available instance and sends its HTTP requests directly to that Accounts service instance.

---

## 3. Eureka Dashboard (Task 2)

![Eureka Dashboard](docs/screenshots/eureka-dashboard.png)

The registered services are ACCOUNTS-SERVICE and WEB-SERVICE. The information displayed for these instances includes:
- Service Name
- Status (indicates whether the instance is available or in another state)
- IP Address
- Port
- Availability Zone
- AMIs (not used)

---

## 4. Multiple Instances (Task 4)

![Multiple Instances](docs/screenshots/multiple-instances.png)

- **What happens when you start a second instance of the accounts service?**
When a second instance is started, Eureka detects it and registers it alongside the first instance. Therefore, when a client wants to use this service, Eureka returns the list of both instances.

- **How does Eureka handle multiple instances?**
Eureka maintains a record of all active instances. As explained earlier, if a client requests information about a service, Eureka provides the complete list of instances registered under that service name.

- **How does client-side load balancing work with multiple instances?**
When the client receives the list of instances, it distributes requests among the available instances to achieve load balancing and improve system resilience. 

---

## 5. Service Failure Analysis (Task 5)

### Initial Failure

![Error Screenshot](docs/screenshots/failure-error.png)

When the Accounts service instance on port 3333 is stopped, any request sent to the Web service that tries to communicate with that instance will initially fail, since the instance is no longer available. Requests directed to the other instance (e.g., the one running on port 2222) will continue to function normally.

### Eureka Instance Removal

![Instance Removal](docs/screenshots/instance-removal.png)

Explain how Eureka detects and removes the failed instance:

- **How long did it take for Eureka to remove the dead instance?**
In our configuration, it took approximately 10 seconds. This is because the instance stops sending heartbeats, and once the time since the last renewal exceeds the lease expiration duration (10 seconds), Eureka marks the instance as unavailable and evicts it from the registry.

- **What mechanism does Eureka use to detect failures?**
Periodic heartbeats sent by each instance. If the server does not receive a heartbeat within the expected time, it considers the instance failed and removes it from the registry.

---

## 6. Service Recovery Analysis (Task 6)

![Recovery State](docs/screenshots/recovery.png)

Answer the following questions:

- **Why does the web service eventually recover?**
This happens because the Web service uses Eureka as a dynamic discovery mechanism. Although it initially fails when trying to communicate with the instance that was stopped, once Eureka detects that this instance is no longer available, it updates its registry and provides the client with a revised list of active instances. From that moment on, requests are correctly routed to the instance that is still running.

- **How long did recovery take?**
The recovery time corresponds to the interval required for Eureka to detect the instance failure and remove it from its registry.

- **What role does client-side caching play in the recovery process?**
The client caches the list of instances provided by Eureka in order to avoid excessive queries to the server. As long as this cache still contains the failed instance, the client will continue trying to communicate with it. Once the cache is refreshed with the updated list of available instances, the client stops using the failed instance and the system returns to normal operation.

---

## 7. Conclusions

In this lab session, I learned:

- The practical aspects of microservices architecture and how it enables applications to scale through independent and decoupled services.

- How services register themselves in Eureka and how clients can dynamically discover them without relying on static configurations.

- How Eureka’s heartbeat mechanism works to detect which instances are active and which have failed, and how this process contributes to the system’s self-healing capabilities.

- The role of client-side load balancing, which distributes requests across multiple instances to improve performance and availability.

---

## 8. AI Disclosure

ChatGPT was used to correct grammatically error of this report. Approximately 10% of the report text was generated with AI assistance.


