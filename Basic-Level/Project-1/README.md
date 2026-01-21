# This is projectwala/Basic-Level/Project-1 README.md file
<img width="330" height="128" alt="image" src="https://github.com/user-attachments/assets/6a07b62d-d849-4daf-848b-eb731d100aea" />


## Project 1: Basic Web Server Deployment (Level 1)

This project is part of my DevOps learning journey.  
The goal of this level is to understand how a **basic web application is deployed** in two ways:

- Directly on a Linux server
- Inside a Docker container

This project builds the foundation for future containerized, CI/CD, and Kubernetes-based deployments.

---

## Project Objective

- Understand how a web server works on a Linux system
- Learn service management using `systemctl`
- Get hands-on experience with Docker
- Compare traditional deployment vs container-based deployment

---

## Tech Stack Used

- Linux (RHEL-based)
- Apache HTTP Server (`httpd`)
- Docker
- Shell scripting

---

## Part 1: Web Server Deployment on a Single Linux Instance

### Step 1: Install Apache Web Server

```bash
yum install -y httpd
````

---

### Step 2: Start the Apache Service

```bash
systemctl start httpd.service
```

---

### Step 3: Enable Apache Service on Boot

```bash
systemctl enable httpd.service
```

---

### Step 4: Create a Simple Web Page

```bash
echo "Hello World!" > /var/www/html/index.html
```

---

### Output

* Apache runs directly on the Linux host
* Web application is accessible via:

```text
http://<server-ip>:80
```
<img width="304" height="225" alt="image" src="https://github.com/user-attachments/assets/42d02962-a43d-4372-b0c3-4bb44ca4b562" />

---

## Part 2: Web Server Deployment Using Docker Container

### Step 1: Install Docker

Docker was installed on **RHEL 9** using the official Docker documentation.

> Reference: [https://docs.docker.com/engine/install/rhel/](https://docs.docker.com/engine/install/rhel/)

---

### Step 2: Create Application Files

#### `index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Basic Web Server</title>
  </head>
  <body>
    <h1>Hello World!</h1>
  </body>
</html>
```

---

### Step 3: Create Dockerfile

```Dockerfile
FROM httpd:latest
COPY index.html /usr/local/apache2/htdocs/
```
<img width="511" height="81" alt="image" src="https://github.com/user-attachments/assets/d1e4bf0c-b05b-45b1-a4e5-3110718f6d17" />

---

### Step 4: Build Docker Image

```bash
docker build -t basic-webserver .
```
<img width="1319" height="384" alt="image" src="https://github.com/user-attachments/assets/aeef2410-238e-4aec-a480-d19c4508a487" />

<img width="642" height="127" alt="image" src="https://github.com/user-attachments/assets/5fd9a487-ac00-4f71-bfdc-8b45855ac582" />

---

### Step 5: Run the Docker Container

```bash
docker run -d -p 8080:80 basic-webserver
```
<img width="1317" height="255" alt="image" src="https://github.com/user-attachments/assets/6737feaf-569d-4b0d-802b-1994f494d7de" />
<img width="348" height="89" alt="image" src="https://github.com/user-attachments/assets/f0394cd0-742c-4cf2-ac56-76542c3da032" />

---

### Explanation

* Container port: `80`
* Host port: `8080`
* Application accessible via:

```text
http://<host-ip>:8080
```

---

## Key Learning Outcomes

* Difference between host-based and container-based deployments
* How Apache serves static content
* Basics of Dockerfile creation
* Docker image build and container runtime
* Port mapping between host and container
* Why containers improve portability and consistency

---

## Common Issues and Troubleshooting

* **Apache not starting**

  * Checked service status using:

    ```bash
    systemctl status httpd
    ```

* **Application not accessible**

  * Verified firewall rules
  * Checked Docker port mapping

* **Content not loading**

  * Verified correct document root path

---

## Project Level

* **Level:** Beginner (Level 1)
* **Focus:** Linux fundamentals, Apache, Docker basics

---

## Author

**Rakesh**
DevOps Learner | Hands-on Practitioner

This project is part of a structured DevOps roadmap where each level builds on the previous one.
