
- Networks: `netOne`, `netTwo`
- Containers:  
  - `nginxOne` (Nginx)  
  - `flaskapp` (Flask app)  
  - `mymariadb` (MariaDB)
- Flask image: `flask-image`

---

# Docker Networking Lab – Nginx, Flask, MariaDB


`app.py` file:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Flask inside Docker!\n"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

Example `Dockerfile`:

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000
CMD ["python", "app.py"]
```

Build:

```bash
docker build -t flask-image .
```

---

## Create the two networks

Create two user‑defined bridge networks:

```bash
docker network create netOne
docker network create netTwo
```

Verify:

```bash
docker network ls
```



**Purpose**

- `netOne`: will be used by `nginxOne` and `flaskapp`.
- `netTwo`: will be used by `flaskapp` and `mymariadb`.

``` bash
##  – Create the Nginx container in `netOne`

Run Nginx attached to `netOne`:


docker run -d  --name nginxOne --network netOne nginx:latest
```




```bash

## Create the Flask container on both networks

###  Start the Flask container on `netOne`


docker run -d --name flaskapp --network netOne flask-image
```

This starts the Flask development server listening on port `5000` inside the container.

### Connect Flask container to `netTwo` as well

```bash
docker network connect netTwo flaskapp
```



---

## Create the MariaDB container in `netTwo`

Run MariaDB attached only to `netTwo`:

```bash
docker run -d --name mymariadb --network netTwo \
-e MARIADB_ROOT_PASSWORD=rootpass \
  -e MARIADB_DATABASE=testdb \
  mariadb:latest
```




---

##  Connect to Flask from Nginx using IP:port

### Exec into the Nginx container

```bash
docker exec -it nginxOne bash
```


### Curl the Flask app from inside Nginx



```bash
curl http://flaskapp:5000/
```


response:

```text
Hello from Flask inside Docker!
```


---















### screenshots: 

![[Pasted image 20260308222255.png]]



![[Pasted image 20260308222309.png]]


![[Pasted image 20260308222948.png]]



![[Pasted image 20260308223024.png]]



![[Pasted image 20260308225148.png]]



