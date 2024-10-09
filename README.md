# Host a Static Website on AWS Using Ansible

## Project Overview

This project demonstrates how to automate the deployment of a static website on AWS using Ansible. The deployment involves setting up various AWS resources, including a VPC, EC2 instances, an Application Load Balancer, and Route 53 for domain management.

![Architectural Diagram](https://github.com/user-attachments/assets/dd0f9803-09a7-4750-b28f-73745e78c2aa)

## Prerequisites

Before you begin, ensure you have the following:

1. **Git** and **Visual Studio Code** installed on your computer.
2. A **GitHub** account.
3. An **AWS Account** with a Key Pair created in the AWS Console.

## Architecture

The architecture includes:

- A **VPC** with public and private subnets across two availability zones.
- Resources such as **NAT Gateway**, **Bastion Host**, and **Application Load Balancer** in public subnets.
- Security Groups to control access to resources.
- An **Ansible Server** (EC2 instance) installed in a private subnet to manage web servers.
- Multiple **Web Servers** (EC2 instances) to host the static website.
- **Route 53** for domain registration and record set management.
- **AWS Certificate Manager** to secure web communications.

## Deployment Steps

1. **VPC Setup**: Create a VPC with public and private subnets.
2. **Resource Creation**: Set up NAT Gateway, Bastion Host, and Application Load Balancer.
3. **Security Groups**: Configure Security Groups for access control.
4. **Key Pairs**: Create and import Key Pairs into AWS.
5. **Bastion Host Configuration**: Use the Bastion Host to connect to the Ansible Server in the private subnet.
6. **Ansible Server Setup**: Launch an EC2 instance and install Ansible.
7. **Web Server Configuration**: Launch EC2 instances for hosting the static website.
8. **Ansible Inventory File**: Create and push the Ansible Inventory file to your GitHub repository.

   ```ini
   [webservers]
   10.0.2.225
   10.0.3.251

   [all:vars]
   ansible_python_interpreter=/usr/bin/python3
   ```

9. **Ansible Configuration**: Set up the Ansible configuration.

   ```ini
   [defaults]
   remote_user = ec2-user
   inventory = inventory
   private_key_file = ~/.ssh/id_rsa
   ```

10. **Ansible Playbook**: Create and push the Ansible playbook to deploy the web app.

    ```yaml
    - name: deploy jupiter website
      hosts: all
      become: yes
      become_user: root

      tasks:
        - name: update ec2 instance
          yum:
            name: "*"
            state: latest
            update_cache: yes

        - name: install apache server
          yum:
            name: httpd
            state: latest

        - name: download web files from GitHub
          get_url:
            url: https://github.com/Jundyn/applicationcode/blob/main/jupiter-main.zip
            dest: /var/www/html/

        - name: unzip the zip folder
          ansible.builtin.unarchive:
            src: /var/www/html/jupiter-main.zip
            dest: /var/www/html
            remote_src: yes

        - name: copy web files to the html directory
          copy: 
            src: /var/www/html/jupiter-main/
            dest: /var/www/html
            remote_src: yes

        - name: remove temporary directories
          file: 
            path: /var/www/html/jupiter-main
            state: absent

        - name: remove the zip file
          file: 
            path: /var/www/html/jupiter-main.zip
            state: absent

        - name: start apache server
          ansible.builtin.service:
            enabled: yes
            name: httpd
            state: started
    ```

11. **Test Connection**: Verify the connection between the Ansible Server and Web Servers using Ansible ping.

12. **Load Balancer**: Use the Application Load Balancer to distribute web traffic across EC2 instances.

13. **Domain Setup**: Register your domain in Route 53 and create a record set.

14. **SSL Certificate**: Use AWS Certificate Manager to secure communications for your website.

## Repository

You can find the reference diagram and deployment scripts in the GitHub repository: [GitHub Repository](https://github.com/your-repo-link)

## Conclusion

This project effectively illustrates how to automate the deployment of a static website on AWS using Ansible, ensuring efficient resource management and secure web access. For further information, feel free to check the repository and the provided scripts. see below webpage after deployment

![Jupiter webpage](https://github.com/user-attachments/assets/d07cc4f3-61f7-48d7-b829-be78e1513db2)
