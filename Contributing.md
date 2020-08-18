## Contributing

### Setup Development Env  
* Prerequisites:
    * Docker & docker-compose

* Setup Dev Env:
    * navigate inside a terminal to a folder where you want to clone the cookbook repo and run: `mkdir custom_apps`  
    * clone the repo from https://github.com/nextcloud/cookbook into the `custom_apps` folder  
    * create a docker-compose.yml somewhere and copy & paste following into it     
        ```
        version: "3.4"

        networks: 
            example-com--postgres: ~
            example-com--redis: ~

        services:
            # PostgreSQL
            postgres: 
                container_name: example-com--postgres
                environment: 
                    - POSTGRES_PASSWORD=somepassword
                    - POSTGRES_USER=someuser
                image: postgres:latest
                restart: always
                volumes: 
                    - "./postgres/init:/docker-entrypoint-initdb.d/"
                    - "./postgres/data:/var/lib/postgresql/data"
                    - "/etc/localtime:/etc/localtime:ro"
                networks: 
                    - example-com--postgres

            # NextCloud
            nextcloud: 
                container_name: example-com--nextcloud
                depends_on: 
                - postgres
                image: nextcloud:latest
                restart: always
                ports:
                    - 1000:80
                volumes: 
                    - "ADJUSTPATH/custom_apps:/var/www/html/custom_apps"
                    - "/etc/localtime:/etc/localtime:ro"
                networks: 
                    - example-com--postgres
                    - example-com--redis

            # Redis
            redis:
                container_name: example-com--redis
                image: redis:latest
                restart: always
                networks:
                    - example-com--redis
    * adjust the path of the nextcloud docker container volume to match your path to the `custom_apps` folder (marked as ADJUSTPATH). E.g.: `/Documents/cookbook/custom_apps`
    * navigate to the cookbook folder from the cloned repo and run: `npm install && make`
    * start docker and add the `custom_apps` folder as shared resource
* Start Dev Env:  
    * run: `docker-compose up --build -d`
    * run: `npm run dev` at `/custom_apps/cookbook`

* Troubleshooting:
    * on MacOS the docker image is currently only working with docker version 17.09.1-ce-mac42 (21090)!!!!
    * on MacOS you'll need to delete `/private` as shared resource and add `/etc/localtime`
        * open Preferences --> Resources --> press blue + and choose etc folder
