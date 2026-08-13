# Dcoker-project-3

```markdown
# Project 3: Multi-Container Web Application with Docker Compose (Flask + Redis)

A hands-on DevOps project demonstrating container orchestration, multi-service networking, and stateful caching using Docker Compose, Python Flask, and Redis.

## 🚀 Overview
Moving beyond single containers, this project provisions a multi-tier application stack. It connects a lightweight Python Flask web server with an in-memory Redis caching database, enabling them to communicate seamlessly within an isolated Docker network managed by a single orchestration file.

## 🛠️ Tech Stack & Architecture
* **Orchestration:** Docker Compose
* **Containerization:** Docker
* **Backend Framework:** Python (Flask)
* **Caching Database:** Redis (Alpine)
* **OS:** Ubuntu (Host)

---

## 📂 Project Structure
Your project directory contains the following core files:
```text
project3/
├── app.py
├── requirements.txt
├── Dockerfile
└── docker-compose.yml

```

---

## ⚙️ Configuration Files

### 1. `docker-compose.yml`

Orchestrates the `web` and `redis` services, handling port mappings, environment variables, and volumes:

```yaml
services:
  web:
    build: .
    ports:
      - "9000:5000"
    volumes:
      - .:/app
    environment:
      FLASK_DEBUG: "true"

  redis:
    image: "redis:alpine"

```

### 2. `Dockerfile`

Builds the custom Python environment for the Flask application:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
CMD [ "flask", "run" ]

```

### 3. `app.py` & `requirements.txt`

* **`requirements.txt`**: Contains dependencies (`flask` and `redis`).
* **`app.py`**: A Python script tracking visitor hit counts with a built-in connection retry loop:

```python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello From Third Project, I have been seen {} times.\n'.format(count)

```

---

## 🏃‍♂️ Getting Started & Execution

1. Make sure you have Docker and Docker Compose installed.
2. Open your terminal inside the project directory and run the stack with a single command:
```bash
docker-compose up --build

```


3. Open your browser and navigate to:
```text
http://localhost:9000

```


*(Refreshing the page will dynamically increment the visitor count stored in Redis).*

---

## 📦 Series Context

This is **Project 3 of 3** in your containerization hands-on mini-series:

* **Project 1:** Deploying a Simple HTML Web Page Using Nginx (Bind Mounts)
* **Project 2:** Custom Nginx Image & Docker Hub Publishing
* **Project 3:** Multi-Container Deployment Using Docker Compose (Flask + Redis) *(Current)*
