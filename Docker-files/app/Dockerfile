FROM openjdk:11 AS build_image
RUN apt update && apt install maven -y
RUN git clone --branch docker https://github.com/hkhcoder/vprofile-project.git
RUN cd vprofile-project && mvn clean install 

FROM tomcat:9-jre11
LABEL "Project"="vprofile"
LABEL "Author"="zangetsu"

RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=build_image vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
WORKDIR /usr/local/tomcat/
VOLUME /usr/local/tomcat/webapps