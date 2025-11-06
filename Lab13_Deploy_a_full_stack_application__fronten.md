# Lab 13: Deploy a full-stack application (frontend and backend) using Docker containers.

## Objective

The objective of this lab is to deploy a complete full-stack web application using Docker containers. The frontend (React app) and backend (Node.js or Spring Boot app) will each run in separate containers, and they will communicate through Docker networking. This lab demonstrates how to containerize, build, and run both parts together for a production-like environment.

---

## Tools / Technologies

* Git & GitHub – For version control and pushing code to the repository.
* Docker – For containerization of frontend and backend.
* Docker Compose – To manage multi-container applications.
* Node.js / React – For backend and frontend application code.
* VS Code / Terminal – For development and running commands.

---

## Prerequisites

* Docker and Docker Compose installed.
* Node.js installed locally (for testing before containerization).
* Git and GitHub account configured.
* Basic understanding of Dockerfiles and networking.

---

## Steps / Commands

### 1. Clone the Project Repository

git clone <your-repository-url>
cd <repository-folder>

*This clones your project into your local machine.*

---

### 2. Create Backend Dockerfile

Inside your backend/ folder, create a file named Dockerfile:

# Use Node.js as base image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# Expose backend port
EXPOSE 5000

# Start server
CMD ["npm", "start"]

---

### 3. Create Frontend Dockerfile

Inside your frontend/ folder, create another Dockerfile:

# Build stage
FROM node:18 as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

---

### 4. Create Docker Compose File

At the project root, create a file named docker-compose.yml:

version: '3'
services:
  backend:
    build: ./backend
    container_name: backend_container
    ports:
      - "5000:5000"
    networks:
      - fullstack-net

  frontend:
    build: ./frontend
    container_name: frontend_container
    ports:
      - "3000:80"
    depends_on:
      - backend
    networks:
      - fullstack-net

networks:
  fullstack-net:
    driver: bridge

---
<img width="1280" height="680" alt="image" src="https://github.com/user-attachments/assets/5020bda2-9ccc-441b-97e2-137bf0a37314" />


### 5. Build and Run Containers

docker-compose up --build

*This command builds both images and starts containers.*

Check running containers:

docker ps

Visit your app:

* Frontend: [http://localhost:3000](http://localhost:3000)
* Backend API: [http://localhost:5000](http://localhost:5000)

---

### 6. Verify Communication Between Frontend and Backend

* Open the frontend and test API calls (e.g., fetching data from backend).
* Check logs:

    docker logs backend_container
  docker logs frontend_container
  

---

### 7. Stop and Remove Containers

docker-compose down

*Stops and removes containers, networks, and images.*

---

## Expected Output / Result

* Both frontend and backend containers run successfully.
* Access the frontend at http://localhost:3000.
* Frontend communicates with backend API.
* Screenshot examples to capture:

  * Docker Desktop showing both containers running.
  * Browser showing frontend and backend responses.
  <img width="1280" height="655" alt="image" src="https://github.com/user-attachments/assets/b76b11e6-82b6-4cac-8202-3306b9f9cd02" />


---

## Deliverables (what to push)

* Labs/Lab13_FullStack_Deployment.md (this file, completed)
* Lab13_files/ folder containing:

  * Frontend Dockerfile
  * Backend Dockerfile
  * docker-compose.yml
  * Screenshots of output

---

## Notes / Tips

* If you get a CORS error, configure CORS in backend.
* Always use relative URLs or environment variables for API calls.
* Use docker-compose logs -f for debugging.
* If containers fail to start, check exposed ports and Dockerfile paths.
