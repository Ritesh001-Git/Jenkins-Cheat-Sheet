# Quality Gates in CI/CD

## What is a Quality Gate?

A **Quality Gate** is a predefined set of conditions that **must be satisfied before the CI/CD pipeline proceeds to the next stage**. If any quality gate fails, the pipeline stops, preventing low-quality or insecure code from reaching production.

**Definition:**

> A Quality Gate is a checkpoint in the CI/CD pipeline that automatically verifies code quality, testing, security, and performance standards before deployment.

**Key Point:**

A quality gate should **fail the pipeline**, not just display a warning. This ensures only code that meets the agreed standards is deployed.

---

# Why are Quality Gates Important?

Quality gates help to:

- Prevent buggy code from reaching production.
- Maintain coding standards across the team.
- Detect security vulnerabilities early.
- Improve software reliability.
- Reduce production incidents.
- Automate code quality enforcement.

---

# Common Quality Gates

| Quality Gate | Purpose | Example Condition |
|--------------|---------|------------------|
| Build Validation | Ensure project builds successfully | Build must succeed |
| Unit Testing | Verify application logic | 100% unit tests passed |
| Code Coverage | Ensure enough code is tested | Coverage ≥ 80% |
| Static Code Analysis | Detect bugs and code smells | Zero Critical Issues |
| Security Scan | Detect vulnerable dependencies | No High/Critical CVEs |
| Smoke Testing | Verify basic application functionality | All smoke tests pass |
| Integration Testing | Verify service interactions | All integration tests pass |
| Performance Testing | Ensure acceptable performance | Response time < 500 ms |
| Deployment Approval | Manual approval for production | Approval required |

---

# Common Testing Performed in CI/CD

## 1. Build Validation

Checks whether the application compiles successfully.

Example:
- Maven Build
- Gradle Build
- npm build

If build fails:
```
Pipeline Stops
```

---

## 2. Unit Testing

Tests individual methods or classes.

Example Tools:
- JUnit
- PyTest
- Jest
- NUnit

Condition:
```
All Unit Tests Must Pass
```

---

## 3. Code Coverage

Measures how much of the source code is executed by tests.

Example:

```
Coverage = 85%
```

If Quality Gate is

```
Coverage >= 80%
```

Pipeline passes.

If

```
Coverage = 65%
```

Pipeline fails.

Common Tools:
- JaCoCo
- Cobertura
- Istanbul

---

## 4. Static Code Analysis

Analyzes source code without executing it.

Detects:
- Bugs
- Code Smells
- Duplicated Code
- Security Hotspots

Common Tool:
- SonarQube

Example Rule:

```
Critical Bugs = 0
```

If SonarQube reports

```
Critical Bugs = 2
```

Pipeline fails.

---

## 5. Security Scanning

Scans application dependencies and container images for known vulnerabilities.

Common Tools:

- OWASP Dependency Check
- Trivy
- Snyk

Example Rule:

```
High Severity Vulnerabilities = 0
```

If any High/Critical vulnerability exists, deployment is blocked.

---

## 6. Smoke Testing

Verifies that the deployed application is basically functional.

Example Checks:

- Application starts successfully
- Homepage loads
- Login API works
- Database connection works

If any smoke test fails:

```
Pipeline Stops
```

---

## 7. Integration Testing

Tests communication between different services.

Examples:

- Backend ↔ Database
- Backend ↔ Redis
- Backend ↔ External APIs

---

## 8. Performance Testing

Measures application performance under load.

Example Rule:

```
Average API Response Time < 500 ms
```

If exceeded:

```
Pipeline Fails
```

---

# Popular Quality Gate Tools

| Purpose | Tool |
|----------|------|
| Static Analysis | SonarQube |
| Code Coverage | JaCoCo |
| Dependency Scan | OWASP Dependency Check |
| Container Scan | Trivy |
| Security Scan | Snyk |
| Unit Testing | JUnit, PyTest, Jest |
| Performance Testing | JMeter, k6 |

---

# Example Jenkins Pipeline with Quality Gates

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/example/project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Code Coverage') {
            steps {
                sh 'mvn jacoco:report'
            }
        }

        stage('Static Code Analysis') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Dependency Scan') {
            steps {
                sh './dependency-check.sh'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

---

# Pipeline Flow

```
Developer Pushes Code
        │
        ▼
Checkout Code
        │
        ▼
Build
        │
        ▼
Unit Tests
        │
        ▼
Code Coverage
        │
        ▼
SonarQube Analysis
        │
        ▼
Quality Gate
        │
        ▼
Dependency Scan
        │
        ▼
Docker Build
        │
        ▼
Deploy
```

If any stage fails:

```
Pipeline Stops
```

---

# Real-Time Example

Suppose the project has the following quality gate rules:

- Unit Test Pass Rate = 100%
- Code Coverage ≥ 80%
- SonarQube Critical Issues = 0
- High Severity CVEs = 0
- Smoke Tests = Pass

A developer pushes new code.

Results:

```
Unit Tests          ✅ Passed
Coverage            ❌ 72%
SonarQube           ✅ Passed
Security Scan       ✅ Passed
Smoke Test          Not Executed
```

Since the coverage gate failed:

```
Pipeline Failed
Deployment Blocked
```

The developer must improve test coverage before merging the code.

---

# Production Example

In one project, the team configured these quality gates:

- SonarQube Quality Gate
- JaCoCo Code Coverage (Minimum 80%)
- OWASP Dependency Check
- Unit Tests
- Smoke Tests

The pipeline automatically blocked merges whenever:

- Code coverage dropped below 80%.
- SonarQube detected critical issues.
- OWASP Dependency Check found High/Critical vulnerabilities.
- Unit tests failed.
- Smoke tests failed.

This prevented low-quality or insecure code from reaching production.

---

# Best Practices

- Never ignore Quality Gate failures.
- Keep Quality Gate rules consistent across all environments.
- Use remote SonarQube servers for centralized analysis.
- Set realistic but strict coverage thresholds.
- Scan dependencies in every pipeline run.
- Run smoke tests immediately after deployment.
- Treat Quality Gate failures as blockers, not warnings.
- Review and update Quality Gate rules periodically.

---

# Interview Answer (30–45 Seconds)

> A Quality Gate is an automated checkpoint in a CI/CD pipeline that ensures the code meets predefined quality standards before moving to the next stage. Typical quality gates include successful builds, 100% unit test pass rate, minimum code coverage (for example, 80%), zero critical issues from SonarQube, no high-severity vulnerabilities from OWASP Dependency Check or Trivy, and successful smoke tests. If any quality gate fails, the pipeline is automatically stopped, preventing low-quality or insecure code from reaching production. In production projects, Quality Gates are commonly integrated into Jenkins pipelines using tools like SonarQube, JaCoCo, and OWASP Dependency Check to enforce consistent code quality and security.