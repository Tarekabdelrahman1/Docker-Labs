# Lab 3:Private Registry & Nginx Proxy
---

## 1) Private Registry & Nginx Proxy
```bash
docker tag tarekabdelrahman/iti-flask-lab2:latest 127.0.0.1:5000/flask-app:v1
docker push 127.0.0.1:5000/flask-app:v1
```
<img width="798" height="197" alt="image" src="https://github.com/user-attachments/assets/64b97bf3-11ca-4a82-a1d3-f9309e4f336e" />

## 2. Reverse Proxy Configuration (default.conf)
Nginx is configured to act as a Reverse Proxy, routing traffic from port 80 to the Flask backend.

```text
server {
    listen 80;

    location / {
        proxy_pass http://flask-app:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
## 3. Docker compose file
The stack pulls the image from the local registry and connects services via a dedicated bridge network.

```YAML
services:
  flask-app:
    image: 127.0.0.1:5000/flask-bonus:v1
    container_name: flask_backend
    restart: always
    ports:
      - "8080:5000"
    networks:
      - bonus_net

  proxy:
    image: nginx:alpine
    container_name: nginx_proxy
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - flask-app
    networks:
      - bonus_net

networks:
  bonus_net:
    driver: bridge
```
## 4)Testing & Verification
```bash
docker compose up -d
docker compose ps
```
<img width="1366" height="195" alt="image" src="https://github.com/user-attachments/assets/ba73563e-b609-4c75-b133-74cebb344cb8" />

**Verification**

```text
http://localhost:80
```
<img width="1916" height="833" alt="image" src="https://github.com/user-attachments/assets/0270eda2-2f2e-4dae-955d-37112ed0142b" />
