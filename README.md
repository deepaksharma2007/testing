# testing

Step-1 SSH should allow in ec2 sg

Step-2 install git in ec2 machine 

`$ yum install git -y`

Step-3 Add secret in github , so github action can connect to our ec2 instance 

repo settings  --> Secrets & variable --> Action --> New repository secret 

We need to add three secret :-
1. IP of instance
2. User of ec2 instance
3. Private key of ec2 instance to authenticate 
![image](https://github.com/user-attachments/assets/175a7011-3e27-41f2-a9f6-b3ef1844c1b3)
