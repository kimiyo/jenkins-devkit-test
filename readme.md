# Jenkins DevKit: Automating Jenkins

## source

- https://medium.com/@Joachim8675309/jenkins-devkit-automating-jenkins-42b970550a0b

## commands

```bash
docker-compose up
```

- In another terminal window or tab, we can test that docker does work from the container:

```bash
docker exec -ti jenkins-devkit bash
docker ps
```

- Automating Plugin Installation

```bash
curl -s https://gist.githubusercontent.com/darkn3rd/5da788f00466e2d5d1b3d619710fc647/raw/ > plugins.txt
```

```bash
cat <<-DOCKERFILE >> Dockerfile

####### INSTALL PLUGINS
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh \
 < /usr/share/jenkins/ref/plugins.txt
DOCKERFILE
```

- Automate Bypassing the Setup Wizard

```bash
cat <<-DOCKERFILE >> Dockerfile
####### DISABLE SETUP WIZARD
RUN echo $JENKINS_VERSION > \
 /usr/share/jenkin/ref/jenkins.install.UpgradeWizard.state; \
 echo $JENKINS_VERSION > \
 /usr/share/jenkins/ref/jenkins.install.InstallUtil.lastExecVersion
DOCKERFILE

cat <<-DOCKERFILE >> Dockerfile
####### CONFIGURE JENKINS AND INSTALLED PLUGINS
ENV CASC_JENKINS_CONFIG /var/jenkins_home/casc_configs
RUN mkdir -p /var/jenkins_home/casc_configs
COPY jenkins.yaml /var/jenkins_home/casc_configs/jenkins.yaml
DOCKERFILE
```

## Test

```bash
docker-compose stop
docker-compose build
docker-compose up -d
```

```bash
http://localhost:8080
```

## clean up

```bash
# stop and remove running container
docker-compose stop
docker-compose rm
# delete persistent volume
docker volume rm jenkins-devkit_jenkins_data
# purge docker image
docker rmi  jenkins-devkit_jenkins:latest
```
