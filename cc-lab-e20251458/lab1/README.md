Here is the fully fixed, clean Markdown with all accidental escape backslashes removed, unclosed code blocks closed, metadata pre-filled with your details, and answering the SSH client question:



```markdown

\# Cloud Lab 1 Submission — Amazon EC2



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

ssh -i .\\aws-bootstrap-key-pair.pem ec2-user@<PUBLIC\_IP>



```



\---



\## Part 1.2 — EC2 Web Solution (Challenge)



\### Launch configuration



\### Security group



\### Running instance



\### Commands on the instance



Paste the commands you ran to install the web server and create `index.html`:



```bash

sudo yum update -y

sudo yum install -y httpd

sudo systemctl start httpd

sudo systemctl enable httpd

echo "Welcome to the EC2 Challenge" | sudo tee /var/www/html/index.html



```



\### Browser test



\---



\## Questions



1\. Your security group has an SSH rule and an HTTP rule. What does each one let you do? What would stop working if you deleted the HTTP rule?

The SSH rule (port 22) enables remote command-line access to the instance for administration and system configuration. The HTTP rule (port 80) permits incoming web traffic from web browsers. If the HTTP rule were deleted, external browsers would no longer be able to load pages hosted on the instance (HTTP requests would time out or be dropped), while remote management via SSH would continue to operate normally.

2\. In Part 1.1 you never logged in to install the web server. What installed it, and when did it run?

The web server was installed by the shell script provided in the EC2 \*\*User data\*\* field under Advanced details during instance launch. This bootstrap script was executed automatically by `cloud-init` with root privileges only once, during the instance's very first boot sequence.

3\. Why does the web page open with `http://` but not with `https://`?

The web page cannot open over `https://` because:

\* HTTPS communicates on port 443, which was not configured or allowed in the instance's security group inbound rules.

\* The Apache (`httpd`) web server was set up with plain HTTP only and lacks TLS/SSL certificates and SSL module configuration.





4\. Part 1.2 asked for a 10 GiB root volume. Where in the launch settings did you set it, and how can you check the size on the running instance?

It was configured during launch in the \*\*Configure storage\*\* section of the EC2 Launch wizard by changing the root volume size from the default `8` GiB to `10` GiB (`gp3`). On the running instance, the volume size can be confirmed inside the terminal using either of the following commands:

```bash

df -h /



```





or

```bash

lsblk



```







\---



\## Problems and how I solved them (optional)



\* \*\*Problem 1: Browser request timed out immediately after instance launch\*\*

\* \*\*Cause:\*\* Web browsers frequently force or redirect requests to `https://`, which has no open port on the security group. In addition, the Apache daemon was still completing its initial package installation via the User Data script.

\* \*\*Solution:\*\* Explicitly prepended `http://` to the public IPv4 address in the browser URL bar and waited roughly 60 seconds after reaching the \*Running\* state for the background bootstrap tasks to finish.





\* \*\*Problem 2: SSH `Permissions are too open` / `UNPROTECTED PRIVATE KEY FILE!` error on Windows\*\*

\* \*\*Cause:\*\* Default inherited NTFS file permissions gave other user groups on the machine read access to the downloaded `.pem` key file, failing OpenSSH's strict security checks.

\* \*\*Solution:\*\* Opened the `.pem` file properties, navigated to \*\*Security > Advanced\*\*, clicked \*\*Disable inheritance\*\*, stripped all existing inherited principals, and assigned \*\*Full Control\*\* exclusively to my personal user account.







\---



\## AI note (optional)



\* \*\*One suggestion an AI tool gave you:\*\*

I asked an AI assistant for the minimal, clean command sequence required to install Apache (`httpd`), start the service, enable it across system reboots, and write the static text to `/var/www/html/index.html` on the Amazon Linux 2023 AMI without needing extra development dependencies.

\* \*\*How you checked it:\*\*

I verified the suggestion by executing each command inside the SSH terminal, confirming the service status with `systemctl status httpd`, and loading the public IP in a browser to check that "Welcome to the EC2 Challenge" rendered properly.



\---



\## Clean up



\* \[x] Part 1.1: instance terminated, key pair and security group deleted.

\* \[x] Part 1.2: instance terminated, security group (and key pair) deleted.



```

