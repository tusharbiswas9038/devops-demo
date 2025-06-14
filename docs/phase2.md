Phase 2 – Dockerization and Compose Setup

This document guides you through containerizing a three-tier web application and orchestrating it using Docker Compose. The stack will include:

Frontend – Simple React or static HTML app

API – Python Flask app

DB – PostgreSQL database

1. Application Structure

src/
├── frontend/        # React or static site (NGINX-based)
├── api/             # Python Flask API
└── db/              # PostgreSQL database init

2. API Service (Flask)

2.1. src/api/app.py

from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api")
def index():
    return jsonify({"message": "API running"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

2.2. src/api/requirements.txt

Flask==2.3.2

2.3. src/api/Dockerfile

FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]

3. Frontend (Static HTML + NGINX)

3.1. src/frontend/index.html

<!DOCTYPE html>
<html><body><h1>Hello from Frontend</h1></body></html>

3.2. src/frontend/Dockerfile

FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html

4. Database Setup (PostgreSQL)

4.1. src/db/init.sql

CREATE TABLE test (
  id SERIAL PRIMARY KEY,
  name TEXT
);

5. Docker Compose File

5.1. docker-compose.yml

version: "3.9"
services:
  frontend:
    build: ./src/frontend
    ports:
      - "8080:80"

  api:
    build: ./src/api
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: postgres:14
    restart: always
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: devops_demo
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./src/db/init.sql:/docker-entrypoint-initdb.d/init.sql

volumes:
  db_data:

6. Build and Run

6.1. Build the services:

docker-compose build

6.2. Run the stack:

docker-compose up -d

6.3. Test endpoints:

Frontend: http://localhost:8080

API: http://localhost:5000/api

DB: psql -h localhost -U user -d devops_demo

7. Git Commit

git add .
git commit -m "feat: dockerize frontend, api, db with compose"

8. Next Steps

Once verified locally, proceed to:

Phase 3: Jenkins setup with CI/CD pipelines

Phase 4: Kubernetes manifests (local K3s)

Phase 5: Ansible for provisioning tasks

End of Phase 2 – Dockerization Guide


