To establish a Continuous Integration and Continuous Deployment (CI/CD) pipeline for a Spring Boot application using Gradle, GitLab, and Jenkins, follow these steps:

1. **Spring Boot Application Setup**:
    - Create a Spring Boot project with Gradle as the build tool. You can use [Spring Initializr](https://start.spring.io/) to generate the project structure.

2. **Version Control with GitLab**:
    - Initialize a Git repository in your project directory:
      ```bash
      git init
      ```
    - Create a repository on GitLab and push your local project to it:
      ```bash
      git remote add origin https://gitlab.com/your-username/your-repo.git
      git add .
      git commit -m "Initial commit"
      git push -u origin master
      ```

3. **Setting Up Jenkins**:
    - Install Jenkins on your system. You can download it from the [official Jenkins website](https://www.jenkins.io/download/).
    - Install necessary plugins in Jenkins:
        - **Git Plugin**: Allows Jenkins to interact with Git repositories.
        - **Gradle Plugin**: Enables Jenkins to build projects using Gradle.
        - **GitLab Plugin**: Facilitates integration between Jenkins and GitLab.

4. **Creating a Jenkins Pipeline**:
    - On the Jenkins dashboard, create a new item and select "Pipeline" as the project type.
    - Configure the pipeline to pull the source code from your GitLab repository:
        - In the "Pipeline" section, set the definition to "Pipeline script from SCM".
        - Choose "Git" as the SCM and provide your repository URL.
        - Add credentials if your repository is private.

5. **Defining the Jenkinsfile**:
    - Create a `Jenkinsfile` in the root of your project to define the pipeline stages:
      ```groovy
      pipeline {
          agent any
          stages {
              stage('Checkout') {
                  steps {
                      git branch: 'master', url: 'https://gitlab.com/your-username/your-repo.git'
                  }
              }
              stage('Build') {
                  steps {
                      sh './gradlew clean build'
                  }
              }
              stage('Test') {
                  steps {
                      sh './gradlew test'
                  }
              }
              stage('Package') {
                  steps {
                      sh './gradlew bootJar'
                  }
              }
              stage('Deploy') {
                  steps {
                      // Add your deployment steps here
                  }
              }
          }
          post {
              success {
                  echo 'Pipeline completed successfully.'
              }
              failure {
                  echo 'Pipeline failed.'
              }
          }
      }
      ```
    - Commit and push the `Jenkinsfile` to your GitLab repository.

6. **Integrating GitLab with Jenkins**:
    - Set up a webhook in your GitLab repository to trigger Jenkins builds on code changes:
        - Navigate to your GitLab project settings.
        - Go to "Webhooks" and add a new webhook with the URL of your Jenkins server (e.g., `http://your-jenkins-server/gitlab-webhook/`).
        - Select the events that should trigger the webhook, such as push events or merge requests.

7. **Running the Pipeline**:
    - In Jenkins, build the pipeline manually for the first time.
    - Subsequent builds will be triggered automatically based on the GitLab webhook configuration.

By following these steps, you can set up a robust CI/CD pipeline that automates the building, testing, and deployment of your Spring Boot application using Gradle, GitLab, and Jenkins. 
