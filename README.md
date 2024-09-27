# Troubleshooting-Connectivity-in-a-Virtual-Private-Cloud-VPC-
In this project, I practiced troubleshooting connectivity issues within a Virtual Private Cloud (VPC) environment. The focus was on identifying and remediating misconfigured security group rules that impacted communication between Amazon EC2 instances.
 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
In this project, I practiced troubleshooting connectivity issues within a Virtual Private Cloud (VPC) environment. The focus was on identifying and remediating misconfigured security group rules that impacted communication between Amazon EC2 instances. The project involved examining and updating security groups to ensure proper traffic flow while following the principle of least privilege. I was troubleshooting access issues between three key instances: an App Server (in a private subnet), a Bastion Host, and a Public Server (in a public subnet). I had to develop and run connectivity tests to identify security group misconfigurations and adjust rules to permit only the required traffic. I applied best practices to ensure security groups reference other security groups where appropriate, enhancing the security posture of the VPC.
<br />

<h2>Languages and Utilities Used</h2>

- <b>Bash: Utilized for running SSH commands (ssh-agent, hostname, ssh -A, etc.) to test connectivity between Amazon EC2 instances.</b>
- <b>AWS Management Console: Used to inspect and modify security group rules and EC2 instance settings.</b>
- <b>AWS EC2: The service where instances like App Server, Bastion Host, and Public Server are managed..</b>
- <b>AWS Systems Manager (Session Manager): Simplified the connection process by allowing you to establish SSH sessions through the browser.</b> 
- <b>Security Groups: Virtual firewalls in AWS that were configured and modified to control inbound and outbound traffic.</b>
- <b>Apache Server: Used in the challenge section to test web connectivity through HTTP/HTTPS.</b> 
- <b>Web Browser: Used to test HTTP connectivity by accessing the public IP address of the Apache Server.</b>

<h2>Environments Used </h2>

 <b>Cloud Platform:
AWS (Amazon Web Services): This project was conducted entirely in AWS, utilizing several of its services.
</b> 

<h2>Program walk-through:</h2>

<p align="center">

Figure 1: Three Amazon EC2 instances in VPC A: The App Server instance is an application server that resides in the private subnet. The Bastion Host instance is used to connect to internal, protected resources, such as App Server. The Public Server instance is a test server. It is used to test whether Secure Shell (SSH) traffic is allowed into App Server from any hosts besides the Bastion Host instance. The Public Server instance resides in the public subnet.
 <br/>
