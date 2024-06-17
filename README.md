
# Instructions for Creating and Running a Flask Application with Docker and Nginx

This guide provides step-by-step commands to create and run a Flask application using Docker and Nginx.

## 1. Create the Project and Navigate to the Project Directory
```sh
mkdir my_flask_app
cd my_flask_app
```

## 2. Create a Virtual Environment (Optional)
```sh
python3 -m venv venv
source venv/bin/activate
```

## 3. Create the `requirements.txt` File
```sh
touch requirements.txt
```

## 4. Add Flask and Gunicorn to `requirements.txt`
```txt
flask
gunicorn
```

## 5. Install Dependencies (if using a virtual environment)
```sh
pip install -r requirements.txt
```

## 6. Create the Application File `app.py`
```sh
touch app.py
```

## 7. Edit `app.py` to Add a Simple Application Example
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run()
```

## 8. Create a Dockerfile
```sh
touch Dockerfile
```

## 9. Edit the Dockerfile
```Dockerfile
# Use the official Python image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy project files
COPY . .

# Install dependencies
RUN pip install -r requirements.txt

# Specify the command to run the application
CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:8000", "app:app"]
```

## 10. Create the Nginx Configuration File (`nginx.conf`)
```sh
touch nginx.conf
```

## 11. Edit `nginx.conf`
```nginx
server {
    listen 80;

    location / {
        proxy_pass http://web:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 12. Create the Docker Compose File (`docker-compose.yml`)
```sh
touch docker-compose.yml
```

## 13. Edit `docker-compose.yml`
```yaml
version: '3'

services:
  web:
    build: .
    container_name: flask_app
    expose:
      - "8000"
  nginx:
    image: nginx:latest
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web
```

## 14. Run Docker Compose
```sh
docker-compose up --build
```

## 15. Open the Project in VSCode (Optional)
```sh
code .
```

Now your Flask application will run in a Docker container with Nginx as a reverse proxy.
