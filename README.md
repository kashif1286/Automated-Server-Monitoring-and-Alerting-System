
# Automated Server Monitoring and Alerting System on EC2

Here’s a detailed step-by-step guide to create a system that monitors server health (CPU, memory, disk usage) on EC2 instances and automatically sends alerts via Amazon SNS when thresholds are exceeded. but i creted only CPU UTILIZATION in this project but i ma provide (memory, disk usage)

![Blank diagram (1)](https://github.com/user-attachments/assets/5d9ac605-56ab-42e6-a74b-1e4088c28ac0)

# Step 1: Set Up Your EC2 Instance

1.1 Launch an EC2 Instance

1. Log in to the AWS Management Console:

Go to AWS Console.

Sign in with your AWS account credentials.

2. Navigate to EC2 Dashboard:

In the AWS Management Console, search for "EC2" and click on EC2 under the Services section.

3. Launch a New EC2 Instance:

On the EC2 Dashboard, click on the Launch Instance button.

Give your instance a Name (e.g., “ServerMonitoringInstance”).

4. Choose an Amazon Machine Image (AMI):

Select Amazon Linux 2023 or Amazon Linux 2 (recommended for compatibility with most monitoring tools).

5. Choose an Instance Type:

Select an instance type based on your requirements.

For a basic monitoring system, t2.micro (which is eligible for the free tier) is sufficient.

6. Configure Key Pair (Login):

In the Key pair (login) section, either create a new key pair or choose an existing one.

If creating a new key pair:

Choose Create new key pair.

Select RSA or ED25519 as the key type.

Click on Create key pair. 

The private key (.pem file) will be downloaded to your computer.

7. Network Settings (Security Group Configuration):

Set up your security group to allow SSH access and optionally HTTP/HTTPS if needed for web access.

Create a new security group or choose an existing one.

Ensure that SSH (port 22) is open to allow you to connect.

You can restrict the source IP to only your IP address for better security.

8. Example Security Group rules:
SSH: Port 22, Source: Your IP (for secure access).

HTTP: Port 80, Source: Anywhere (if you want web access).

HTTPS: Port 443, Source: Anywhere (if you use SSL).

9. Configure Storage:

For a simple monitoring setup, the default 8 GB of storage is enough.

If you anticipate more intensive monitoring tasks or logging, you can increase the size.

Launch the Instance:

Review all settings and click Launch Instance.
Wait for the instance to be launched.
 This may take a few minutes.

![ec1](https://github.com/user-attachments/assets/8135310b-24e3-4f32-af06-3bc2cd1db241)

![ec2](https://github.com/user-attachments/assets/573913c5-942a-405a-9c3a-55c7d68f0c18)

![ec3](https://github.com/user-attachments/assets/e441f322-c3aa-471e-8453-e362cde80125)

![ec4](https://github.com/user-attachments/assets/e3534b8e-7a99-4aa3-b523-a0360d1d1d82)

cheak the status, instance ID , public IP adress

![run1](https://github.com/user-attachments/assets/a5f5e45d-a1b1-4c21-a8a4-8d9fcd0bfe5f)

connect to instance

![run2](https://github.com/user-attachments/assets/7e805dc8-c60b-4874-be95-8b0689773927)

# Step 2: Install Required Packages

Update the EC2 Instance
To ensure that your instance has the latest security patches and updates, it’s recommended to update the system before installing any software.


```bash
sudo yum update -y

```


![run3](https://github.com/user-attachments/assets/b1ddec7e-014e-4d82-877c-eb147bbfbc5f)

Install the CloudWatch Agent
The Amazon CloudWatch Agent is needed to collect CPU, memory, and disk usage metrics from your EC2 instance and send them to Amazon CloudWatch for monitoring.

```bash
sudo yum install amazon-cloudwatch-agent -y


```
This command installs the CloudWatch agent and its dependencies. The CloudWatch agent will be used to collect and send system metrics (e.g., CPU, memory, and disk usage) to CloudWatch.

![run4](https://github.com/user-attachments/assets/cce8ddef-a34d-4caf-8e89-4ba4ab251e3e)

Verify the Installation:

After the installation is complete, you can verify that the CloudWatch agent is installed by checking its version:

```bash
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent -version

```

![run5](https://github.com/user-attachments/assets/6bb45780-e686-4555-b51d-7634e7b766b9)

Install Shell Scripting Tools (Optional)
If you plan to create custom shell scripts for additional monitoring or automation, you might want to install some shell scripting utilities. While Amazon Linux comes with many tools pre-installed, you can install additional packages as needed.

```bash
sudo yum install vim -y
sudo yum install nano -y
sudo yum install htop -y
sudo yum install bc -y

```

![run6](https://github.com/user-attachments/assets/0e7bd284-a58e-48df-adba-96e25d5f4e15)

Confirm Installation of Optional Tools:

Check the version of any installed tools to confirm they are ready for u

```bash
vim --version
nano --version
htop --version
bc --version

```

![run7](https://github.com/user-attachments/assets/5898f8ab-d17f-465a-a5c4-f3b4d466f0e3)

# Step 3: Configure the CloudWatch Agent

Create a CloudWatch Agent Configuration File
AWS provides a built-in configuration wizard to help you generate a config.json file for the CloudWatch agent. This file defines what metrics the agent will collect and send to CloudWatch.

Run the CloudWatch Agent Configuration Wizard:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard


```

![run8](https://github.com/user-attachments/assets/eeb2991a-9c3a-4789-a3ca-beaa238cdb88)

Follow the Prompts:

The wizard will ask you a series of questions to determine which metrics to monitor and where to send the data. Answer the 

questions based on your needs:

Operating system: Choose Linux.

Metrics to collect: You can choose to collect CPU, memory, and disk usage metrics.

CloudWatch Log Group: Select a log group name or create a new one.

Destination: Choose EC2 for monitoring EC2 instances. 

you follow the read underline meand (default choose)

![run9](https://github.com/user-attachments/assets/08cadee9-ba50-4461-98c5-bfa52eea3d35)

![run10](https://github.com/user-attachments/assets/641af4cd-e1ea-4dbe-8b28-8df94bc0f55e)

![run11](https://github.com/user-attachments/assets/1203dc9b-c199-47b9-ac0d-ad93cd92af26)


Locate the Configuration File:


```bash
/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

```
you copy this command and paste lof file path 
and also paste you EC2 instnace id 


![run12](https://github.com/user-attachments/assets/8e4de982-9f87-44cf-bd8b-ce6819576221)


![run13](https://github.com/user-attachments/assets/0ee27fe1-6e07-4df3-af03-aa1e94555edf)

you only not mention X-reay Demon configuration you only press ENTER button

![run14](https://github.com/user-attachments/assets/427e05f7-f821-40cf-8f46-eb9ef72a7a77)

you not wirte [AmazonCloudWathc-linux] you press ENTER button,

you not write [ us-east-1] you press Ensure button,

you not menstion access key and access secret key , you only press button you move on space. 

![run15](https://github.com/user-attachments/assets/8b7d7a4d-4e92-4b17-b9d4-8421cdd24ac4)

 Start the CloudWatch Agent
Once the configuration file is ready, you can start the CloudWatch agent to begin collecting and sending data to CloudWatch.

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a start -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s

```
This command tells the agent to use the configuration file you created and start collecting metrics on your EC2 instance.

![run16](https://github.com/user-attachments/assets/07121390-5a95-47c8-9b3a-128e6c7989dc)

Verify the CloudWatch Agent is Running:

To check if the CloudWatch agent is running, use the following command:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status

```
The output should show the agent's status as (running) if everything is working correctly.

![run17](https://github.com/user-attachments/assets/22bd8a96-37a9-40d5-a1eb-1dbffa9e114a)

# Step 4: Set Up CloudWatch Alarms

Navigate to CloudWatch Alarms:

1. Go to the AWS Management Console.

In the search bar, type "CloudWatch" and click on CloudWatch to open the CloudWatch dashboard.

In the left-hand navigation pane, click on Alarms.

Click Create Alarm to start the process.

2. Select the Metric:

Click Select metric and follow the prompts to find the metrics you want to monitor.

\\\\For CPU Utilization:

In the Browse tab, select EC2 > Per-Instance Metrics > CPU Utilization.

Choose your EC2 instance from the list, then click Select metric.
Set the threshold value.

For example, set the alarm to trigger if CPU Utilization exceeds 80% for 5 minutes.

\\\\For Memory Usage:

Since memory usage is not natively available from EC2, use the custom metrics sent by the CloudWatch Agent.

Go to Browse > CWAgent > InstanceId > Memory > Usage.

Choose your instance and select the Memory Utilization metric.

Set a threshold (e.g., alert if memory usage exceeds 85%).

\\\\For Disk Usage:

Select the metrics for disk space sent by the CloudWatch agent.

Navigate to CWAgent > InstanceId > Disk > UsedPercent.

Set a threshold (e.g., alert if disk space usage exceeds 80%)
.
3. Configure Alarm Settings:

For each metric (CPU, Memory, Disk), set the following options:

Period: Set the period to 5 minutes (or any preferred interval).

Threshold type: Set the threshold for when the alarm should trigger (e.g., 80% CPU utilization).

![cw1](https://github.com/user-attachments/assets/1809e589-a25b-4148-b1a8-720b6d57656e)

![cw2](https://github.com/user-attachments/assets/3097e797-c0a2-4340-8b27-6dcc9ac48570)

![cw3](https://github.com/user-attachments/assets/21e45c9d-8c50-483a-9fc2-b5482a8141df)
 
 you write search on metric ( EC2, CPU, Utilization and copy instance id and paste)

![cw4](https://github.com/user-attachments/assets/4c184c7b-361f-4681-a5b6-3eac53b886f1)

![cw5](https://github.com/user-attachments/assets/a50af3ac-5e97-4051-923a-70d916fe6f85)

you cheak your CPU utilization becuase my utilization graph percentage is 11 then i am set 12 value

![cw6 0](https://github.com/user-attachments/assets/5ac9c4b2-23f3-4d90-9e64-80ed2efe0d61)

you set your value depend ghraph percentage and near set value 

![cw6](https://github.com/user-attachments/assets/e7b95ba4-319a-41d8-8cdb-abe35bad0159)

1st create SNS topic 

![snscw7](https://github.com/user-attachments/assets/a51c6034-ae6a-4414-8656-2dc6d0d3a653)

# Step 5: Configure Amazon SNS for Alerts

Amazon SNS (Simple Notification Service) allows you to send notifications to email addresses, phone numbers, or other endpoints when the alarm is triggered.

1. Navigate to SNS in the AWS Console:

In the AWS Management Console, type “SNS” in the search bar and click on Simple Notification Service (SNS).

2. Create a New SNS Topic:

Click on Topics from the left-hand menu, then click Create Topic.

Topic Type: Choose Standard (recommended for most use cases).

Name your topic (e.g., “EC2-Server-Health-Alerts”).

Click Create Topic.

3. Subscribe to the Topic:

After creating the SNS topic, you need to subscribe an endpoint (like your email or phone number) to receive notifications.

Click on Create subscription.

Topic ARN: Select the SNS topic you just created.

Protocol: Choose how you'd like to receive notifications.

 You can choose Email for simple notifications or SMS for text alerts.

Endpoint: Enter the email address or phone number where you want to receive alerts.

Click Create subscription.

4. Confirm the Subscription:

If you selected Email as the protocol, you’ll receive a confirmation email. Click the confirmation link to activate the subscription.


![sns1](https://github.com/user-attachments/assets/d0e462f8-31c0-4e84-862f-26766294cb77)

![sns2](https://github.com/user-attachments/assets/717d439d-74d5-4325-8e42-1d8b54280d08)

![sns3](https://github.com/user-attachments/assets/9dd0e78f-d670-4f4e-93d9-f96805359e89)

![sns4](https://github.com/user-attachments/assets/53e11c75-dd5e-42bc-b9a9-ca32897592de)

![sns5](https://github.com/user-attachments/assets/e37f5281-2299-463c-8242-983801a2cdc5)

![sns6](https://github.com/user-attachments/assets/09c40aff-f7b4-420d-919c-194b01da1079)

![sns7](https://github.com/user-attachments/assets/0b28b2c5-b487-4e14-9867-1dcb51664ac2)

![sns8](https://github.com/user-attachments/assets/70231a3b-3338-4000-a6bc-d607827d4f49)

set SNS topic in send a notification 

![snscw7](https://github.com/user-attachments/assets/a51c6034-ae6a-4414-8656-2dc6d0d3a653)

![snscw8](https://github.com/user-attachments/assets/444fe206-ad4d-425e-ae3a-85b4722324b3)

![snscw9](https://github.com/user-attachments/assets/fa01c11b-ddb6-4680-9bef-4aa2f199667a)

create alarm 

![snscw10](https://github.com/user-attachments/assets/18818d7c-5411-4279-b417-49c37ee57652)

click the alarm and watch monitor 

![snscw11](https://github.com/user-attachments/assets/220af22d-9308-4260-a6a6-19247477e6f0)


# Step 6: Automate Health Checks with Shell Scripting

Create a Monitoring Script

Create the Shell Script:

SSH into your EC2 instance if you're not already connected.

Create a new shell script file (e.g., monitor_health.sh)


```bash
sudo nano /monitor_health.sh

```

![r1](https://github.com/user-attachments/assets/bed6e53a-11c4-4b5c-9cf9-5c558272effa)

COPY SNS Topic ARN AND paste

![r2](https://github.com/user-attachments/assets/63eecf45-8322-4ffe-aae9-914336f9815a)

CPOY DISTK USAGE : dvetmpfs


![r3 0](https://github.com/user-attachments/assets/049e2512-ff12-44a1-9c2b-8575d55f89c8)

paste SNS ARn & Disk usage

Add the Following Code to the Script: This script will check memory and disk usage and send alerts via SNS if the thresholds are exceeded.

```bash
#!/bin/bash

# Check memory usage (in percentage)
MEMORY_USAGE=$(free -m | awk 'NR==2{printf "%.2f", $3*100/$2 }')

# Check disk usage (in percentage, replace /dev/nvme0n1p1 with the correct disk partition)
DISK_USAGE=$(df -h | grep '/dev/nvme0n1p1' | awk '{print $5}' | sed 's/%//')

# Define thresholds
MEMORY_THRESHOLD=80
DISK_THRESHOLD=90

# Send alerts if memory usage exceeds the threshold
if (( $(echo "$MEMORY_USAGE > $MEMORY_THRESHOLD" | bc -l) )); then
    aws sns publish --topic-arn arn:aws:sns:region:account-id:ServerHealthAlerts \
    --message "Memory usage is above threshold: $MEMORY_USAGE%"
fi

# Send alerts if disk usage exceeds the threshold
if [ "$DISK_USAGE" -gt "$DISK_THRESHOLD" ]; then
    aws sns publish --topic-arn arn:aws:sns:region:account-id:ServerHealthAlerts \
    --message "Disk usage is above threshold: $DISK_USAGE%"
fi

```
Modify the Script:

Replace /dev/nvme0n1p1 with the correct disk partition on your instance. You can use the df -h command to check your partition.

Replace arn:aws:sns:region:account-id:ServerHealthAlerts with your actual SNS Topic ARN (from Step 5).



![r4](https://github.com/user-attachments/assets/441b4cbf-e74d-41a4-8f6d-a74cf8645251)

Make the Script Executable:

Save and exit the file.

Make the script executable by running the following command

```bash
sudo chmod +x /path_to_your_script/monitor_health.sh

```

![r5](https://github.com/user-attachments/assets/a0f79d79-d2bf-47bd-bbee-afa0bbbf140c)

 Set Up a Cron Job for Regular Monitoring

 Install the cronie Package

```bash
sudo yum update -y
sudo yum install cronie -y
sudo systemctl start crond
sudo systemctl enable crond
sudo systemctl status crond


```

![r6](https://github.com/user-attachments/assets/aa336098-38fb-4d82-af8f-51a6e1e87946)

Edit the Crontab

```bash

crontab -e

```


![r7](https://github.com/user-attachments/assets/e6169b4c-726c-4fac-819c-4eb1eff0f636)

Add Your Cron Job: For example, if you want to run a script every 5 minutes, you would add this line to the crontab:

```bash

*/5 * * * * /monitor_health.sh


```


![r8](https://github.com/user-attachments/assets/2647f8b2-e21f-4851-8d11-8dbfc3e970ad)

Verify the Cron Job: To ensure that the cron job has been saved, you can list all your active cron jobs with:

```bash

crontab -l


```

![r9](https://github.com/user-attachments/assets/780a83a3-04f3-4317-8a35-243d96a3d254)

# 7 Trigger an Alarm

Simulate High CPU Usage
To create high CPU usage, you can use a tool like stress or run a CPU-intensive process.

Install the stress tool:

If stress is not installed, you can install it with

```bash
sudo yum install stress -y


```

![r10](https://github.com/user-attachments/assets/0a117bb8-8581-4dd8-a103-fbcbdae191b8)

Run stress to Simulate High CPU Usage:

Use the following command to simulate high CPU usage for 5 minutes:

```bash

stress --cpu 2 --timeout 300


```

![r11](https://github.com/user-attachments/assets/7fe61195-d239-4ec1-85ba-206eda6945a4)

your project is succesfully run but you wait 3min to 4 min beause result you see after 3min 

![congratulation run1](https://github.com/user-attachments/assets/016480c2-eb5d-43c6-b5ad-c82b4d98ed94)

 configuration you have notification

![conguratulation run 2](https://github.com/user-attachments/assets/fa429f8c-b159-492e-84de-2114d2c45d04)

you delete / terminated all service becuse work is completed










