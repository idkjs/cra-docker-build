# Deploying a Create-React-App Docker Image with Digital Ocean

Thanks to [@shaneOsbourne](https://twitter.com/shaneosbourne?lang=en)'s for sharing his [article](https://medium.com/@shakyShane/lets-talk-about-docker-artifacts-27454560384f)

The take away that I needed was about build the image locally, pushing to some docker repository or git and building the image from the dockerfile AFTER you created the image locally.

## Running

I got this to run by pushing to hub.docker.com then pulling into the Digital Ocean container then:

```
$ docker pull idkjs/dockerbuild
$ docker run -p 8080:80 idkjs/docker-build
```

The image will running at http://droplet.ip.adddress:8080.

## Dockerfile

This is a multistage build which is exsposing port 80 on the image container to be available. When we run the container with `docker run -p 8080:80 idkjs/docker-build` we are mapping the digitalocean container's outgoing 8080 port to image containers 80 port so anything that hits http;//ip.address:8080 while be accessing 80 on the running container. Not sure about the terminology.

```
FROM node:9.8-slim as build-deps
WORKDIR /usr/src/app
COPY package.json yarn.lock ./
RUN yarn
COPY . ./
RUN yarn build

FROM nginx:1.13.12-alpine
COPY --from=build-deps /usr/src/app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
