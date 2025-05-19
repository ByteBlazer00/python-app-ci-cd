

# python-app-ci-cd

This project demonstrates how to build a Jenkins-based CI/CD pipeline to deploy a sample Python Flask application on a Kubernetes cluster.

---

## 🚀 Project Overview

The goal is to automate the build, test, and deployment process of a Python web application using Jenkins, Docker, and Kubernetes.

---

## 🛠️ Stack Used

- **Python Flask** – Sample web application  
- **Docker** – Containerization  
- **Jenkins** – CI/CD pipeline automation  
- **Kubernetes** – Container orchestration  
- **GitHub** – Source code repository  

---

## 🔧 CI/CD Pipeline Flow

1. **Code Push** – Developer pushes code to GitHub
2. **Build Triggered** – Jenkins polls GitHub or uses a webhook
3. **Docker Image Built** – Jenkins builds a Docker image
4. **Push to Docker Hub** – Image is pushed to a Docker registry
5. **Deploy to K8s** – Jenkins deploys the updated image to a Kubernetes cluster

---

## 📁 Project Structure

python-app-ci-cd/
│

├── app/ # Python Flask application

│ └── app.py

│
├── Dockerfile # Docker configuration

├── deployment.yaml # Kubernetes deployment manifest

├── service.yaml # Kubernetes service manifest

├── jenkins/

│ └── Jenkinsfile # Jenkins pipeline script

├── README.md # Project documentation

└── .gitignore




---

## ✅ Prerequisites

- Jenkins server with Docker and Kubernetes CLI configured
- Access to a Kubernetes cluster (local or cloud)
- Docker Hub account (or any container registry)
- GitHub repository for source code

---

## 📦 How to Run

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/python-app-ci-cd.git

2. Configure Jenkins credentials and environment variables

3. Setup Jenkins pipeline using the Jenkinsfile

4. Push code to GitHub and watch the pipeline automate everything!





📷 Demo

![Screenshot 2025-05-11 175319](https://github.com/user-attachments/assets/54dc7362-f781-48d7-b65b-db5eadea0b45)


🧑‍💻 Author
ByteBlazer00


