# Django App with Docker

A simple Django web application containerized with Docker as part of the **SoftUni DevOps for Developers** 
The project demonstrates how to:

- build a Python/Django Docker image from a `Dockerfile`
- install the application's Python dependencies
- run Django inside a container
- expose Django on port `8000`
- verify the running container from the Docker CLI

## Project structure

```text
.
├── Dockerfile
├── manage.py
├── requirements.txt
└── example/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## Technologies

- Python 3.12
- Django 3.2.13
- Docker
- Docker BuildKit

## Dockerfile

The application uses Python 3.12, which is within the version range specified by the exam task.

```dockerfile
FROM python:3.12

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

### What the Dockerfile does

| Instruction | Purpose |
|---|---|
| `FROM python:3.12` | Uses Python 3.12 as the base image |
| `WORKDIR /app` | Sets the working directory inside the container |
| `COPY requirements.txt .` | Copies the Python dependency list |
| `RUN pip install --no-cache-dir -r requirements.txt` | Installs the required Python packages |
| `COPY . .` | Copies the Django project into the image |
| `EXPOSE 8000` | Documents the application port |
| `CMD [...]` | Starts the Django development server |

## Run locally with Docker

Open a terminal in the directory containing the `Dockerfile`.

### 1. Build the Docker image

```powershell
docker build --load -t django_image .
```

The `--load` option is useful when Docker is using the `docker-container` BuildKit driver. It loads the resulting image into the local Docker image store so that it can be used by `docker run`.

Verify the image:

```powershell
docker images
```

You should see:

```text
django_image   latest
```

### 2. Start the container

```powershell
docker run -d --name django_app -p 8000:8000 django_image
```

This creates a container named `django_app` and maps:

```text
Host:      8000
   ↓
Container: 8000
```

### 3. Verify that the container is running

```powershell
docker ps
```

You should see the container with a port mapping similar to:

```text
0.0.0.0:8000->8000/tcp
```

### 4. Check the Django logs

```powershell
docker logs django_app
```

A successful startup should contain a message indicating that Django's development server has started on:

```text
http://0.0.0.0:8000/
```

### 5. Open the application

Open:

**http://localhost:8000**

The supplied project may not define a URL for `/`, so a Django 404 response at the root does not necessarily indicate a Docker problem.

The project defines the Django admin URL, so you can also test:

**http://localhost:8000/admin/**

## Useful Docker commands

### List running containers

```powershell
docker ps
```

### List all containers, including stopped ones

```powershell
docker ps -a
```

### View application logs

```powershell
docker logs django_app
```

### Stop the container

```powershell
docker stop django_app
```

### Start the existing container again

```powershell
docker start django_app
```

### Remove the container

```powershell
docker rm django_app
```

### Remove the image

```powershell
docker rmi django_image
```

## Troubleshooting

### The container appears in Docker Desktop but is not running

First check its status:

```powershell
docker ps -a
```

Then inspect the logs:

```powershell
docker logs django_app
```

A container that immediately shows `Exited` has started and then terminated. The logs normally reveal the reason.

Do not immediately recreate the container or start it manually from Docker Desktop. Check the CLI logs first.

### Docker build warns that no output is specified

You may see:

```text
WARNING: No output specified with docker-container driver.
Build result will only remain in the build cache.
```

Use:

```powershell
docker build --load -t django_image .
```

The `--load` option makes the image available to the local Docker image store.

## Exam command sequence

The essential CLI sequence for this task is:

```powershell
docker build --load -t django_image .

docker images

docker run -d --name django_app -p 8000:8000 django_image

docker ps

docker logs django_app
```

## Learning objective

This exercise is a practical introduction to the basic Docker workflow for a Python web application:

```text
Django project
      │
      ▼
  Dockerfile
      │
      ▼
docker build
      │
      ▼
 Docker image
      │
      ▼
 docker run
      │
      ▼
Django container
      │
      ▼
localhost:8000
```
