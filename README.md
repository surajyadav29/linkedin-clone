# LinkedIn Clone – Full Stack Application

This project is a LinkedIn-style full stack web application built to demonstrate real-world application development and deployment on AWS EC2.  
It covers backend API development, frontend static hosting, and production deployment using industry-standard tools.

---

## Project Overview

The application consists of two main parts:

Backend: REST API built using Flask  
Frontend: Static web application served via Nginx  

The backend handles authentication, user data, and file uploads, while the frontend interacts with the backend APIs.

---

## Technology Stack

Backend  
Python  
Flask  
Gunicorn  
Supervisor  
SQLite  

Frontend  
HTML  
CSS  
JavaScript  
Nginx (static hosting)  

Infrastructure  
AWS EC2 (Ubuntu)  
Nginx (reverse proxy)  
Git and GitHub  

---

## Application Architecture Flow

User Browser  
Nginx (Port 80)  
Gunicorn (Port 8000)  
Flask Backend API  

Frontend files are served directly by Nginx.  
API requests are forwarded to the backend via Gunicorn.

---

## Project Structure

linkedin-clone-backend/

app.py – Main Flask application  
models.py – Database models  
uploads/ – Uploaded files  
venv/ – Python virtual environment (ignored in Git)  
.gitignore  
README.md  

linkedin-clone-frontend/

index.html – Main frontend file  
css/ – Stylesheets  
js/ – JavaScript files  
assets/ – Images and static files  

---

## Backend Deployment on AWS EC2

Launch an Ubuntu EC2 instance and allow ports 22 and 80.

Connect to EC2:

ssh -i linkedin-key.pem ubuntu@EC2_PUBLIC_DNS

Install required packages:

sudo apt update  
sudo apt install python3-pip python3-venv nginx supervisor -y  

Clone backend repository:

git clone https://github.com/your-username/linkedin-clone-backend.git  
cd linkedin-clone-backend  

Create virtual environment and install dependencies:

python3 -m venv venv  
source venv/bin/activate  
pip install flask gunicorn  

Test backend:

gunicorn -b 127.0.0.1:8000 app:app  

---

## Supervisor Configuration for Backend

Supervisor ensures the backend runs continuously.

Create configuration file:

sudo nano /etc/supervisor/conf.d/linkedin.conf

Example configuration:

[program:linkedin]  
directory=/home/ubuntu/linkedin-clone-backend  
command=/home/ubuntu/linkedin-clone-backend/venv/bin/gunicorn -b 127.0.0.1:8000 app:app  
autostart=true  
autorestart=true  
user=ubuntu  

Apply configuration:

sudo supervisorctl reread  
sudo supervisorctl update  
sudo supervisorctl start linkedin  

---

## Frontend Deployment on AWS EC2

Clone frontend repository:

cd ~  
git clone https://github.com/your-username/linkedin-clone-frontend.git  

Move frontend files to Nginx web directory:

sudo rm -rf /var/www/html/*  
sudo cp -r linkedin-clone-frontend/* /var/www/html/  

Set permissions:

sudo chown -R www-data:www-data /var/www/html  

Restart Nginx:

sudo systemctl restart nginx  

---

## Nginx Reverse Proxy Configuration

Create Nginx configuration file:

sudo nano /etc/nginx/sites-available/linkedin

Example configuration:

server {  
    listen 80;  
    server_name _;  

    location / {  
        root /var/www/html;  
        index index.html;  
        try_files $uri $uri/ =404;  
    }  

    location /api {  
        proxy_pass http://127.0.0.1:8000;  
        proxy_set_header Host $host;  
        proxy_set_header X-Real-IP $remote_addr;  
    }  
}

Enable configuration:

sudo ln -s /etc/nginx/sites-available/linkedin /etc/nginx/sites-enabled  
sudo nginx -t  
sudo systemctl restart nginx  

---

## Restart Application After EC2 Stop and Start

Connect to EC2:

ssh -i linkedin-key.pem ubuntu@EC2_PUBLIC_DNS

Check backend status:

sudo supervisorctl status

Start backend if stopped:

sudo supervisorctl start linkedin

Restart Nginx:

sudo systemctl restart nginx

Open application:

http://EC2_PUBLIC_DNS

---

## What I Learned From This Project

End-to-end deployment of a full stack application  
Backend process management using Supervisor  
Using Nginx for static frontend hosting and reverse proxy  
Handling application restarts after EC2 stop and start  
Practical AWS EC2 server management experience  

---

## Project Status

Backend running using Supervisor  
Frontend served using Nginx  
Application accessible via EC2 public IP

---
 
## Author

Ajay Yadav  

AWS and DevOps fresher who built and deployed this project on AWS EC2 to gain practical experience in application deployment, process management, and server configuration.  
This project demonstrates my ability to work with real deployment tools used in production environments.
