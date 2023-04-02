
Ref: https://www.tigera.io/learn/guides/container-security-best-practices/docker-security/


Docker security encompasses the runtime, build, and orchestration of containers. 
Security aspects include base images, the Dockerfile, the container runtime, and securing the Docker daemon. 
We should also ensure proper configuration of container isolation, user privileges, and security best practices for container orchestration at scale.


**5 Security Risks in Docker Container Deployment and How to Mitigate Them**

    - Unrestricted Traffic and Unsafe Communication
    - Vulnerable and Malicious Container Images
    - Unrestricted Access
    - Host Kernel Vulnerabilities
    - Breaking Out of Containers

**6 Docker Container Security Best Practices**

    1. Avoid Root Permissions
    2. Use Secure Container Registries
    3. Limit Resource Usage
    4. Scan Your Images
    5. Build Your Networks and APIs for Security
    6. Docker Container Monitoring

I was able to releate all above best practises being followed at nationwide.

This year at Nationwide,we are doing the all vulnerability fixes.

1.We are making sure that all the images are scanned periodically to look new  vilnerabilities.

2.Recently I have fixed bunch of critical vulnerabilities releated the actual OS level base images/Software package releated and some complience releated like avoid using the root user.

  a:  jenkins latest image was upgraded with jenkin:3.96-alpine and also side car images which had critical vulnerability 
  
  b:  Centos7 and python3.8 had vulnerabilities ,we upgraded to python 3.9.11 to fix the critical vulnerabilities.


