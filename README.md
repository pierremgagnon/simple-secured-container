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

[![N|Solid](https://github.com/pierremgagnon/simple-secured-container/blob/master/Diagram.png)](https://github.com/pierremgagnon/simple-secured-container/blob/master/Diagram.png)

# Deployment instructions
 
 # 1. Build the confidential content image
 
 This simple docker image is based on Nginx a light Web Server Easily conterizable.
 The source image is locate in the "web" folder.
 Simply build the image by by going to the root folder of the solution and type :
 docker build proxy
