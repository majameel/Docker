<<<<<<< HEAD
# A simple MERN stack application 

### Create a network for the docker containers

`docker network create demo`

### Build the client 

```sh
cd mern/frontend
docker build -t mern-frontend .
```

### Run the client

`docker run --name=frontend --network=demo -d -p 5173:5173 mern-frontend`

### Verify the client is running

Open your browser and type `http://localhost:5173`

### Run the mongodb container

`docker run --network=demo --name mongodb -d -p 27017:27017 -v ~/opt/data:/data/db mongodb:latest`

### Build the server

```sh
cd mern/backend
docker build -t mern-backend .
```

### Run the server

`docker run --name=backend --network=demo -d -p 5050:5050 mern-backend`

## Using Docker Compose

`docker compose up -d`

=======
# 📦 MERN Stack Application with Docker Compose

This repository contains a tutorial on containerizing a MERN (MongoDB, Express, React, Node.js) stack application and deploying it using Docker Compose.

## 📋 Table of Contents

- [🔀 Branch Structure](#-branch-structure)
- [🏛️ Architecture Overview](#️-architecture-overview)
- [💻 What is MERN Stack?](#-what-is-mern-stack)
- [🚀 Quick Start](#-quick-start)
- [📂 Project Structure](#-project-structure)
- [🐳 Manual Containerization](#-manual-containerization)
- [🧩 Docker Compose Deployment](#-docker-compose-deployment)
- [📝 Environment Variables](#-environment-variables)
- [🧪 Testing](#-testing)
- [🔧 Troubleshooting](#-troubleshooting)

## 🔀 Branch Structure

This repository has two main branches:

- **`main`**: Contains the source code for running the application without containers using npm
- **`compose`**: Contains the final containerized version with Dockerfiles and Docker Compose configuration

## 🏛️ Architecture Overview

This application follows a **three-tier architecture**:

### Tier 1: Presentation Layer (Frontend)
- **React** application serving the user interface
- Runs on port `5173`

### Tier 2: Business Logic Tier (Backend)
- **Express.js** server handling API requests
- **Node.js** runtime environment
- Runs on port `5050`

### Tier 3: Data Tier (Database)
- **MongoDB** database for data persistence
- Runs on port `27017`

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   React     │───▶│  Express.js │───▶│  MongoDB    │
│ (Frontend)  │    │ (Backend)   │    │ (Database)  │
│  Port 5173  │    │  Port 5050  │    │ Port 27017  │
└─────────────┘    └─────────────┘    └─────────────┘
```

## 💻 What is MERN Stack?

**MERN** stands for:
- **M**ongoDB - NoSQL database
- **E**xpress - Backend web framework
- **R**eact - Frontend JavaScript library
- **N**ode.js - JavaScript runtime

### Why MERN is Popular:
1. **JavaScript Everywhere**: One language for frontend and backend
2. **Open Source**: Free and community-driven
3. **Easy to Learn**: Simple setup and development
4. **Scalable**: Suitable for small to large applications

## 🚀 Quick Start

### Prerequisites
- Docker and Docker Compose installed
- Git for cloning the repository

### Clone and Run

```bash
# Clone the repository
git clone https://github.com/iam-veeramalla/MERN-docker-compose.git
cd MERN-docker-compose

# Checkout the compose branch (contains Docker configuration)
git checkout compose

# Build and run with Docker Compose
docker-compose up --build
```

### Access the Application
- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:5050/record
- **MongoDB**: localhost:27017 (for database tools)

## 📂 Project Structure

```
├── frontend/
│   ├── src/
│   │   └── components/
│   │       ├── Record.js
│   │       └── RecordList.js
│   ├── package.json
│   └── Dockerfile
├── backend/
│   ├── db/
│   │   └── connection.js
│   ├── routes/
│   │   └── record.js
│   ├── server.js
│   ├── package.json
│   └── Dockerfile
└── docker-compose.yml
```

## 🐳 Manual Containerization

If you want to understand how containerization works step by step:

### 1. Create Docker Network

```bash
docker network create mern-network
```

### 2. Build Frontend Container

```bash
# Navigate to frontend directory
cd frontend

# Build the image
docker build -t mern-frontend .

# Run the container
docker run -d --name frontend --network mern-network -p 5173:5173 mern-frontend
```

### 3. Run MongoDB Container

```bash
docker run -d --name mongodb --network mern-network -p 27017:27017 -v mongodb_data:/data/db mongo:latest
```

### 4. Build Backend Container

```bash
# Navigate to backend directory
cd backend

# Build the image
docker build -t mern-backend .

# Run the container
docker run -d --name backend --network mern-network -p 5050:5050 mern-backend
```

## 🧩 Docker Compose Deployment

The `docker-compose.yml` file orchestrates all services:

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "5173:5173"
    depends_on:
      - backend
    networks:
      - mern-network

  backend:
    build: ./backend
    ports:
      - "5050:5050"
    depends_on:
      - mongo
    networks:
      - mern-network

  mongo:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
    networks:
      - mern-network

volumes:
  mongodb_data:

networks:
  mern-network:
```

### Commands

```bash
# Build and start all services
docker-compose up --build

# Run in detached mode
docker-compose up -d

# Stop all services
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

## 📝 Environment Variables

The application uses the following environment variables:

### Backend Environment Variables
- `PORT`: Server port (default: 5050)
- `MONGODB_URI`: MongoDB connection string (default: mongodb://mongodb:27017/test)

### Frontend Environment Variables
- `VITE_API_URL`: Backend API URL (default: http://localhost:5050)

You can set these in a `.env` file or modify the docker-compose.yml file.

## 🧪 Testing

### API Endpoints

The backend provides these endpoints:

- `GET /record` - Get all records
- `GET /record/:id` - Get a specific record
- `POST /record` - Create a new record
- `PUT /record/:id` - Update a record
- `DELETE /record/:id` - Delete a record

### Test with curl

```bash
# Get all records
curl http://localhost:5050/record

# Create a new record
curl -X POST http://localhost:5050/record \
  -H "Content-Type: application/json" \
  -d '{"name": "Test", "position": "Developer", "level": "Senior"}'
```

## 🔧 Troubleshooting

### Common Issues

1. **Port already in use**
   ```bash
   # Check what's using the port
   netstat -tulpn | grep :5173
   
   # Change ports in docker-compose.yml if needed
   ```

2. **MongoDB connection issues**
   ```bash
   # Check if MongoDB container is running
   docker ps
   
   # Check MongoDB logs
   docker logs mongodb
   ```

3. **Frontend can't connect to backend**
   - Ensure all containers are on the same network
   - Check if backend is accessible: `curl http://localhost:5050/record`

### Logs

```bash
# View logs for all services
docker-compose logs

# View logs for specific service
docker-compose logs backend

# Follow logs in real-time
docker-compose logs -f
```

### Reset Everything

```bash
# Stop and remove all containers, networks, and volumes
docker-compose down -v
docker system prune -f

# Rebuild and restart
docker-compose up --build
```

## 📚 Additional Resources

- [Docker Documentation](https://docs.docker.com)
- [Docker Compose Reference](https://docs.docker.com/compose)
- [MongoDB Docker Image](https://hub.docker.com/_/mongo)
- [Node.js Docker Image](https://hub.docker.com/_/node)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Note**: This tutorial is based on the work by [Abhishek Veeramalla](https://github.com/iam-veeramalla). Make sure to check out the original repository for the complete code and additional examples.
>>>>>>> a9b0ade054edbe5081cdc7d20b2cf22cf39cfc8e
