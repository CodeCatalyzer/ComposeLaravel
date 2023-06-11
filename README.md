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
  # Services
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
   # Only run this container, after the mysql, node and composer have been runned. This makes sure the application doesnt go live without,
   for example, having a connection with the database
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
  ```
  ## Mysql
  
  ```php
  mysql:
  # Set container name to aServer
    container_name: aServer
    # Use the mysql:8.0 image
    image: mysql:8.0
    # Use the myNetwork network
    networks:
      - myNetwork
    # Set the environment variables password and database
    environment:
      - "MYSQL_ROOT_PASSWORD=aPassword"
      - "MYSQL_DATABASE=aDatabase"
     # Sets the port to 3306:3306
    ports:
      - 3306:3306
     # Makes the database accesible from third party DBM applications
    expose:
      - 3306
  ```
  ## Node
  ```php
  node:
    # Sets the container name to node
    container_name: node
    # Sets the image as the latest node image
    image: node:latest
    # Make use of the myNetwork network
    networks:
      - myNetwork
    # Updates everytime something changes in - ./src/:/var/app/
    volumes:
      - ./src/:/var/app/
    # Sets the working direction to /var/app/
    working_dir: /var/app/
    # Runs the commands npm install and npm run dev
    command: npm install && npm run dev
  ```
  ##Composer
  Same way as node:
  ```php
   composer:
    container_name: composer
    image: composer:latest
    volumes:
      - ./src/:/var/app/
    working_dir: /var/app/
    command: composer install
  ```
  
 
  
