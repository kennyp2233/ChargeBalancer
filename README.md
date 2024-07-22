# Práctica de Verificación de Rendimiento

Este proyecto configura un entorno de pruebas de rendimiento utilizando Docker Compose con Nginx como balanceador de carga y Apache Bench (`ab`) para realizar las pruebas de carga. El entorno está compuesto por dos servicios web y un servicio de balanceo de carga Nginx. Apache Bench se utiliza para medir el rendimiento del balanceador de carga.

## Estructura del Proyecto

La estructura del proyecto es la siguiente:
```bash
performance-test/
│
├── docker-compose.yml
├── nginx/
│ ├── Dockerfile
│ ├── nginx.conf
├── web/
│ ├── Dockerfile
│ └── index.html
└── ab/
└── Dockerfile
```

### Archivos

- **`docker-compose.yml`**: Archivo de configuración para Docker Compose.
- **`nginx/`**: Directorio con el Dockerfile y configuración de Nginx.
- **`web/`**: Directorio con el Dockerfile y contenido estático de las páginas web.
- **`ab/`**: Directorio con el Dockerfile para instalar Apache Bench.

## Requisitos

- Docker
- Docker Compose

## Explicación de Docker Compose y Dockerfiles

### Docker Compose

Docker Compose es una herramienta para definir y ejecutar aplicaciones Docker multi-contenedor. Utiliza un archivo YAML (`docker-compose.yml`) para configurar los servicios, redes y volúmenes necesarios para una aplicación. Con Docker Compose, puedes definir todos los servicios que tu aplicación necesita, construir sus imágenes, y levantar los contenedores con un solo comando.

En este proyecto, el archivo `docker-compose.yml` define cuatro servicios:

- **`web1` y `web2`**: Servicios que construyen imágenes para servidores web utilizando la configuración del directorio `web`.
- **`nginx`**: Servicio que construye una imagen de Nginx para actuar como balanceador de carga, utilizando la configuración del directorio `nginx`.
- **`ab`**: Servicio que construye una imagen con Apache Bench (`ab`) instalado para realizar pruebas de rendimiento.

Cada servicio está conectado a una red llamada `webnet`, lo que permite la comunicación entre contenedores.

### Dockerfiles

Los Dockerfiles son archivos de texto que contienen instrucciones para construir una imagen Docker. Cada Dockerfile especifica cómo se debe construir la imagen, incluyendo la base de la imagen, las dependencias a instalar, y los comandos a ejecutar.

En este proyecto, tienes varios Dockerfiles:

- **`nginx/Dockerfile`**: Define la imagen de Nginx. Parte de la imagen base `nginx:alpine` y copia el archivo de configuración `nginx.conf` al contenedor.

    ```Dockerfile
    FROM nginx:alpine
    COPY nginx.conf /etc/nginx/nginx.conf
    ```

- **`web/Dockerfile`**: Define la imagen para los servidores web. Utiliza la imagen base `nginx:alpine` y copia un archivo `index.html` al directorio de contenido estático de Nginx.

    ```Dockerfile
    FROM nginx:alpine
    COPY index.html /usr/share/nginx/html/index.html
    ```

- **`ab/Dockerfile`**: Define la imagen para ejecutar Apache Bench. Parte de la imagen base `nginx:alpine` y añade `apache2-utils`, que incluye Apache Bench.

    ```Dockerfile
    FROM nginx:alpine
    RUN apk add --no-cache apache2-utils
    CMD ["sh", "-c", "while true; do sleep 30; done;"]
    ```

Cada Dockerfile está diseñado para crear un contenedor con la configuración y herramientas necesarias para su respectivo servicio. Al construir las imágenes a partir de estos Dockerfiles, Docker crea contenedores que proporcionan la funcionalidad definida en cada uno.

Estas herramientas permiten configurar un entorno completo para pruebas de rendimiento de manera reproducible y aislada.


## Instalación y Ejecución

1. **Clona el repositorio:**

    ```bash
    git clone <url_del_repositorio>
    cd performance-test
    ```

2. **Construye y levanta los contenedores:**

    ```bash
    docker-compose up --build -d
    ```

    Esto construirá las imágenes de los contenedores y los levantará en segundo plano.

3. **Verifica que los contenedores están corriendo:**

    ```bash
    docker ps
    ```

    Deberías ver algo como esto:

    ```plaintext
    CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                               NAMES
    123456789abc   performance-test_ab        "sh -c 'while true; …"   10 minutes ago   Up 10 minutes                                       performance-test_ab_1
    23456789bcde   performance-test_nginx      "/docker-entrypoint.…"  10 minutes ago   Up 10 minutes   0.0.0.0:80->80/tcp                performance-test_nginx_1
    3456789cdefg   performance-test_web        "/docker-entrypoint.…"  10 minutes ago   Up 10 minutes                                       performance-test_web_1
    456789defgh   performance-test_web        "/docker-entrypoint.…"  10 minutes ago   Up 10 minutes                                       performance-test_web_2
    ```

4. **Accede al contenedor `ab` para ejecutar Apache Bench:**

    ```bash
    docker exec -it performance-test_ab_1 sh
    ```

5. **Ejecuta las pruebas de rendimiento:**

    ```bash
    ab -n 1000 -c 10 http://nginx/
    ```

    Esto enviará 1000 solicitudes al balanceador de carga Nginx con una concurrencia de 10 solicitudes simultáneas.

## Descripción de Archivos

- **`docker-compose.yml`**: Configura los servicios `web1`, `web2`, `nginx` y `ab`. Conecta todos los servicios en una red llamada `webnet`.
- **`nginx/nginx.conf`**: Configuración de Nginx para balanceo de carga entre los servicios `web1` y `web2`.
- **`nginx/Dockerfile`**: Dockerfile para construir la imagen de Nginx con la configuración personalizada.
- **`web/Dockerfile`**: Dockerfile para construir la imagen de los servidores web que sirven contenido estático.
- **`ab/Dockerfile`**: Dockerfile para construir la imagen con Apache Bench instalado.

## Resultados

Después de ejecutar el comando `ab`, revisa la salida en la terminal del contenedor `ab` para obtener detalles sobre el rendimiento, como el número de solicitudes procesadas por segundo, tiempos de respuesta y otros indicadores clave.

## Limpieza

Para detener y eliminar los contenedores y redes creados, ejecuta:

```bash
docker-compose down
```
# Imagen de prueba
![alt text](image.png)
