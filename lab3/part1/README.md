# Lab 3
## PART 1
### 1) Configure Docker for Insecure Registry
By default, Docker requires HTTPS for registries. To use a local registry over HTTP, the Docker daemon must be configured to trust the local endpoint.
```bash
sudo vim /etc/docker/daemon.json
```
```json
{
  "insecure-registries": ["127.0.0.1:5000"]
}
```
```bash
sudo systemctl restart docker
```
### 2) Run the CNCF Distribution Registry
deploy a private registry container with persistent storage to ensure images are not lost if the container is removed.
```bash
# Create a directory for persistent storage
mkdir -p ~/docker-registry-data
```
# Run the registry container
```bash
docker run -d -p 5000:5000 --name local-registry -v ~/docker-registry-data:/var/lib/registry registry:2
```
### 3) Create the Dockerfile
```Dockerfile
FROM alpine:latest
RUN apk update && apk add nginx
RUN mkdir -p /run/nginx
CMD ["nginx", "-g", "daemon off;"]                                
```
**Build the image**
```bash
docker build --network=host -t my-alpine-nginx:v1 .
```
### 4) Pushing to Private Registry
  #### 1) Tagging the Image
   To push an image to a private registry, it must be tagged with the registry's network address.
   ```bash
   docker tag my-alpine-nginx:v1 127.0.0.1:5000/my-alpine-nginx:v1
   ```
  #### 2) Pushing the Image
```bash
docker push 127.0.0.1:5000/my-alpine-nginx:v1
```
<img width="828" height="142" alt="image" src="https://github.com/user-attachments/assets/687a14d7-e437-4994-b92c-016f7f21c4a1" />

  #### 3) Verification via API
We can verify the image exists in the registry using a simple curl command to the Registry V2 API.

```Bash
curl http://127.0.0.1:5000/v2/_catalog
```
<img width="395" height="67" alt="image" src="https://github.com/user-attachments/assets/44fb62ff-c021-4bd4-af52-3c54cdbd1308" />












