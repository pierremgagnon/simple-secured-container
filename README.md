# Simple-Secured-Container
Demonstrate how to secure any web content through a reverse proxy container

This solution we demontrate here is composed of three components :
  - A container Engine (In this case Docker)
  
  - A simple (unsecured) containerized Web site that host confidential content
  
  - A reverse proxy container that will:
      - Handle End-Users connexions over SSL, 
      - Authenticate them,
      - Redirect a sub-segment of a requested URL to the specific confidential Web Site
      
Here is the diagram of the solution:

<img src="https://github.com/pierremgagnon/simple-secured-container/blob/master/Diagram.png" width="600">

# Deployment instructions
 
 # 1. Build the confidential content image
 
 This simple docker image is based on Nginx a light Web Server Easily conterizable.
 The source image is locate in the "web" folder.
 Simply build the image by by going to the root folder of the solution and type :
 > *docker build -t web web*
 
 The docker file describing the image contains :
  - the reference to the official Nginx:latest image
  - a integration of the welcome page (confidential content) a the default home page

As we did not make any change to the Nginx configuration, the server will listen on port 80 and use the default home page (./usr/share/nginx/html) 

 # 2. Build the reverse proxy image
 
 Again, we will create an Nginx based image, as Nginx has also proxy/reverse proxy capabilities.
 The source image is locate in the "proxy" folder.
 Simply build the image by going to the root folder of the solution and type :
 > *docker build -t proxy proxy*
 
 The docker file describing the image contains :
  - the reference to the official Nginx:latest image
  - a bunch of file integration mostly for Nginx configuration:
    - ./etc/nginx/sites-enabled/proxy.conf
    - ./etc/nginx/conf.d/default.conf 
    - ./etc/ssl/certs/localhost.crt
    - ./etc/ssl/private/localhost.key
    - ./etc/nginx/.htpasswd

 # 3. Deploy the solution using Docker-compose
 
To deploy the solution, go to the root folder of the solution and type :
> *docker compose up -d*

**docker-compose.yml structure**
 
<img src="https://github.com/pierremgagnon/simple-secured-container/blob/master/dc.png" width="205">
 
The *service:* section defines the containers to include in the solution and their configuration 

``` proxy:
    container_name: runningproxy
    hostname: proxy
    image: proxy
    ports:
      - 443:443
    networks:
      - backend
      - frontend
```
This container will run proxy image, use proxy hostname, expose only ssl port 443 and be connected to backend and frontend networks

``` web:
    container_name: runningweb
    hostname: web
    image: web
    networks:
      - backend
```
This container will run web image, use web hostname (used in previous reverse proxy config), will only be connected to the backend networks and will not expose any port to the outside. 
Indeed we secure network protocols by having internal proxy->web communication occuring internally and on a dedicated docker network.

Theses networks defined in this section:      
```networks:
  frontend:
  backend:
```


