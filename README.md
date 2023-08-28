# Jenkins Installation and Secure Access with Nginx and Certbot

This guide provides step-by-step instructions for setting up Jenkins on an Ubuntu EC2 instance, configuring Nginx as a reverse proxy, and securing your Jenkins instance with an SSL certificate using Certbot.

## Prerequisites

- Ubuntu EC2 instance running on AWS
- A registered domain name (e.g., `jenkins.example.com`)
- AWS Route 53 for DNS management
- Hostinger account for domain registration [we can domain with low cost]

## Installation Steps

1. **Launch Ubuntu EC2 Instance:**

   Launch an Ubuntu EC2 instance on AWS and ensure you have SSH access.

2. **Install Jenkins and Java:**

   Run the provided script to install Jenkins and Java on your EC2 instance.

   chmod +x nginx_install_script.sh

   ./nginx_install_script.sh

## Install Nginx:

Install Nginx using the following script.

chmod +x nginx_install_script.sh

./nginx_install_script.sh

## Configure Nginx as Reverse Proxy:


sudo vi /etc/nginx/sites-available/jenkins.pavan-devops.cloud

Edit the Nginx configuration file to set up reverse proxy for Jenkins. Replace jenkins.pavan-devops.cloud with your domain name

upstream jenkins{
    server 127.0.0.1:8080;
}

server{
    listen      80;
    server_name jenkins.pavan-devops.cloud;  //in server_name replace dns name with yours//

    access_log  /var/log/nginx/jenkins.access.log;
    error_log   /var/log/nginx/jenkins.error.log;

    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass  http://jenkins;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        proxy_redirect off;

        proxy_set_header    Host            $host;
        proxy_set_header    X-Real-IP       $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto https;
    }

}



## Create a Symbolic Link to Enable the Configuration:

Run the following command to create a symbolic link from the configuration file in the sites-available directory to the sites-enabled directory:


sudo ln -s /etc/nginx/sites-available/jenkins.pavan-devops.cloud /etc/nginx/sites-enabled/

Replace jenkins.pavan-devops.cloud with your actual domain name.

## Test Nginx Configuration for Syntax Errors:

Verify that there are no syntax errors in your Nginx configuration files by running the following command:

sudo nginx -t

## If there are no issues, the command will confirm that the configuration is valid.

Restart Nginx to Apply Changes:

## To apply the changes you've made to the Nginx configuration, restart the Nginx service:

sudo systemctl restart nginx
This will activate the updated Nginx configuration.

## Test Access to Jenkins via Your Domain:

Open a web browser and navigate to http://your_domain. This should redirect you to your Jenkins instance. However, since you've secured your Jenkins instance with Certbot, you should use https instead of http.


## Secure Jenkins with Certbot:

you can use the command:

sudo certbot --nginx -d jenkins.pavan-devops.cloud

This command tells Certbot to use the Nginx plugin and obtain a certificate for your Jenkins domain.

Remember to replace placeholders like jenkins.pavan-devops.cloud with your actual domain name and customize commands as needed based on your environment.

After completing these steps, your Jenkins instance should be accessible securely via https://jenkins.pavan-devops.cloud.