![Before Image of three Amazon EC2 instances](https://github.com/user-attachments/assets/709b8d1d-e50b-452e-95c7-0b28c7ca7d46)

<br />

Review App Server Inbound Rules: This configuration allows inbound connections to App Server from any IP address over port 22. All other ports are denied access. This allows Bastion Host to connect to App Server. However, unauthorized hosts from any IP address (source 0.0.0.0/0) can also connect to App Server. Currently, this security group is noncompliant based on the company security policy.
 <br/>
![Review App Server Inbound rules](https://github.com/user-attachments/assets/4a9c9c4f-6128-4202-97db-4c38b0a405cb)



<br />

Figure 2: The user can access the Bastion Host and Public Server instances. From there, the user can connect by using SSH into the App Server instance from either of the public Bastion Host or Public Server instances.
 <br/>
![Figure 2 Test Connectivity](https://github.com/user-attachments/assets/d9b619a7-95f6-46fc-8376-19de3b0b639a)

<br />

I was able to connect to the App Server using the Bastion Host instance:
 <br/>
![Successful SSH to App Server from Bastion Server](https://github.com/user-attachments/assets/f3de6f20-45bb-42d4-812e-0c202bd42345)


<br />

I was able to connect to the App Server using the Public Server instance:
 <br/>
![Successful SSH to App Server from Public Server](https://github.com/user-attachments/assets/770bfe89-e564-4b5d-8cfb-4b856cc46d66)


<br />

Figure 3: Updating the App Server security group allows traffic only from the Bastion Host as a source. 2. This blocks the connection attempts from the Public Server.
 <br/>
![Figure 3 Restricting Access to Public Server](https://github.com/user-attachments/assets/78575ae0-76d1-4e1d-8efd-bb0ee35e37fd)


<br />

Edit Inbound Rules to only allow traffic from the Bastion Host:
 <br/>
![Update Security Group Rules for App Server from Bastion Server Only](https://github.com/user-attachments/assets/37193e94-fab7-4bd0-809b-0ca12beff73f)

<br />

Unable to connect to the App Server from the Public Server after updating the inbound rules: This is compliant behavior.
 <br/>
![Unsuccessful SSH to App Server from Public Server](https://github.com/user-attachments/assets/6c8c230b-32db-4805-9b34-62d08708b8d1)


I checked for connectivity to the App Server from the Bastion Host after updating the inbound rules: It was successful. This is compliant behavior.
 <br/>
![Successful SSH to App Server from Bastion Server](https://github.com/user-attachments/assets/c42c1a6b-0401-4661-ab9d-cc2997386aa2)

<br />

Figure 4: I turned the Public Server into another Bastion Host, and added it to the BastionHostSG security group. The user can use the Bastion Host or the second Bastion Host (Public Server) as a compliant way to connect by using SSH into the App Server. Both the Bastion Host and Public Server are assigned the BastionHostSG.
 <br/>
![Figure 4](https://github.com/user-attachments/assets/d1805b90-76e2-4429-a8ca-cd1ba4590422)


<br />

I updated the security group attached to App Server so that it only allows traffic from instances with the BastionHostSG attached:
 <br/>
![Update Security Group Rules for App Server from Bastion Server Instances](https://github.com/user-attachments/assets/3625377c-82ec-4291-a1f4-049606b191ed)


<br />

I checked for connectivity to the App Server from the Public Server after updating the security group: It was successful. This is now compliant behavior because you made the Public Server an additional bastion host by adding it to the Bastion Host security group.
 <br/>
![Successful SSH to App Server from Public Server after adding to Bastion SG](https://github.com/user-attachments/assets/823855de-93fc-403d-8226-288db782e42d)


<br />
<h2>Challenge: Troubleshooting connectivity within the VPC</h2>
<br />

Figure 5: The Apache server is running on an EC2 instance. The EC2 instance needs the correct security group rules to allow a connection through the internet. This shows a sample test page or a custom html page to show that you have connectivity to your Apache server.
 <br/>
![Figure 5](https://github.com/user-attachments/assets/d0ad2e3d-c2be-449d-8e2b-d74d064379e3)


<br />

Here are the inbound rules for the Apache Server prior to me testing connectivity using http://3.8.111.1
 <br/>
![Inbound Rules before testing connectivity  to Apache Server](https://github.com/user-attachments/assets/5fabef28-8431-4afa-8e98-f2fe1897b570)
<br />

I was not able to connect to the Apache server using http://3.84.111.1:
 <br/>
![Unsuccessful Apache Server](https://github.com/user-attachments/assets/8504963f-0c90-4e43-a2a1-cb9b5088e04d)
<br />

I suspected issue was that the instance need to allow traffic from "HTTP" port 80 instead of "HTTPS" port 443. I made configuration changes to address to address the issue.
 <br/>
![image](https://github.com/user-attachments/assets/ad991e31-c5c8-481f-846e-6b2eb9198d82)

<br />

I refreshed the browser and I was able to connect to the Apache test page.
 <br/>
![Successful connection to Apache Server](https://github.com/user-attachments/assets/f5e74d12-4a1e-4ab9-a1ec-8e9cb2585717)

<br />

<h2>Summary</h2>
In this lab, I practiced troubleshooting connectivity issues within a Virtual Private Cloud (VPC) by managing and updating security group rules for Amazon EC2 instances. The main focus was to ensure compliance with company policies by restricting SSH access to the App Server instance through the Bastion Host only. I inspected existing security group rules and modified them to adhere to the principle of least privilege. Connectivity tests from both the Bastion Host and Public Server confirmed compliance after adjustments. In the challenge section, I troubleshooted a connectivity issue with an Apache Server, which involved updating security group rules to allow web traffic (HTTP/HTTPS) from any IP address. After making these changes, the Apache test page successfully loaded. This lab enhanced my skills in configuring and securing AWS EC2 instances within a VPC.
<br />


