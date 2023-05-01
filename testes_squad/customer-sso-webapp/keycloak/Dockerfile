FROM maven as plugin_migration

COPY ./plugin_migration/pom.xml /tmp/
COPY ./plugin_migration/src /tmp/src
WORKDIR /tmp/
RUN mvn clean package


FROM maven as plugin_otp

COPY ./plugin_otp/pom.xml /tmp/
COPY ./plugin_otp/src /tmp/src
WORKDIR /tmp/
RUN mvn clean package


FROM quay.io/keycloak/keycloak:20.0.3

USER 0

# Add simple custom JGroups configuration
COPY --chown=keycloak:keycloak ./cache-custom.xml /opt/keycloak/conf/cache-custom.xml

# Add enhanced custom JGroups configuration with encryption support
COPY --chown=keycloak:keycloak ./cache-custom-jgroups.xml /opt/keycloak/conf/cache-custom-jgroups.xml
COPY --chown=keycloak:keycloak ./jgroups-multicast-enc.xml /opt/keycloak/conf/jgroups-multicast-enc.xml
COPY --chown=keycloak:keycloak ./jgroups-multicast-diag.xml /opt/keycloak/conf/jgroups-multicast-diag.xml
COPY --chown=keycloak:keycloak ./jgroups-jdbcping-enc.xml /opt/keycloak/conf/jgroups-jdbcping-enc.xml


COPY --from=plugin_migration /tmp/target/*.jar /opt/keycloak/providers/app.jar
COPY --from=plugin_otp /tmp/target/*.jar /opt/keycloak/providers/2fa.jar


USER keycloak

ENTRYPOINT [ "/opt/keycloak/bin/kc.sh" ]