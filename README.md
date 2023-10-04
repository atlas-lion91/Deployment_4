# AWS EC2 Deployment 5 Guide: Monitoring with Aws CloudWatch

October 2nd, 2023

By: Khalil Elkharbibi




## Introduction & Purpose

Welcome to the AWS EC2 Deployment Guide, where we'll take you through the process of deploying a Python URL shortener application on an EC2 instance. Unlike our previous deployments with Elastic Beanstalk, this guide focuses on manual setup and configuration, offering you greater control and customization of your infrastructure.

## Prerequisites

Before getting started, ensure you have the following prerequisites in place:

- **AWS Account**: You need an active AWS account.
- **EC2 Instance**: Launch an EC2 instance (we recommend t2.medium).
- **Software**: Make sure you have Git, Python, Jenkins, and Nginx installed.

## Step-by-Step Deployment

### Setting Up a Virtual Environment

Creating a virtual environment allows you to isolate your Jenkins environment from other system dependencies, ensuring consistency and reducing conflicts

Let's begin by creating a virtual environment for Jenkins:

1) **AWS Account**: If you don't have one already, create an AWS account.

2) **EC2 Instance**: Launch an EC2 instance in a new public subnet, selecting an appropriate instance type basedon your specific workload requirements. In this case we chose a t2.medium.

3) **Security Group**: Configure the EC2 instance's security group to allow traffic on ports 80, 8080, 8000, and 22.

4) **Git Installation**: Install Git (https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

5) **Python and Pip**: Install Python and Pip with this command:
    ```bash
    sudo apt install python3.10-venv python3-pip -y
    ```

6) **Jenkins Setup**: Jenkins is an automation server that simplifies building, testing, and deploying applications, enhancing the efficiency of your development and deployment processes. 
 
  * Install and set up Jenkins (https://www.jenkins.io/doc/book/installing/linux/#debianubuntu) on your EC2 instance. Additionally, install the "Pipeline Keep Running Step" plugin to ensure continuous process execution.
 
 **Advantages**:
- *Automation*: Jenkins automates repetitive tasks, reducing human error and ensuring consistency.
- *Scalability*: It scales easily to accommodate complex deployment pipelines.
- *Integration*: Jenkins integrates seamlessly with other tools and services.

8) **Nginx Configuration**: Nginx is a high-performance web server and reverse proxy that enhances security, load balancing, and the overall performance of your application.


   * Install Nginx (https://www.nginx.com/blog/setting-up-nginx/) and update the default configuration file (`/etc/nginx/sites-enabled/default`) to act as a reverse proxy, forwarding requests to the application server running on port 8000.

    ```nginx
    server {
        listen 5000 default_server;
        listen [::]:5000 default_server;
        # We changed the port from 80 to 5000

        # Also scroll down to where you see “location” and replace it with the text below:
        location / {
            proxy_pass http://127.0.0.1:8000;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }

    ```
**Advantages**:
- **Load Balancing**: Nginx distributes incoming traffic, improving application availability.
- **Security**: It offers robust security features to protect against common web threats.
- **Performance**: Nginx's lightweight design and event-driven architecture boost application speed.

### Monitoring with CloudWatch: Amazon CloudWatch provides real-time monitoring and actionable insights, ensuring your infrastructure operates efficiently and remains responsive to changes.

To monitor your infrastructure effectively, follow these steps:

1. **CloudWatch Agent**: Install the CloudWatch agent (https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) on your EC2 instance.

2. **Configuration**: Run the configuration wizard to set up the CloudWatch agent. Ensure it collects metrics.

3. **Agent Activation**: Start the CloudWatch agent and verify metric collection:
    ```bash
    sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json
    sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status
    ```
**Advantages**:
- **Automated Monitoring**: CloudWatch collects and monitors data automatically without additional configuration.
- **Cost-Effective**: It offers a cost-effective monitoring solution, with pay-as-you-go pricing.
- **Integration**: CloudWatch seamlessly integrates with AWS services, providing comprehensive observability.

<img width="807" alt="cpu_system" src="https://github.com/atlas-lion91/Deployment_4/assets/140761974/4e05fdff-e6e5-41ec-8766-cbc2d235cbfe">

### Jenkins Pipeline Configuration

Now, configure your Jenkins pipeline for seamless automation:

1. **Pipeline Job**: Create a multibranch pipeline job in Jenkins.

2. **GitHub Integration**: Configure the job to connect to your GitHub repository.

3. **Webhook Setup**: Create a webhook in GitHub for your repository to trigger Jenkins builds automatically.

4. **Branch Scanning**: Set up branch sources to scan for Jenkinsfiles.

5. **Initial Run**: Execute the initial pipeline run.

### Server Performance Evaluation

During deployment and builds, it's essential to monitor server performance. Use tools like `htop` and CloudWatch metrics and verify application accessibility by visiting your EC2 public IP on port 8000 in a web browser.

### CPU Usage Alerts

Set up a CloudWatch alarm to receive notifications when CPU usage exceeds 15% for 5 minutes, helping you proactively manage server resources.

### T2 Micro Considerations

Be mindful that a t2.micro instance may encounter resource limitations, potentially leading to longer build times, performance bottlenecks under moderate loads, and instability during resource-intensive tasks.

### Jenkins Email Notifications

To set up email notifications in Jenkins:

1. In "Manage Jenkins" > "Configure System," provide the system admin email address.

2. Add a post-build action in your Jenkinsfile or configure it in the Post-build Actions (https://plugins.jenkins.io/email-ext/#plugin-content-pipeline-step) section to enable Editable Email Notification. This allows you to receive email alerts when builds fail.

## Issues & Troubleshooting

### Jenkins Installation Error - "GPG Key Retrieval Failed"

If you encounter this error during Jenkins installation, it may be due to changes in Jenkins apt repositories. Resolve it by clearing Jenkins cache, reinstalling Java versions, and reinstalling packages.

### Jenkins Build Failure

Before initiating your Jenkins build, make sure to complete the following steps:

* Clone the repository.
* Create a branch for your changes.
* Update the Jenkinsfile as required.
* Merge your branch back into the main branch.

Failing to follow these steps may result in a failed Jenkins build.

### Incorrect Application Port

If you face accessibility issues with your app, ensure you're using the correct port (8000) instead of 5000. Restart the EC2 instance if needed.


## Optimization Strategies

To optimize your deployment, consider the following strategies:

- **Vertical Scaling**: Upgrade the EC2 instance to a larger type as your application's resource demands grow.

- **Horizontal Scaling**: Deploy multiple instances behind a load balancer for better traffic distribution and reliability.

- **Caching Mechanisms**: Implement caching to reduce server load.

- **CDN Integration**: Integrate a Content Delivery Network (CDN) for serving static assets and minimizing latency.

- **AMI Creation**: Create an Amazon Machine Image (AMI) for launching pre-configured EC2 instances.

- **Infrastructure Automation**: Automate infrastructure provisioning using tools like Terraform.

## System Diagram

Here's a visual representation of the deployed system:


## Conclusion

This guide has taken you through the process of setting up a robust infrastructure for running an application on AWS. CloudWatch offers monitoring capabilities, while Jenkins streamlines builds and deployments. As your application scales, remember the optimization strategies to ensure efficiency and reliability.

