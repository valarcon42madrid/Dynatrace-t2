# Etapa de construcción de la app
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package

# Etapa final con Dynatrace OneAgent
FROM openjdk:17-slim

# Variables necesarias para OneAgent
ENV ONEAGENT_INSTALLER_SCRIPT_URL="https://xxxx4242.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest"
ENV ONEAGENT_INSTALLER_DOWNLOAD_TOKEN="dt0c01.XXXXXXX"

WORKDIR /app

# Instala curl y bash (necesarios para el script)
RUN apt-get update && apt-get install -y curl bash

# Descarga e instala OneAgent dentro del contenedor
RUN curl -o OneAgentInstaller.sh -fsSL "$ONEAGENT_INSTALLER_SCRIPT_URL?Api-Token=$ONEAGENT_INSTALLER_DOWNLOAD_TOKEN" \
    && /bin/bash OneAgentInstaller.sh APP_LOG_CONTENT_ACCESS=1 --set-app-log-content-access=true --set-infra-only=false --set-host-group=docker-test --set-host-id-source=container-id \
    && rm OneAgentInstaller.sh

# Copia el .jar desde la etapa anterior
COPY --from=build /app/target/*.jar app.jar

# Exponer puertos de la app
EXPOSE 6060 9090

# Comando para iniciar la app
CMD [ "java", "-jar", "app.jar" ]

