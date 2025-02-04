FROM maven:3.9.9-eclipse-temurin-21-jammy AS BUILD_IMAGE 
#RUN  git clone https://github.com/hkhcoder/vprofile-project.git

#RUN cd vprofile-profile && git checkout containers && mvn install

# Clone the correct repository
RUN git clone https://github.com/hkhcoder/vprofile-project.git

# Change into the repository folder, checkout the containers branch, and install dependencies
RUN cd vprofile-project && \
    git checkout containers && \
    mvn install

FROM tomcat:10-jdk21

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=BUILD_IMAGE /vprofile-profile/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
