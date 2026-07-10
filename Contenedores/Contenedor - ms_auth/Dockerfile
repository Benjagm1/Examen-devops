# Etapa 1: Construcción (Builder)
FROM eclipse-temurin:25-jdk AS builder
WORKDIR /build

# Se copia el código fuente del backend
COPY Stack/Backend /build

RUN chmod +x ./mvnw
# Descarga de dependencias offline
RUN ./mvnw -q -DskipTests dependency:go-offline
# Empaquetado del microservicio específico
RUN ./mvnw -q -DskipTests -Pdocker -pl ms-autenticacion -am package

# Etapa 2: Ejecución (Runtime minimalista)
FROM eclipse-temurin:25-jre-alpine
WORKDIR /app

# Instalamos curl usando apk (gestor de Alpine) y creamos el usuario no-root 'gea'
RUN apk add --no-cache curl \
    && addgroup -S gea && adduser -S gea -G gea

# Cambiamos al usuario seguro
USER gea

# Copiamos el .jar compilado (respetando el nombre auth.jar)
COPY --from=builder /build/ms-autenticacion/target/auth.jar ./app.jar

EXPOSE 8051

# Validación de salud del microservicio para ECS
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -f http://localhost:8051/ || exit 1

ENTRYPOINT ["java", "-jar", "app.jar"]