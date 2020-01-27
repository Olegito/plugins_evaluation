# plugins_evaluation
This repository is made to evaluate various __[Maven](https://maven.apache.org/)__ plugins for building Docker images of java (Spring Boot) applications

# General comparison

| Feature | Jib |
| ------- | ----------- |
| Dockerfile   | not needed |
| Docker damon   | optional |
| setting | optional from simple to specefic |
| peformance    | good |
| work with online repos    | yes |
| compatibility    | Maven, Gradle |

# What you need to know before starting

__[Spring Boot Crash Course](https://www.youtube.com/watch?v=vtPkZShrvXQ&t=)__

__[Best Docker tutorial](https://www.youtube.com/watch?v=YFl2mCHdv24)__

__[Dockerfile tutorial](https://www.youtube.com/watch?v=LQjaJINkQXY&t=)__

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
<a name="desc"></a>
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
