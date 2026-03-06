# Docker Volumes Lab

## Problem 1 - Named Volumes

### Create Volumes
docker volume create html-volume
docker volume create nginx-config-volume

### Run First Container
docker run -d \
  --name my-nginx \
  -v html-volume:/usr/share/nginx/html \
  -v nginx-config-volume:/etc/nginx/conf.d \
  nginx

### Edit HTML Content
docker exec -it my-nginx bash
echo "<h1>Hello from my Nginx</h1>" > /usr/share/nginx/html/index.html
exit

### Remove Container
docker stop my-nginx
docker rm my-nginx

### Run 2 New Containers with Same Volumes
docker run -d \
  --name nginx-container-1 \
  -v html-volume:/usr/share/nginx/html \
  -v nginx-config-volume:/etc/nginx/conf.d \
  -p 8080:80 \
  nginx

docker run -d \
  --name nginx-container-2 \
  -v html-volume:/usr/share/nginx/html \
  -v nginx-config-volume:/etc/nginx/conf.d \
  -p 8081:80 \
  nginx

### Access from Browser
http://localhost:8080
http://localhost:8081

---

## Problem 2 - Bind Mounts

### Create Local Directories
mkdir -p ~/nginx-lab/html
mkdir -p ~/nginx-lab/config
echo "<h1>Hello from Bind Mount</h1>" > ~/nginx-lab/html/index.html

### Run First Container
docker run -d \
  --name nginx-bind-mount \
  -v ~/nginx-lab/html:/usr/share/nginx/html \
  -v ~/nginx-lab/config:/etc/nginx/conf.d \
  -p 8082:80 \
  nginx

### Remove Container
docker stop nginx-bind-mount
docker rm nginx-bind-mount

### Run New Container with Same Bind Mounts
docker run -d \
  --name nginx-bind-new \
  -v ~/nginx-lab/html:/usr/share/nginx/html \
  -v ~/nginx-lab/config:/etc/nginx/conf.d \
  -p 8083:80 \
  nginx

### Check Old Data in New Container
docker exec nginx-bind-new cat /usr/share/nginx/html/index.html

---

## Notes

- Named volumes are managed by Docker and stored in /var/lib/docker/volumes/
- Bind mounts use a path you define on your host machine
- Data in volumes and bind mounts persists after container removal
- The new container will have access to all data from the old container
