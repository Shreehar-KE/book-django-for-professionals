# Django for Professionals 4.0 - Willian S. Vincent

## Chapter 2: Docker Hello, World!
- `docker --version`
- `docker run hello-world`
- `docker info`
- Image: Read-Only Template, Container: Instance of an Image - similar to class & object
- *`dockerfile`* - steps to create an image
- `FROM, ENV, WORKDIR, COPY, RUN `
- *`.dockerignore`* - similar to *`gitignore`*
- `docker build .` - to build the image
- *`docker-compose.yml`* - list of instructions used to run the image as a container
    ```
    version: "3.9"
    services:
        web:
            build: .
            ports:
                - "8000:8000"
            command: python manage.py runserver 0.0.0.0:8000
            volumes:
                - .:/code
    ```
- `docker compose up` & `docker compose down` to start & stop the container