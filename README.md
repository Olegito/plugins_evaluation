# plugins_evaluation
This repository is made to evaluate various __[Maven](https://maven.apache.org/)__ plugins for building Docker images of java (Spring Boot) applications

# General comparison

| Feature | Jib |Kaniko|Quarkus|
| ------------------ | ----------- | ---------|------|
| Dockerfile         | not needed | needed| needed
| Docker damon       | optional | optional| needed
| Windows containers | yes | no | yes
| setting            | optional from simple to specefic | | detailed, specific, with Dockerfile|
| peformance    | good | |multistage images very memory consuming|
| work with online repos    | yes | yes| not detected|
| compatibility    | Maven, Gradle | | |
|documentation quality|good||big, helping examples, but sometimes not correct|

# What you need to know before starting

__[Spring Boot Crash Course](https://www.youtube.com/watch?v=vtPkZShrvXQ&t=)__

__[Best Docker tutorial](https://www.youtube.com/watch?v=YFl2mCHdv24)__

__[Dockerfile tutorial](https://www.youtube.com/watch?v=LQjaJINkQXY&t=)__

__[Kubernetes](https://www.youtube.com/watch?v=R-3dfURb2hA)__

# __[Jib](https://github.com/GoogleContainerTools/jib)__

Jib builds optimized Docker and OCI images for your Java applications without a Docker daemon - and without deep mastery of Docker best-practices. It is available as plugins for __[Maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#build-your-image)__ and __[Gradle](https://github.com/GoogleContainerTools/jib/tree/master/jib-gradle-plugin)__ and as a Java library.

### For better understanding.

__[Jib repository and docs for Maven plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)__

__[Jib Presentation from Google](https://www.youtube.com/watch?v=H6gR_Cv4yWI)__

__[Dockerizing an app in the cloud and in local docker environment](https://www.youtube.com/watch?v=4lepROopuiw&t=)__

__[Using Jib to build Docker Image](https://www.youtube.com/watch?v=7BV3mn0cmJE&t=)__

__[Jib examle (text article)](https://www.baeldung.com/jib-dockerizing)__

## Preparation

Able to work without preparation with one line of code e.g.:

    mvn compile com.google.cloud.tools:jib-maven-plugin:1.8.0:build -Dimage=<MY IMAGE>

---
To make this command shorter and to make the work more conveniet overall you may want to make the following setup:

Add the following plugin into the `pom.xml` of your maven project:

    <project>
    ...
    <build>
        <plugins>
        ...
        <plugin>
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>1.8.0</version>
            <configuration>
            <to>
                <image>myimage</image>
            </to>
            </configuration>
        </plugin>
        ...
        </plugins>
    </build>
    ...
    </project> 

And you are almost good to go.

But in order to manage and to share your Docker images online you will need a service for this. And you need a setup of your credentials for the plugin to be able to authentivate into your repository. There are a couple of services for that:

### __[Docker Hub](https://hub.docker.com)__

The fastest way for me was to install __[Docker for Desktop](https://hub.docker.com/?overlay=onboarding)__ and after installation to log into the app with your __[Docker Hub](https://hub.docker.com/)__ account to push and manage your images with your Docker Hub easily. 

Once you are ready with setup change your `pom.xml` like this:

    <configuration>
        <to>
            <image>docker.io/my-docker-id/my-repository-name</image>
        </to>
    </configuration>


### __[Google Cloud](https://cloud.google.com/)__
When you use google cloud. Install google __[sdk](https://cloud.google.com/sdk/docs/)__ first for credentials management.

Note: you have to accept terms of use first before you can add projects when you sign in with new account. And apperently API usage of image management of google cloud is not free of charge.

Once you are ready with setup change your `pom.xml` like this:

    <configuration>
        <to>
            <image>gcr.io/my-gcp-project/my-app</image>
        </to>
    </configuration>



### __[Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/de/ecr/)__

Apart from that there is __[Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/de/ecr/)__ which uses its own __[credential management helper](https://github.com/awslabs/amazon-ecr-credential-helper)__.

AWS requires the following setup:

    <configuration>
        <to>
            <image>my_acr_name.azurecr.io/my-app</image>
        </to>
    </configuration>

Once everything is setp for your favourite registry you can execute nothing more than:

    mvn compile jib:build

And your image will be built and sent to you repository.

For more specific settings and features please check the __[Jib __repository](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)__.

# __[Kaniko](https://github.com/GoogleContainerTools/kaniko)__

# __[Quarkus](https://quarkus.io/)__


First you have to configure __[configure graalvm](https://quarkus.io/guides/building-native-image#configuring-graalvm)__

Graal for MAC

    brew cask install graalvm/tap/graalvm-ce-java11
after installing graalVM add its binary to the PATH (MAC)
    
    export PATH=/Library/Java/JavaVirtualMachines/graalvm-ce-java11-19.3.1/Contents/Home/bin:"$PATH"

And follow further instructions to be able to build images with help of Quarkus.

Or you may use the [helping tool](https://code.quarkus.io/) to build your app tamplate from scratch and start developing in a boot strapped application.

Helpful examples can be found __[here](https://github.com/quarkusio/quarkus-quickstarts/)__.

Create native executable.

    ./mvnw package -Pnative

Run your Docker daemon aun Build the docker image with:

    docker build -f src/main/docker/Dockerfile.native -t quarkus-quickstart/getting-started .

Save the contents of this file in `src/main/docker/Dockerfile.multistage` as it is not included in the getting started quickstart.

    ## Stage 1 : build with maven builder image with native capabilities
    FROM quay.io/quarkus/centos-quarkus-maven:19.3.1 AS build
    COPY src /usr/src/app/src
    COPY pom.xml /usr/src/app
    USER root
    RUN chown -R quarkus /usr/src/app
    USER quarkus
    RUN mvn -f /usr/src/app/pom.xml -Pnative clean package

    ## Stage 2 : create the docker final image
    FROM registry.access.redhat.com/ubi8/ubi-minimal
    WORKDIR /work/
    COPY --from=build /usr/src/app/target/*-runner /work/application
    RUN chmod 775 /work
    EXPOSE 8080
    CMD ["./application", "-Dquarkus.http.host=0.0.0.0"]

__Note:__ The first line  `FROM... 19.3.1` was changed to "19.3.1-java11", because the manifest in the official tutorial is no more avaliable in the repository and .


Build multistage docker image with:

    docker build -f src/main/docker/Dockerfile.multistage -t quarkus-quickstart/getting-started .

__Note:__ The build of this image failed two times with code 137 (out of memory). So further work with this was tool was stopped. 
