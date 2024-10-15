## Self-Study Summary: Apache Load Balancer Configuration

In this task, I learned how to configure an Apache Load Balancer on an Ubuntu 20.04 EC2 instance, distributing traffic between two RHEL8 web servers. The goal was to ensure efficient traffic distribution and implement load balancing by configuring Apache modules and using traffic-based methods.

### Key Steps:
1. **Setting Up the Environment**: 
   - Launched an Ubuntu EC2 instance for the load balancer and configured two RHEL8 web servers to handle incoming traffic.
   - Opened TCP port 80 on the load balancer to allow HTTP traffic.

2. **Installing and Configuring Apache**:
   - Installed Apache on the Ubuntu load balancer instance, enabling the necessary proxy and balancing modules.
   - Configured Apache to route traffic to the two web servers using the **bytraffic** method to balance requests based on current traffic load.

3. **Testing the Load Balancer**:
   - Verified the load balancer setup by accessing its public IP and checking the HTTP GET requests logged on both web servers.
   - Ensured traffic was evenly distributed by setting equal load factors on both web servers.

4. **Local DNS Resolution**:
   - Simplified server management by configuring local DNS entries in the `/etc/hosts` file on the load balancer, allowing me to refer to the web servers by custom names (e.g., `Web1`, `Web2`).

### Key Learnings:
- **Load Balancing Methods**: I explored the `bytraffic` load balancing method, which distributes traffic based on server load. I also discovered other methods, such as `bybusyness` and `byrequests`.
- **Apache Proxy Configuration**: I became familiar with Apache’s proxy modules (`proxy`, `proxy_balancer`, `proxy_http`), which enable efficient traffic routing to multiple web servers.
- **Practical Testing**: I validated the configuration by monitoring server logs and confirmed that requests were evenly distributed between both web servers.

This exercise enhanced my understanding of load balancing concepts, web server traffic management, and the use of Apache modules for efficient load distribution.