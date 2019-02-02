---
title: Create CI/CD pipeline for Google App Engine with CircleCI 2.0 
key: 20181221
tags: 
  - GCP
  - DevOps
---
In previous post, I talked about how to create CI/CD pipeline for AWS Lambda Functions with CircleCI. Today, we are going to create a CI/CD pipeline for **GAE**(Google App Agine) with CircleCI 2.0. 

The reasons I choice GAE are that it is free and zero effort of infrastructure management! Even, we do not need to containerize the application and GAE provides a maven plugin for Java applications, which make the deployment process very simple and smooth. 

<!--more-->

We need the following resources to create our CI/CD pipeline today:

1. A GCP management account and a project. 
2. A Bitbucket or Github account.
3. A CircleCI account linked with the above git account.
4. A Google App Engine application.
5. The source code of GAE application (my example is a Spring Boot Java application).

### Set up Google App Engine

- First, create a standard environment Google App Engine application in GCP web console as below. 

![gae-ci-started](https://s3.amazonaws.com/runzhuo-me/image/gae-ci-get-started.png)

- Second, create a service key, which is used by CircleCI to deploy the application. Go to *Credentials* and click *Create Credentials* and then create a service account key in json format. We must keep this json file securely in a private place! 

![gae-ci-service-key](https://s3.amazonaws.com/runzhuo-me/image/gae-ci-service-key.png)

- Third, go to *API* in GCP web console. Search "App Engine Admin API" and enable it for the project.

![gae-ci-enable-api](https://s3.amazonaws.com/runzhuo-me/image/gae-ci-enable-api.png)

### Deploy GAE through CircleCI

- First, create ".circleci" folder at the root path of our application. Then, create a `install-and-activate-google-cloud-sdk.sh` file in that folder. 

```shell
#!/bin/bash

set -e

# expecting the install directly in the home directory
GCLOUD=${HOME}/google-cloud-sdk/bin/gcloud

echo ${GCLOUD_SERVICE_KEY} | base64 --decode --ignore-garbage > ${HOME}/gcloud-service-key.json

if ${GCLOUD} version 2>&1 >> /dev/null; then
    echo "Cloud SDK is already installed"
else
    SDK_URL=https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-190.0.1-linux-x86_64.tar.gz
    INSTALL_DIR=${HOME}

    cd ${INSTALL_DIR}
    wget ${SDK_URL} -O cloud-sdk.tar.gz
    tar -xzvf cloud-sdk.tar.gz

    ${GCLOUD} components install app-engine-java
fi

${GCLOUD} auth activate-service-account --key-file ${HOME}/gcloud-service-key.json
${GCLOUD} config set project ${GCLOUD_PROJECT}

```

- Second, add environment variables for our project in CircleCI as below. The `GCLOUD_PROJECT` is the name of our GCP project and `GCLOUD_SERVICE_KEY` is the base64 value of the json service key file from the second step in previous section.

![gae-ci-env-var](https://s3.amazonaws.com/runzhuo-me/image/gae-ci-env-variables.png)

- Third, set up `config.yml` file in ".circleci" folder and add a deploy job as below:

```yaml
{% raw %}
  deploy:
    docker:
      - image: circleci/openjdk:8-jdk-node-browsers
    steps:
      - checkout
      - restore_cache:
          key: v3-YOUR-PROJECT-{{ checksum ".circleci/install-and-activate-google-cloud-sdk.sh" }}
      - run: mvn dependency:go-offline
      - run: /bin/bash .circleci/install-and-activate-google-cloud-sdk.sh
      - run: mvn appengine:deploy
      - save_cache:
          key: v3-YOUR-PROJECT-{{ checksum ".circleci/install-and-activate-google-cloud-sdk.sh" }}
          paths:
            - ~/google-cloud-sdk
            - ~/.m2
{% endraw %}
```

Now, when we push any changes to our git repository, our code will be built by CircleCI and deploy to GAE automatically. 

### Reference

1. [Getting started with Google App Engine and Spring Boot](https://medium.com/google-cloud/getting-started-with-google-app-engine-and-spring-boot-in-5-steps-2d0f8165c89)

2. [Deploying Kotlin/Java applications to Google App Engine Standard with CircleCI](https://medium.com/2park/deploying-kotlin-java-applications-to-google-app-engine-standard-with-circleci-580c04f77cb7)
