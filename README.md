# Flask + MySQL Docker Network

A two-tier web application built using **Flask, MySQL, and Docker**. The Flask application and MySQL database run in separate containers connected through a custom Docker bridge network. A Docker named volume provides persistent database storage.

## 🚀 Project Overview

This project demonstrates containerization, Docker networking, database connectivity, and persistent storage using Docker CLI commands.

### Architecture

```text
                 Web Browser
              http://localhost:5000
                       |
                       v
              +------------------+
              | Flask Container  |
              |   Port: 5000     |
              +------------------+
                       |
                       | Custom Bridge Network
                       |
                       v
              +------------------+
              | MySQL Container  |
              |   Port: 3306     |
              +------------------+
                       |
                       v
              +------------------+
              | Docker Volume    |
              |   mysql_data     |
              +------------------+
```

## 🛠️ Technologies Used

- **Python** — Application programming language
- **Flask** — Web framework
- **MySQL 8.0** — Relational database
- **PyMySQL** — Python MySQL connector
- **Gunicorn** — WSGI application server
- **Docker** — Containerization and networking

## ✨ Features

- Flask application running inside a Docker container
- MySQL database running in a separate container
- Custom Docker bridge network for container-to-container communication
- Persistent database storage using a named volume
- Environment variables for database configuration
- Web interface for viewing and submitting messages
- Health-check endpoint for the Flask application

## 📁 Project Structure

```text
Flask-MySQL-Docker-Network/
├── app.py
├── Dockerfile
├── requirements.txt
└── templates/
    └── index.html
```

## ⚙️ Prerequisites

Install the following before starting:

- Docker Desktop
- Git
- A code editor such as Visual Studio Code

Ensure Docker Desktop is running.

## 🏗️ Setup and Installation

### 1. Clone the repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd Flask-MySQL-Docker-Network
```

Replace `<YOUR_REPOSITORY_URL>` with your actual GitHub repository URL.

### 2. Build the Flask Docker image

```bash
docker build -t flask-app .
```

This builds the image using the Dockerfile in the current directory.

### 3. Create a custom bridge network

```bash
docker network create --driver bridge flask-mysql-network
```

Verify the network:

```bash
docker network ls
```

### 4. Create a persistent Docker volume

```bash
docker volume create mysql_data
```

The volume stores MySQL database files independently of the container lifecycle.

### 5. Run the MySQL container

```bash
docker run -d --name mysql --network flask-mysql-network -e MYSQL_ROOT_PASSWORD=RootPass123! -e MYSQL_DATABASE=flaskdb -e MYSQL_USER=flaskuser -e MYSQL_PASSWORD=FlaskPass123! -p 3307:3306 -v mysql_data:/var/lib/mysql mysql:8.0
```

Check the container logs:

```bash
docker logs mysql
```

Wait until MySQL is ready to accept connections.

### 6. Run the Flask container

```bash
docker run -d --name flask-app --network flask-mysql-network -p 5000:5000 -e MYSQL_HOST=mysql -e MYSQL_PORT=3306 -e MYSQL_USER=flaskuser -e MYSQL_PASSWORD=FlaskPass123! -e MYSQL_DB=flaskdb flask-app
```

Flask connects to MySQL using the hostname `mysql` and the internal database port `3306`.

### 7. Verify the containers

```bash
docker ps
```

Inspect the network:

```bash
docker network inspect flask-mysql-network
```

Both containers should appear in the network configuration.

## 🌐 Access the Application

Open the following URLs in your browser:

- Application: http://localhost:5000
- Health endpoint: http://localhost:5000/health

The health endpoint returns:

```json
{
  "status": "ok"
}
```

**Note:** The health endpoint verifies the Flask application process, not MySQL connectivity.

## 🗄️ Access the MySQL Database

Connect to MySQL from the terminal:

```bash
docker exec -it mysql mysql -u flaskuser -p
```

Enter the configured password, then run:

```sql
USE flaskdb;
SHOW TABLES;
DESCRIBE messages;
SELECT * FROM messages;
```

Exit the MySQL client:

```sql
EXIT;
```

## 🔌 Understanding Docker Networking

The application uses a custom bridge network named `flask-mysql-network`.

- Flask connects to MySQL using `mysql:3306`.
- MySQL is published on host port `3307` for optional external database access.
- Flask is published on host port `5000` for browser access.

The port mapping format is:

```text
HOST_PORT:CONTAINER_PORT
```

For example, `3307:3306` maps host port 3307 to MySQL's container port 3306.

The MySQL host port mapping is optional because Flask communicates directly over the Docker network.

## 💾 Persistent Storage

The named volume `mysql_data` is mounted at:

```text
/var/lib/mysql
```

This allows database files to persist when the MySQL container is stopped or removed. The volume must not be deleted if you want to preserve the stored data.

View volumes:

```bash
docker volume ls
```

## 🧹 Cleanup

Stop the containers:

```bash
docker stop flask-app mysql
```

Remove the containers:

```bash
docker rm flask-app mysql
```

Remove the network after the containers have been disconnected or removed:

```bash
docker network rm flask-mysql-network
```

**Warning:** Do not remove the `mysql_data` volume unless you intentionally want to delete the stored database files.

## 🔐 Security Notes

- The passwords in the example commands are for local learning only.
- Use strong, unique credentials for real deployments.
- Do not commit passwords or other secrets to GitHub.
- Avoid publishing the MySQL port if external database access is unnecessary.

## 🎯 Learning Outcomes

- Building Docker images from a Dockerfile
- Running Flask and MySQL in separate containers
- Creating and inspecting a custom bridge network
- Configuring container communication using environment variables
- Publishing container ports to the host
- Persisting database data using Docker volumes
- Executing SQL commands inside a running container

## 👨‍💻 Author

**Your Name**

GitHub: [Your GitHub Profile](https://github.com/)

---

*This project was created for learning and demonstrating Docker containerization, networking, and database integration.*