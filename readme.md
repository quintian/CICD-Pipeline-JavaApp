This project is to create a Jenkins and ansible pipeline to automatically build, test and deploy an app on github.
Application source:
- Jenkins will clone Spring PetClinic from GitHub:
  https://github.com/spring-projects/spring-petclinic.git
- PetClinic should NOT be stored inside this repo.

Pipeline:
1. Jenkins starts from docker-compose.
2. Jenkins checks out Spring PetClinic from GitHub.
3. Jenkins builds with Maven.
4. Jenkins runs tests.
5. Jenkins runs SonarQube scan.
6. Jenkins packages the app.
7. Deployment is manual/optional.
8. If DEPLOY=true, Jenkins runs Ansible to deploy the built JAR to a target server.

Local repo:
- docker-compose.yml starts Jenkins + SonarQube
- Jenkinsfile tells Jenkins to clone PetClinic from GitHub
- Jenkins builds/tests/scans PetClinic
- Ansible deploy is optional/manual