# ComposeLaravel
Learn how to run a laravel project with docker-compose

# Docker-compose.yml
[Creating a laravel project trough dockerfiles](https://github.com/CodeCatalyzer/LAMP-Stack)
For the first assignment i had to acces a laravel project trough different dockerfiles. Problem with this is that you have to run
a lot of commands before you can start and it's very easy to make a typo. Therefore docker has invented the docker compose.
Inside a docker-compose.yml file you can declare multiple images and run the application with a single command.

To make it clear we will to this step by step.

## Version
At first we have to start with declaring with version of docker compose we want to use. I have used version 3.
```php
version: '3'
```

## Network
Now we will create the network where all of the images will be linked together.
```php
# Setting up the network
networks:
  myNetwork:
    driver: bridge
  ```
  
  Really easy for now right? Well it will not get much harder
  #Services
  Services are the components of the container, just imagine them as different pieces of lego building something.
  ## PHP / laravel
  ```php
  laravel:
   # Name of the container
    container_name: myapp
   # builds the Dockerfile inside the root directory. It's the same Dockerfile as we used in the last assignment
    build: ./
   # Makes the container use the myNetwork network
    networks:
      - myNetwork
   # Only run this container, after the mysql, node and composer have been runned. This makes sure the application doesnt go live without, for      example, having a connection with the database
    depends_on:
      - mysql
      - node
      - compose
     # Updates everytime the code changes.
    volumes:
      - ./src/:/var/www/html/
      # Listens to port 8080:80
    ports:
      - 8080:80
    ```q
    
    ## MySQL

  
