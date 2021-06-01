- Setup Strapi CMS on Docker
- Step 9:
  
  **Creating a Batch File to List Both Docker Images and Containers**
  
     @echo off
     docker image ls
     pause
     docker container ls
  
  **Executing MongoDB in Docker Container**
  
     docker run -e MONGO_INITDB_DATABASE=strapi --name strapi-mongo -d mongo
  
  **Executing Strapi and Linking to MongoDB in Docker Container**
  
     docker run -e APP_NAME=strapi-app -e DATABASE_CLIENT=mongo -e DATABASE_HOST=strapi-mongo -e DATABASE_PORT=27017 -e DATABASE_NAME=strapi --link strapi-mongo:mongo -p 1337:1337 --name strapi -d strapi/strapi
  
  **Creating a Batch File to Execute Both MongoDB and Strapi in Docker Containers**
  
     @echo off
     docker run -e MONGO_INITDB_DATABASE=strapi --name strapi-mongo -d mongo
     pause
     docker run -e APP_NAME=strapi-app -e DATABASE_CLIENT=mongo -e DATABASE_HOST=strapi-mongo -e DATABASE_PORT=27017 -e DATABASE_NAME=strapi --link strapi-mongo:mongo -p 1337:1337 --name strapi -d strapi/strapi
- Step 10: Congratulations! You Successfully Created Your Strapi Application
  
  You may now continue to the next article "**Creating Content Placeholders, Types, and Relations in Strapi**".
  
  ![][1]
  
  [1]: images/setup-strapi-cms-on-docker/step-10--congratulations--you-successfully-created-your-strapi-application.png
  
  ![][2]
  
  [2]: images/setup-strapi-cms-on-docker/264ceffe-7514-4c14-acdd-6ce223899861.png