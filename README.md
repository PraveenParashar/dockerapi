# dockerapi with mongo DB in Linux container Ubuntu 18.04 
https://docs.docker.com/engine/examples/dotnetcore/

dotnet --info

<h3>Docker File<h3>
Dockerfile
    <code>
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
 </code>
dockerignore file
https://docs.docker.com/engine/reference/builder/#dockerignore-file

<h3>Build and run the Docker image<h3>
$ docker build -t aspnetapp .
$ docker run -d -p 8080:80 --name myapp aspnetapp
View the web page running from a container

    Go to localhost:8080 to access your app in a web browser.
    If you are using the Nano Windows Container and have not updated to the Windows Creator Update there is a bug affecting how Windows 10 talks to Containers via “NAT” (Network Address Translation). You must hit the IP of the container directly. You can get the IP address of your container with the following steps:
        Run docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" myapp
        Copy the container IP address and paste into your browser. (For example, 172.16.240.197)


<h2>Docker Compose<h2>
 <code>
    version: '3.1'

services:

  mongo:
    container_name: mongo
    image: mongo
    restart: always
    volumes:
      - ${WEBAPP_STORAGE_HOME}/site:/data/db
      #- ./data:/data/db
    ports:
      - "27017:27017"

  web:
        build: .
        ports:
            - "8082:80"
            - "44348:443"
        depends_on:
            - mongo
        volumes:
            - ${HOME}/.microsoft/usersecrets/:/root/.microsoft/usersecrets
            - ${HOME}/.aspnet/https:/root/.aspnet/https/     
        
        links:
          - mongo

 </code>
<h2>appsetting<h2>

 <code>
{
  "ConnectionStrings": {
    "SupplementDB": "mongodb://mongo:27017"
  },

  
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
 </code>
