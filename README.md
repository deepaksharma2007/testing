# testing

Step-1 SSH should allow in ec2 sg

Step-2 install git in ec2 machine 

`$ yum install git -y`

Step-3 We need to generate classic token to pull a private repo inside ec2 machine 

Step-4 Add secret in github , so github action can connect to our ec2 instance 

repo settings  --> Secrets & variable --> Action --> New repository secret 

We need to add three secret :-
1. IP of instance ( we can use domain name , because IP might change on reboot )
2. User of ec2 instance
3. Private key of ec2 instance to authenticate 
![image](https://github.com/user-attachments/assets/175a7011-3e27-41f2-a9f6-b3ef1844c1b3)


Now as soon as we merge in master than our code is deployed in ec2 machine and restart apache service , so we can see latest deployemt


=====================================================================================================================================
# Adding httpd service monitoring by cloudwatch agent
### Step-1 Install agent 
$ yum install amazon-cloudwatch-agent -y 

### Step-2 Adding details of httpd to monitor
Edit your CloudWatch Agent configuration (usually in /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json) to include httpd as a procstat metric.
We need to add the highlighted part in receiver sections 
<img width="652" height="399" alt="image" src="https://github.com/user-attachments/assets/f89ece18-8195-40be-b9ad-2d3e4612e854" />

In pipeline section , we need to add highlighted part 
<img width="575" height="330" alt="image" src="https://github.com/user-attachments/assets/996035d9-d204-4d68-8180-3a108b7c3ff4" />

### Step-3 Restart cloudwatch agent service 
`$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a stop `
 `$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start`

This config collects process-level metrics every 60 seconds for processes matching httpd.

### Step-4 Verify metrics in cloudwatch
Go to CloudWatch Console → Metrics → CWAgent namespace → procstat and look for:
`procstat_pid_count` for httpd.

### Step-5: Create a CloudWatch Alarm
Go to CloudWatch → Alarms → Create Alarm -> Choose CWAgent / procstat / procstat_pid_count
Add a dimension filter: process name = httpd

*Condition:*
- Threshold type: Static
- Whenever procstat_pid_count is <= 0
- For at least 1 consecutive data point(s)

*Actions:*
- Choose or create an SNS topic to send email
- If creating a new topic:
 `Enter a name`
  `Add your email address`

You’ll receive a confirmation email – make sure to confirm it

Name your alarm, e.g., httpd-stopped-alarm

Create the alarm.

✅ Done!
Now, if your httpd service stops (i.e., no process found), procstat_pid_count becomes 0, and CloudWatch triggers an alarm and sends an email.
