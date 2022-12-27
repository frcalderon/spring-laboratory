FROM openjdk:11
COPY "keycloak" "keycloak"
EXPOSE 8080
ENV KEYCLOAK_ADMIN=admin
ENV KEYCLOAK_ADMIN_PASSWORD=admin
ENTRYPOINT ["keycloak/bin/kc.sh", "start-dev"]