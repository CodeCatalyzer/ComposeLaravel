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
    # Stores the data in the mysql folder inside the application. This way the data isn't lost everytime you run the application.
    volumes:
      - ./mysql:/var/lib/mysql
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
  ## Composer  
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
  
 The complete code will look like this: 
```php
 version: '3'

# Setting up the network
networks:
  myNetwork:
    driver: bridge
    

# Setting up the services (containers)
services:
  laravel:
    container_name: myapp
    build: ./
    networks:
      - myNetwork
    depends_on:
      - mysql
      - node
      - composer
    volumes:
      - ./src/:/var/www/html/
    ports:
      - 8080:80

  mysql:
    container_name: aServer
    image: mysql:8.0
    networks:
      - myNetwork
    environment:
      - "MYSQL_ROOT_PASSWORD=aPassword"
      - "MYSQL_DATABASE=aDatabase"
    volumes:
      - ./mysql:/var/lib/mysql
    ports:
      - 3306:3306
    expose:
      - 3306

  node:
    container_name: node
    image: node:latest
    networks:
      - myNetwork
    volumes:
      - ./src/:/var/app/
    working_dir: /var/app/
    command: npm install && npm run dev

  composer:
    container_name: composer
    image: composer:latest
    volumes:
      - ./src/:/var/app/
    working_dir: /var/app/
    command: composer install
  ```
  Now we can run the application by running the command:  `docker compose up`
![afbeelding](https://github.com/CodeCatalyzer/ComposeLaravel/assets/112801788/a6fc45c4-024c-4edd-b661-118b505b44ff)  
Your docker desktop app will look like this: 
![afbeelding](https://github.com/CodeCatalyzer/ComposeLaravel/assets/112801788/afaa6b78-1910-478b-b85c-2847f941101c)

![afbeelding](https://github.com/CodeCatalyzer/ComposeLaravel/assets/112801788/91fe40f1-b6ce-4c24-8509-9dec19d0409d)
![afbeelding](https://github.com/CodeCatalyzer/ComposeLaravel/assets/112801788/afe7b317-2a98-420c-bc37-a04a2f01582a)

To stop, run the command: `docker compose down`
The moment you want to start programming again you only need to run `docker compose up` and all the data and code will be back.

Now the node and composer files will exite after they have run the commands. Adding `sleep infinity`
