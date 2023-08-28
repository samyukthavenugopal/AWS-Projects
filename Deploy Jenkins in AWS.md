# Deploy Jenkins on AWS EC2 in Windows
## Create EC2 Instance in AWS
1. Sign in to the AWS Account.
2. Head to Instance in the AWS Dashboard. [Reference](https://www.google.com/search?q=create+ec2+instance+2023&sca_esv=560305232&rlz=1C1CHBF_enIN962IN963&biw=1536&bih=707&tbm=vid&sxsrf=AB5stBgTOmnsWP7o6hjj5Br1p6bk1lzOPQ%3A1693043943986&ei=58zpZLDoO_qtseMPj4y2iAI&ved=0ahUKEwjw9_qWiPqAAxX6VmwGHQ-GDSEQ4dUDCA0&uact=5&oq=create+ec2+instance+2023&gs_lp=Eg1nd3Mtd2l6LXZpZGVvIhhjcmVhdGUgZWMyIGluc3RhbmNlIDIwMjMyBRAhGKABSMULUHlYzAlwAHgAkAEAmAHCAaABnweqAQMwLja4AQPIAQD4AQHCAgQQIxgnwgIFEAAYgATCAggQABiKBRiRAsICBxAAGIoFGEPCAggQIRgWGB4YHYgGAQ&sclient=gws-wiz-video#fpstate=ive&vld=cid:5aef662e,vid:0Gz-PUnEUF0)
3. Click Launch Instance and enter the required fields such as Instance name, type, OS etc..
   - I will be using the Ubuntu AMI, AWS free Tier instance and storage and leave the security fields default.
5. Create a new key pair and download the `.pem` file and launch the instance.
6. Once the Instance is launched select the instance and click connect.
7. Head on to SSH Client tab and copy the example ssh connection DNS. Example: `ssh -i "example.pem" ubuntu@ec2-1-123-456-789.ap-south-1.compute.amazonaws.com`
## Connect to EC2 Instance using SSH
*  Here i will be using **SSH Client** to connect the instance. Instance can also be connected via [CLI](https://www.youtube.com/watch?v=crNyDkR3ulU) or [Ec2 Instance Connect](https://www.google.com/search?sca_esv=560305232&rlz=1C1CHBF_enIN962IN963&hl=en&sxsrf=AB5stBjTRdOVbO2JSvE5SkP8NtsP7TgmGQ:1693044452664&q=ec2+instance+connect&tbm=vid&source=lnms&sa=X&ved=2ahUKEwj6l8KJivqAAxUDS2wGHdPfBCAQ0pQJegQIDRAB&biw=1536&bih=707&dpr=1.25#fpstate=ive&vld=cid:fdb639e3,vid:lxSNeF7BAII).
*  To create an ssh connection on windows we can either install external open source terminals like [PuTTy](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or [MobaXterm](https://mobaxterm.mobatek.net/download-home-edition.html) or install [OpenSSH Client](https://www.google.com/search?sca_esv=560314180&rlz=1C1CHBF_enIN962IN963&sxsrf=AB5stBgdC_oXMBEK1BtPLz6LvC4_sQxA9g:1693047155519&q=how+to+install+open+ssh+client+in+windows+10&tbm=vid&source=lnms&sa=X&ved=2ahUKEwiYnKuSlPqAAxVp-zgGHUL3Cf8Q0pQJegQIBxAB&biw=1536&bih=707&dpr=1.25#fpstate=ive&vld=cid:d32f8674,vid:g2I6en4Mdjo) under optional features in windows.
### Windows OpenSSH Client 
1. In this project i am using the PowerShell by installing the OpenSSH Client. [Reference](https://youtu.be/sJ3SJ-66-F8)
3. Now open Windows PowerShell and navigate the directory or folder where your .pem file is saved.
4. In my case it is saved in the downloads folder so i am navigating to downloads via `cd downloads/` command in the shell.
5. Once we are at the required path, we paste the copied ssh and click enter. It should launch ubuntu with the ec2 instance id. Example : `ubuntu@ip-123-45-67-890:~$ `
   - If any error appears type in  chmod 400 followed by your .pem file name. Example: `chmod 400 example.pem` and hit enter.
   - Now paste the previously copied ssh dns and the instance connection should be launched. 
   - If the error still persist check if the instance name and dns are correct.
   - If the error is displayed as *"Connection Timed Out"* go to **Firewall & network protection** on your pc and turn off the firewall networks.
   - Restart your pc and try connecting again.
### PuTTy Terminal
1. If you are using PuTTy, install and configure the installations and open PuTTy. [Reference](https://www.youtube.com/watch?v=jv-dgOfFN4o)
3. Go to your Ec2 instance and Copy the Public IPv4 address in the details tab below.
4. Paste the IPv4 address in Host Name(or IP address) field under sessions tab in putty.
5. Then head on to connection>ssh>Auth and navigate to private key file for aunthentication field in putty.
6. Browse your .pem key file. Then click Open in the bottom of the putty dialog box.
7. Click connect in the appeared pop up and your ec2 connection is launched.
## Deploy Jenkins on EC2
1. Now to Deploy Jenkins on your Ec2 we must install [Java JDK](https://www.oracle.com/in/java/technologies/downloads/). 
2. Once you install the java jdk get back to the Windows PowerShell or Putty Terminal and type `java --version` to check if java is installed.
3. Go to your browser and type [Jenkins install](https://www.jenkins.io/doc/book/installing/linux/), open the jenkins documentation and head to installing jenkins and click linux.
4. Copy the commands in either Long Term Support release or Weekly release else copy the code below and paste it in the PowerShell or Terminal.
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
6. It takes a couple of minutes to download and install.
7. Once jenkins in installed we can confirm it by typing `systemctl status jenkins`. It should be active and running.
### Set Port Range and Protocol
*  One final step for the instance to run is by configuring the port type. 
   - Default port range is `22` for ssh ec2 instance and `8080` for jenkins.
   - So we add the 8080 port in our instance for smooth running of our jenkins in ec2.
1. We get back to our AWS EC2 Instance and head to security tab and click on security groups.
2. Under the Inbound rules click on edit inbound rules and add rule.
3. In the type choose Custom TCP and in port type 8080 and save rules.
## Check Deployed Instance
*  Now get back to the Instance and under details tab click copy Public IPv4 DNS. On your Browser type `http://Copied Public IPv4 DNS:8080`
*  Unlock Jenkins is prompted in the Screen. To get the password, Copy the path displayed and paste in PowerShell or Terminal with `sudo cat/your copied path`.
*  Once you hit enter you will get the required password, copy and paste it and hit enter and now the jenkins console is accessable.
# You have Succssfully Deployed Jenkins on your Ec2 Instance.
![Deployed Jenkins Interface](https://github.com/samyukthavenugopal/AWS-Projects/assets/71750386/c67cb5d8-64ef-4653-8afe-5466e6f9fc82)

