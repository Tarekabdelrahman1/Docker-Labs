# Lab 2: Docker Builds, Limits, Networking, and Volumes
## PART 1
### 1) Create the Dockerfile
First, clone the target repository and create a **Dockerfile** inside it based on Ubuntu.
```bash
git clone https://github.com/meldafrawi/basic-flask-app.git
cd basic-flask-app
vim Dockerfile
```
<img width="761" height="191" alt="git clone" src="https://github.com/user-attachments/assets/966939f5-e22a-4195-8c10-f5f4e3f93b8f" />

**Dockerfile Content:**
```bash
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y python3 python3-pip
WORKDIR /app
COPY requirements.txt .
RUN pip3 install -r requirements.txt
COPY . .
CMD ["python3", "routes.py"]
```
### 2) Build the Docker Image
```bash
docker build -t tarekabdelrahman/iti-flask-lab2 .
```
<img width="1272" height="447" alt="2" src="https://github.com/user-attachments/assets/95ca78b3-60f4-4a12-9bc3-f26586087d6d" />

### 3) Run the Image with Constraints and Port Publishing
```bash
systemctl stop httpd.service
docker run -d --memory 100m --name flask-app -p 80:5000 tarekabdelrahman/iti-flask-lab2:latest
```
**Command Breakdown:**

**-d:** Runs the container in Detached mode (background).

**--name flask-app:** Assigns a custom name to the container.

**--memory="100m":** Sets a strict hard limit of 100MB of RAM using Linux Cgroups.

**-p 80:5000:** Maps port 5000 from inside the container to port 80 on the host machine.
<img width="1712" height="817" alt="output" src="https://github.com/user-attachments/assets/3f57735b-42b0-41c8-8d11-ea36640bb17a" />


### 4) Push the Image to Docker Hub
```bash
docker push tarekabdelrahman/iti-flask-lab2:latest .
```
<img width="852" height="221" alt="4" src="https://github.com/user-attachments/assets/00e50dee-d7f6-49d3-afa5-baf7a6d9af85" />

**Docker Hub repo:**
**https://hub.docker.com/repository/docker/tarekabdelrahman/iti-flask-lab2/general**


## PART 2
### 1) Create a Custom Bridge Network
```bash
docker network create --driver bridge --subnet 10.0.0.0/8 iti-network
```
### 2) Create a volume
```bash
docker volume create iti-volume
```
### 3) Run the Webserver Container
```bash
docker run -d --name webserver-iti --network iti-network -p 8080:8080 -v iti-volume:/usr/share/nginx/html nginx:alpine
```
### 5) Internal Container Configuration & Verification

To configure the custom index page and verify the Nginx server configuration from inside the container, an interactive shell session was used:

```bash
# 1. Access the container's shell
docker exec -it webserver-iti sh
```

# 2. Navigate to the Nginx HTML directory
```bash
cd /usr/share/nginx/html
```

# 3. Inject the required text into the index.html file
```bash
echo "<h1>Lab 2 ITI - Tarek Abdelrahman</h1>" > index.html
```
### 4) Edit Nginx Port Configuration using `vi`

To fulfill the requirement of publishing port 8080 from within the container
# 1. Access the container's shell
docker exec -it webserver-iti sh

# 2. Open the default Nginx configuration file
vi /etc/nginx/conf.d/default.conf

# 3. Inside vi, update the listen directives from 80 to 8080:
# server {
#     listen       8080;
#     listen  [::]:8080;
#     server_name  localhost;
#     ...

# 4. Reload Nginx to apply the new port configuration
```bash
nginx -s reload
```
# 5. Test the internal port (8080) using curl
```bash
curl -I http://localhost:8080
```
<img width="512" height="192" alt="image" src="https://github.com/user-attachments/assets/58c6a1c8-d089-4c2d-a5b8-09f77542f49c" />

