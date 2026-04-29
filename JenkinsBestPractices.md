
# Jenkins Best Practices Guide (Interview-Oriented)

## 1. Overview
This guide covers Jenkins best practices focusing on:
- Disk space optimization
- Build time reduction
- Efficient pipeline design
- Docker optimization
- Real-world interview scenarios

---

## 2. Workspace Management (Disk Optimization)

### Problem
Jenkins accumulates files in workspace leading to disk exhaustion.

### Solution
Use workspace cleanup:

```groovy
post {
    always {
        cleanWs()
    }
}
```

### Best Practice
- Always clean workspace after build
- Use ephemeral agents when possible

---

## 3. Git Optimization (Time + Space)

### Problem
Full clone every time wastes time and disk.

### Solution
```groovy
git url: "repo-url", branch: "main", depth: 1
```

### Benefits
- Faster clone
- Less storage usage

---

## 4. Docker Optimization

### Problem
Repeated builds create multiple layers consuming disk.

### Best Practices

#### 1. Use Docker cache
```dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
```

#### 2. Avoid unnecessary rebuilds
```bash
docker compose up -d
```
instead of:
```bash
docker compose up --build
```

#### 3. Cleanup
```bash
docker system prune -af
```

---

## 5. Pipeline Optimization

### Example Optimized Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git url: 'repo-url', branch: 'main', depth: 1
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t app:latest .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker compose pull
                docker compose up -d
                '''
            }
        }
    }

    post {
        always {
            sh 'docker system prune -af'
            cleanWs()
        }
    }
}
```

---

## 6. Disk Space Best Practices

- Increase EC2 volume (min 20GB)
- Run periodic cleanup
- Avoid storing artifacts unnecessarily
- Use external storage (S3)

---

## 7. Time Optimization

- Parallel stages:
```groovy
parallel {
    stage('Test1') {
        steps { sh 'run test1' }
    }
    stage('Test2') {
        steps { sh 'run test2' }
    }
}
```

- Cache dependencies
- Avoid rebuilding unchanged components

---

## 8. Security Best Practices

- Use Jenkins credentials store
- Avoid hardcoding secrets
- Use:
```groovy
withCredentials([])
```

---

## 9. Interview Questions (Scenario-Based)

### Q1: Your Jenkins agent goes offline frequently. Why?
**Answer:**
- Disk full
- Agent process stopped
- Network issue
- JVM crash

---

### Q2: Build is slow. What will you optimize?
**Answer:**
- Shallow git clone
- Docker layer caching
- Parallel stages
- Avoid unnecessary rebuilds

---

### Q3: Disk space keeps filling up. Fix?
**Answer:**
- cleanWs()
- docker system prune
- log rotation
- increase volume

---

### Q4: Why use shared libraries?
**Answer:**
- Code reuse
- Standardization
- Maintainability

---

### Q5: Why not use `docker compose down` every time?
**Answer:**
- Removes containers unnecessarily
- Slows deployment
- Can remove volumes/data

---

## 10. Final Takeaways

- Always clean workspace
- Optimize Docker usage
- Use caching
- Monitor disk
- Prefer scalable architecture

---

End of Guide
