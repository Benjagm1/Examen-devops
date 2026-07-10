# Etapa 1: Construcción (Builder)
FROM node:22-alpine AS builder
WORKDIR /app

# OPTIMIZACIÓN: Copiar usando un comodín para atrapar package.json y package-lock.json
# Esto mejora el uso de la caché de Docker si no hay cambios en las dependencias.
COPY Stack/frontend/frontend-bo/package*.json ./
RUN npm ci

# Copiamos el resto del código fuente del frontend
COPY Stack/frontend/frontend-bo/ ./

# Construimos la aplicación de Angular
RUN npm run build

# Etapa 2: Servidor Web (Producción)
FROM nginx:1.27-alpine

# Copiamos la configuración de Nginx
COPY ["Contenedores/Contenedor - frontend/nginx.conf", "/etc/nginx/conf.d/default.conf"]

# Copiamos el build generado por Angular a la carpeta pública de Nginx
# Nota: La ruta terminada en /browser es típica de Angular 17+.
COPY --from=builder /app/dist/frontend-bo/browser /usr/share/nginx/html

EXPOSE 80

# Validación de salud del contenedor (Excelente para ECS/Fargate)
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -q --spider http://127.0.0.1/ || exit 1