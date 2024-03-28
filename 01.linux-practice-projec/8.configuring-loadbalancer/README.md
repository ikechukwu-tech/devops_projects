# AUTOMATING LOADBALANCER CONFIGURATION WITH SHELL SCRIPTING


Streamline your load balancer configuration with ease using shell scripting and CI/CD on jenkins. this projects demonsrate how to automate the set up and maintainace of your load balancer using a frystle job, enhancing efficiency and reducing manual effort.



# AUTOMATE THE DEPLOYMWENT OF WEB SERVER


in the implementing loadbalancer with nginx coures, we deployed two back end servers with a load balancer distributing traffic accross the web servers, we did that by typing commands right on our terminal.  

in this course we will be authomating the entire process, we will do that by writing a shell script that when ran, all that we did manually will be run for us automatically. As DEVOPs engeneers, automation is at the heart of the work we do, it helps speed the deployment of service, and reduces the chance of making errors in our day to day activity.


 this course will give a great introduction  to automation.


 # DEPLOYING AND CONFIGURING WEBSERVERS

 All the processes we need to deploy our webserver has been codefiled in the shell script below :

 #!/bin/bash

####################################################################################################################
##### This automates the installation and configuring of apache webserver to listen on port 8000
##### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
######## ./install_configure_apache.sh 127.0.0.1
####################################################################################################################

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure

PUBLIC_IP=$1

[ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

sudo apt update -y &&  sudo apt install apache2 -y

sudo systemctl status apache2

if [[ $? -eq 0 ]]; then
    sudo chmod 777 /etc/apache2/ports.conf
    echo "Listen 8000" >> /etc/apache2/ports.conf
    sudo chmod 777 -R /etc/apache2/

    sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

fi
sudo chmod 777 -R /var/www/
echo "<!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: "${PUBLIC_IP}"</p>
        </body>
        </html>" > /var/www/html/index.html

sudo systemctl restart apache2




follow the steps below to run the script.

1. provision an EC2  instance runing ubuntu 20.04, you can refer to the course `implementing load balancer with nginx`  for a refresher

2. open port 8080 to allow trafic from anywhere using the security group, again refere to the course mentioned above for a refresher.

3. connect the web server via ternminal using ssh client

4. open a file, paste the script above, and close the file using the comand 
below : 

   ` sudo chmod +x install.sh`


close the file by clicking : `esc: wqa!`

5. change the permission on the file to make an executable using the command below. : `    sudo chmod +x install.sh`

6. run the shell script using the command below. make sure you read the instructions in the script to learn how to use it.

copy the code below :     `./install.sh PUBLIC_IP`



#  DEPLOYMENT OF NGINX AS A LOAD BALANCER USING SHELL SCRIPT


automate  the deployment of nginxas aload balancerusing shell script.


having successfully deployed and configured two web servers, we will move on to the load balancer, as a periquisite we need to provision an EC2 instance running ubuntu 22.04, open port 80 to anywhere  using the security group and connect to the loadbalancer via the terminal.



# DEPLOYING AND CONFIGURING NGINX LOAD BALANCER

 all the steps followed in the implementing of load balancer has been condified in the script below:


 read the instuction in the script below to learn how to use the script

 copy the code below : 
#!/bin/bash

######################################################################################################################
##### This automates the configuration of Nginx to act as a load balancer
##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
##### the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
##### ./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
#####  ./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
############################################################################################################# 

PUBLIC_IP=$1
firstWebserver=$2
secondWebserver=$3

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server  "${firstWebserver}"; # public IP and port for webserser 1
            server "${secondWebserver}"; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name "${PUBLIC_IP}";

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi

sudo nginx -t

sudo systemctl restart nginx

# STEPS TO RUN THE SHELL

1. on your terminal , open a file nginx.sh using the command below:
`sudo vi nginx.sh`

2. copy and paste the script inside the file

3. close the file using the command below :
type `esc the shift + :wqa!`

4. change the file permission to make it an executable using the code below: 
`sudo chmod +x nginx.sh`

5. run the script with the command below :`./nginx.sh PUBLIC_IP Webserver-1 Webserver-2`




# VERIFY THE SETUP

screen shot for for web server on web browser.










