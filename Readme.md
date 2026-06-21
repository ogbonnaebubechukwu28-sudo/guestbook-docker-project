# Guestbook Application

## Overview

This is a simple Guestbook application built with Python Flask and Redis.

The application allows users to:

* Submit messages through a web interface
* Store messages in Redis
* View all submitted messages

The application consists of two components:

1. Flask Web Application
2. Redis Database

Your task is to containerize the application using Docker and deploy it using Docker Compose.

---

# Application Architecture

```text
User
  |
  v
Flask Web Application
  |
  v
Redis Database
```

The Flask application stores and retrieves messages from Redis.

---

# Project Structure

```text
guestbook-app/
│
├── app.py
├── requirements.txt
└── README.md
```

---

# Prerequisites

Install the following on your local machine:

* Python 3.10 or newer
* Redis Server
* Git

---

# Running the Application Locally

## Step 1: Clone the Repository

```bash
git clone <repository-url>
cd guestbook-app
```

---

## Step 2: Create a Virtual Environment

Linux/Mac:

```bash
python3 -m venv venv
source venv/bin/activate
```

Windows:

```bash
python -m venv venv
venv\Scripts\activate
```

---

## Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Step 4: Start Redis

Ensure Redis is running on your machine.

Linux:

```bash
sudo systemctl start redis
```

Verify:

```bash
redis-cli ping
```

Expected output:

```text
PONG
```

---

## Step 5: Run the Application

```bash
python app.py
```

Expected output:

```text
Running on http://0.0.0.0:5000
```

---

## Step 6: Access the Application

Open your browser and visit:

```text
http://localhost:5000
```

You should see:

* A message submission form
* Previously submitted messages

---

# Assignment Requirements

Using the provided source code:

1. Create a Dockerfile for the Flask application.
2. Build a Docker image.
3. Create a Docker Compose configuration.
4. Configure communication between the application and Redis containers.
5. Configure persistent storage for Redis using Docker Volumes.
6. Scan the image using Docker Scout.
7. Push the final image to Docker Hub.
8. Document your implementation in your own README.

---

# Expected Outcome

After containerization:

* The application should run using Docker Compose.
* Redis data should persist after container recreation.
* The application should communicate with Redis through a Docker network.
* The image should be available on Docker Hub.
* Docker Scout scan results should be documented.

Good luck!
