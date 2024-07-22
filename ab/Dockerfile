# ab/Dockerfile
FROM nginx:alpine

# Instala Apache Bench (ab)
RUN apk add --no-cache apache2-utils

# Define el comando predeterminado para mantener el contenedor activo
CMD ["sh", "-c", "while true; do sleep 30; done;"]
