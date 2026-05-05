# Spring Pet Clinic — CI/CD Pipeline

## Overview
Jenkins pipeline that compiles, tests, and packages the Spring Pet Clinic app as a Docker image.

## Prerequisites
- Docker
- Jenkins (with Maven, JDK 17, and optionally JFrog plugin)

## Pipeline Stages
| Stage | Description |
|-------|-------------|
| Compile | `mvn clean compile` |
| Test | `mvn test` + JUnit report |
| Docker Build | Multi-stage image build |
| Artifactory (bonus) | Publishes artifacts to JFrog |

## Running the Docker Image
```bash
docker run -p 8080:8080 spring-petclinic:latest
```
Then open http://localhost:8080

## Notes on JCenter
JCenter was deprecated in 2021. The repository declaration is included in pom.xml
for compliance with the assignment; Maven Central handles actual resolution.

## Bonus: JFrog Artifactory Integration

The pipeline is designed to publish artifacts to JFrog Artifactory using the 
JFrog Jenkins plugin (`rtMavenDeployer` + `rtMavenRun`).

### Setup (for production use)
1. Run Artifactory:
```bash
   docker run -d --name artifactory \
     -p 8082:8082 \
     releases-docker.jfrog.io/jfrog/artifactory-oss:latest
```
2. In Jenkins → Manage Jenkins → JFrog, add:
   - Instance ID: `artifactory-server`
   - URL: `http://artifactory:8082`
   - Credentials: admin/password
3. The pipeline stage will deploy the JAR to `libs-snapshot-local`

### Why it's stubbed out
Local Artifactory startup requires significant resources and 
internal service orchestration that is environment-dependent.
The integration code is included in the Jenkinsfile and is 
production-ready once a live Artifactory instance is available.
