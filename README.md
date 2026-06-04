# DevOps

DevOps project with developer tool use. This branch the code for Jenjins CI/CD Pipeline to run on local device.

## Jenkins

### Create required Containers

On jenkins branch run this commands on terminal.

```sh
docker-compose -f docker-compose.jenkins.yml up -d --build
```

### Get Jenkins admin password

to get jenkins instance password for log in.

```sh
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Access the Jenkins ui `localhost:8080`

### New Jenkins pipeline

to create and run jenkins instance.

Before running the Jenkins pipeline, import the project `.env` into Jenkins via the GUI:

1. Open Jenkins and go to **Manage Jenkins** → **System**.
2. Under **Global properties**, check **Environment variables**.
3. Add each key/value from the project `.env` file.
4. Save the configuration.

### Create a new Jenkins pipeline

1. From the Jenkins dashboard, click **New Item**.
2. Enter a name, select **Pipeline**, then click **OK**.
3. In the job configuration, go to the **Pipeline** section.
4. Choose **Pipeline script from SCM** (recommended) and set **SCM** to **Git**.
5. Set the repository URL and branch (for example, the `jenkins` branch).
6. Set **Script Path** to your Jenkinsfile (for example, `Jenkinsfile`).
7. Click **Save**, then **Build Now** to run the pipeline.
