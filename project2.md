# LEMP STACK IMPLEMENTATION
LEMP is an open-source web application stack used to develop web applications. The term LEMP is an acronym that represents L for the Linux Operating system, Nginx (pronounced as engine-x, hence the E in the acronym) web server, M for MySQL database, and P for PHP scripting language.

## Step 0 : Setting up the prerequisites
- Create a AWS account and Launch an EC2
- Connect to the EC2 cloud service provide with `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`

## Step 1: Installing the NGINX web server
- Start off by updating the web server's package index `sudo apt update`
- Install Nginx web server using : `sudo apt install nginx`
- Confirm if Nginx has been successfully installed `sudo systemctl status nginx`
  ![nginxInstalled](https://user-images.githubusercontent.com/26335055/194863587-cc52db2e-acd2-455d-b154-80b0fb4911af.png)


- After server has been installed succefully, you can access it locally using `curl http://127.0.0.1:80` which returns an output of strange formmated text.
- Test if the Nginx server can responds to request on the internet by opening a web browser and access with 'http://<Public-IP-Address>:80`
  ![nginx](https://user-images.githubusercontent.com/26335055/194863496-9e3a7ace-830c-410e-8a12-e6d298af7daf.png)
