\# Cloud Lab 1 Submission — Amazon EC2



Copy this template to `cc-lab-YOUR\_ID/lab1/README.md` in your own repository. Follow \[the instructions](lab1-instruction.md). Put your screenshots in `lab1/images/` with the file names below.



\- \*\*Student name:\*\* Vireak La

\- \*\*Student ID:\*\* e20251458

\- \*\*Learner Lab user name:\*\* voclabs/user5622973=LA\_VIREAK

\- \*\*Date completed:\*\* 2026-10-23



\---



\## Part 1.1 — An Amazon EC2 Walkthrough



\### Security group



!\[Security group inbound rules](images/01-security-group.png)



\### Key pair



!\[Key pair](images/02-key-pair.png)



\### Instance details



!\[Instance details](images/03-instance-details.png)



\### Browser test



!\[Browser test](images/04-browser-test.png)



\### SSH connection



!\[SSH connection](images/05-ssh-connection.png)



Did you connect with your own SSH client or with EC2 Instance Connect?



I connected using my own SSH client from Windows PowerShell using the downloaded private key:

```powershell

ssh -i .\\aws-bootstrap-key-pair.pem ec2-user@172.31.93.78



\## Part 1.2 — EC2 Web Solution (Challenge)



\### Launch configuration



!\[Launch configuration](images/06-challenge-launch.png)



\### Security group



!\[Challenge security group](images/07-challenge-security-group.png)



\### Running instance



!\[Challenge instance](images/08-challenge-instance.png)



\### Commands on the instance



!\[Terminal commands](images/09-challenge-terminal.png)



Paste the commands you ran to install the web server and create `index.html`:



```bash

sudo yum update -y

sudo yum install -y httpd

sudo systemctl start httpd

sudo systemctl enable httpd

echo "Welcome to the EC2 Challenge" | sudo tee /var/www/html/index.html



\## Questions



1\. Your security group has an SSH rule and an HTTP rule. What does each one let you do? What would stop working if you deleted the HTTP rule?



&#x20;  The SSH rule (port 22) enables remote command-line access to the instance for administration and system configuration. The HTTP rule (port 80) permits incoming web traffic from web browsers. If the HTTP rule were deleted, external browsers would no longer be able to load pages hosted on the instance (HTTP requests would time out or be dropped), while remote management via SSH would continue to operate normally.



2\. In Part 1.1 you never logged in to install the web server. What installed it, and when did it run?



&#x20;  The web server was installed by the shell script provided in the EC2 \*\*User data\*\* field under Advanced details during instance launch. This bootstrap script was executed automatically by `cloud-init` with root privileges only once, during the instance's very first boot sequence.



3\. Why does the web page open with `http://` but not with `https://`?



&#x20;  The web page cannot open over `https://` because:

&#x20;  \* HTTPS communicates on port 443, which was not configured or allowed in the instance's security group inbound rules.

&#x20;  \* The Apache (`httpd`) web server was set up with plain HTTP only and lacks TLS/SSL certificates and SSL module configuration.



4\. Part 1.2 asked for a 10 GiB root volume. Where in the launch settings did you set it, and how can you check the size on the running instance?



&#x20;  It was configured during launch in the \*\*Configure storage\*\* section of the EC2 Launch wizard by changing the root volume size from the default `8` GiB to `10` GiB (`gp3`). On the running instance, the volume size can be confirmed inside the terminal using either of the following commands:

&#x20;  ```bash

&#x20;  df -h /



\## Problems and how I solved them (optional)



\* \*\*Problem 1: Browser request timed out immediately after instance launch\*\*

&#x20; \* \*\*Cause:\*\* Web browsers frequently force or redirect requests to `https://`, which has no open port on the security group. In addition, the Apache daemon was still completing its initial package installation via the User Data script.

&#x20; \* \*\*Solution:\*\* Explicitly prepended `http://` to the public IPv4 address in the browser URL bar and waited roughly 60 seconds after reaching the \*Running\* state for the background bootstrap tasks to finish.



\* \*\*Problem 2: SSH `Permissions are too open` / `UNPROTECTED PRIVATE KEY FILE!` error on Windows\*\*

&#x20; \* \*\*Cause:\*\* Default inherited NTFS file permissions gave other user groups on the machine read access to the downloaded `.pem` key file, failing OpenSSH's strict security checks.

&#x20; \* \*\*Solution:\*\* Opened the `.pem` file properties, navigated to \*\*Security > Advanced\*\*, clicked \*\*Disable inheritance\*\*, stripped all existing inherited principals, and assigned \*\*Full Control\*\* exclusively to my personal user account.



\## AI note (optional)



\* \*\*One suggestion an AI tool gave you:\*\*  

&#x20; I asked an AI assistant for the minimal, clean command sequence required to install Apache (`httpd`), start the service, enable it across system reboots, and write the static text to `/var/www/html/index.html` on the Amazon Linux 2023 AMI without needing extra development dependencies.



\* \*\*How you checked it:\*\*  

&#x20; I verified the suggestion by executing each command inside the SSH terminal, confirming the service status with `systemctl status httpd`, and loading the public IP in a browser to check that "Welcome to the EC2 Challenge" rendered properly.



\## Clean up



\- \[x] Part 1.1: instance terminated, key pair and security group deleted.

\- \[x] Part 1.2: instance terminated, security group (and key pair) deleted.

