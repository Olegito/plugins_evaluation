FROM openjdk:alpine

EXPOSE 8080

ADD target/demo_spotify_mvn_plugin.jar demo_spotify_mvn_plugin.jar

ENTRYPOINT [ "java", "-jar", "demo_spotify_mvn_plugin.jar" ]