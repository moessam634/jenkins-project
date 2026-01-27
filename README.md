# Spring Boot CI/CD Pipeline with Jenkins, Docker, SonarQube, Trivy, and ArgoCD (Kubernetes on EC2)

This project demonstrates an end-to-end CI/CD pipeline for a Java Spring Boot application using Jenkins, Docker, SonarQube for code quality, Trivy for vulnerability scanning, and ArgoCD for GitOps deployment to a Kubernetes cluster on AWS EC2.

---

## **Architecture Overview**

- **Source Code:** GitHub (branch: `my-changes`)
- **CI/CD:** Jenkins (pipeline as code, with Blue Ocean for visualization)
- **Build & Test:** Maven
- **Image Build & Push:** Docker + DockerHub
- **Code Quality:** SonarQube
- **Image Security:** Trivy
- **Artifact Registry:** Nexus (if used)
- **GitOps Deployment:** Kubernetes (K3s/K8s on EC2) + Argo CD
- **CD Trigger:** Jenkins calls ArgoCD API

---

## **Prerequisites**

- AWS EC2 instance running Ubuntu, with security group allowing NodePort + SSH + HTTP.
- K3s/Kubernetes cluster running on EC2.
- Jenkins server (outside or on EC2), with **Blue Ocean** plugin installed.
- DockerHub account (for pushing images).
- SonarQube server, Trivy for scanning.
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) installed in cluster.
- [Nexus Repository](https://www.sonatype.com/products/repository-oss) if used for artifact storage.
- Image credentials and GitHub PAT stored as Jenkins credentials.
- NodePort for ArgoCD opened in EC2 security group.

---

## **Pipeline Steps**

1. **Code Checkout**  
   Jenkins checks out the `my-changes` branch from GitHub.

2. **Build Application**  
   Maven builds the Spring Boot application.

3. **Run Unit Tests**  
   Maven runs the unit tests.

4. **SonarQube Analysis**  
   Jenkins sends code to SonarQube for static code analysis.

5. **Build Docker Image**  
   Jenkins builds a Docker image tagged with the Jenkins build number.

6. **Trivy Image Scan** *(optional/disabled in sample pipeline)*  
   Trivy scans the image for security vulnerabilities and archives the report.

7. **Push Docker Image**  
   Jenkins pushes both the build tag and the `latest` tag to DockerHub.

8. **User Acceptance Testing**  
   Jenkins runs acceptance tests if present in the Maven profile.

9. **Update Kubernetes Manifest**  
   The image tag in the Kubernetes manifest YAML is updated using `sed`, committed, and pushed to GitHub using a PAT.

10. **Promote with Argo CD**  
    Jenkins triggers Argo CD Sync via its API by calling the NodePort endpoint with an API token from Jenkins credentials.

---

## **ArgoCD API NodePort**

- ArgoCD server is exposed as a NodePort (`kubectl edit svc argocd-server -n argocd`).
- Find the port with `kubectl get svc -n argocd`.
- Jenkins pipeline calls `http://<EC2_PUBLIC_IP>:<NODEPORT>/api/v1/applications/<app-name>/sync`.

---

## **Credentials Setup**

- **DockerHub:** Set as Jenkins "Username with password".
- **GitHub:** Personal Access Token as "Username with password" (`github-creds`).
- **ArgoCD:** API token as Jenkins "Secret text" (`argoCD-creds`).
- Adjust the credentialsId in the Jenkinsfile as appropriate.

---

## **Security Group Setup (AWS EC2)**

- Open inbound SSH (port 22) from your IP.
- Open any NodePort you assign to ArgoCD from your Jenkins server (can use 0.0.0.0/0 for testing—**not recommended for prod**).
- Open 8080 or other ports for web UI as desired.

---

## **How to Run the Pipeline**

1. **Set up all prerequisite services and credentials in Jenkins.**
2. **Expose ArgoCD NodePort** and note your EC2 public IP/port.
3. **Configure DockerHub, GitHub, and ArgoCD credentials in Jenkins.**
4. **Run the pipeline.**  
   - On successful build, Jenkins updates the manifest and then triggers ArgoCD via the NodePort endpoint.
5. **Access your application or the ArgoCD UI from your public EC2 endpoint.**

---

## **Troubleshooting**

- **Jenkins cannot reach ArgoCD:** Check NodePort/Firewall setup.
- **Cannot SSH/port-forward:** Verify EC2 security group, check inbound rules.
- **API refuses connection:** Double-check credentials, URLs, and that ArgoCD server is Running.
- **Localhost issues:** Remember that localhost in a port-forward is local to *that* machine only—use public EC2 IP for remote Jenkins.

---

## **Visual Guide**

_Add a clear image for each of these solutions as you build your system:_

- **SonarQube**           ![SonarQube](sonarqube.png)
- **Nexus Repository**    ![Nexus](nexus.png)
- **Docker Hub**          ![Docker Hub](dockerhub.png)
- **Argo CD**             ![Argo CD](argocd.png)
- **Jenkins / Blue Ocean**![Blue Ocean](blueocean.png)
- **Application UI**      ![Application UI](appui.png)

---

## **References**

- [Argo CD API Docs](https://argo-cd.readthedocs.io/en/stable/operator-manual/api/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)

---

## **Author**

- **Mohamed Essam** 

---

**Happy CICD!**
