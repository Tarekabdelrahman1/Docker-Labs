# Lab 3: Multi-Container Deployment with Docker Compose


## Section 1: Environment Configuration (.env)

Using an `.env` file is a best practice to keep sensitive credentials out of the main compose file. This file contains all the necessary variables for both the database and the application.

### File: `.env`
```text
# --- MySQL Database Configuration ---
# Root password for full database administrative access
MYSQL_ROOT_PASSWORD=1234
# The specific database name to be created on startup
MYSQL_DATABASE=wordpress_db
# Non-root user for WordPress to use
MYSQL_USER=tarek_admin
# Password for the non-root user
MYSQL_PASSWORD=2002

# --- WordPress Configuration ---
# The hostname 'db' matches the service name in docker-compose.yml
WORDPRESS_DB_HOST=db:3306
WORDPRESS_DB_USER=tarek_admin
WORDPRESS_DB_PASSWORD=2002
WORDPRESS_DB_NAME=wordpress_db
```
## Section 2: Docker Compose Infrastructure
The docker-compose.yml file defines two services: db (MySQL 5.7) and wordpress (latest). It uses a storage volume to map the host directory to the container storage.
File: **docker-compose.yml**
```yaml
version: '3.8'

services:
  db:
    image: mysql:5.7
    container_name: wordpress_db
    restart: always
    env_file:
      - .env
    volumes:
      # Mapping the host directory to the MySQL data path
      - ./mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wordpress_app
    restart: always
    depends_on:
      - db
    ports:
      # Exposing WordPress on host port 8080
      - "8080:80"
    env_file:
      - .env

volumes:
  mysql_data:
```
## Section 3: Testing and Verification
Follow these steps to deploy and test the lab environment.

### 1. Launch the Services
Run the following command in the directory containing your files:
```bash
docker compose up -d
```
<img width="1228" height="138" alt="image" src="https://github.com/user-attachments/assets/053c357f-4416-4b67-91a7-62cdc859f274" />

### 2. Verify Container Status
Check if both containers are up and running:
```Bash
docker compose ps
```
<img width="1242" height="128" alt="image" src="https://github.com/user-attachments/assets/6487b78c-d9f7-4f60-aaf7-8f08a09668a7" />

### 3. Test Web Access
Open your browser and navigate to:

```Bash
http://localhost:8080
```
<img width="1711" height="818" alt="image" src="https://github.com/user-attachments/assets/1c044f1b-f2d7-44a0-a856-88e8e38c9d95" />







