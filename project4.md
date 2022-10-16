# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
Mean Stack is a combination of Mongodb (Document Database), Express (Backend Application Framework), Angular (Frontend Application Framework) and Nodejs (Javascript Runtime Environment).
In this project, I am going to implement a simple Book Register web form using MEAN stack.
## Prerequiste
- Launch an Ec2 instance on AWS

## Step 1: Installing Nodejs
- Update and Upgrade Ubuntu

  `sudo apt update`
  `sudo apt upgrade`
- Add certificates

  `sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`
  
  `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
- Install Nodejs

  `sudo apt install -y nodejs`
