FROM 118.25.127.51:8088/tomcat/tomcat:v8.5.41
ADD target/solo.war /tmp
RUN unzip -q /tmp/solo.war -d /usr/local/tomcat/webapps/ROOT

COPY deploy/docker-entrypoint.sh /usr/bin/docker-entrypoint.sh
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 8080
CMD ["./bin/catalina.sh", "run"]
