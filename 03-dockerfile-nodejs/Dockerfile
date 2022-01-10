# use this empty Dockerfile to build your assignment



# This dir contains a Node.js app, you need to get it running in a container

# use the instructions from developer below to create a working Dockerfile
# feel free to add command inline below or use a new file, up to you (but must be named Dockerfile)
# once Dockerfile builds correctly, start container locally to make sure it works on http://localhost
# then ensure image is named properly for your Docker Hub account with a new repo name
# push to Docker Hub, then go to https://hub.docker.com and verify
# then remove local image from cache
# then start a new container from your Hub image, and watch how it auto downloads and runs
# test again that it works at http://localhost


# Instructions from the app developer
# - you should use the 'node' official image, with the alpine 6.x branch (node:6-alpine)
FROM node:6-alpine
  #  yes this is a 2-year old image of node, but all official images are always
  #  available on Docker Hub forever, to ensure even old apps still work.
  #  It is common to still need to deploy old app versions, even years later.
# - this app listens on port 3000, but the container should launch on port 80
EXPOSE 3000
  #  so it will respond to http://localhost:80 on your computer
# - then it should use alpine package manager to install tini: 'apk add --update tini'
RUN apk add --update tini && mkdir -p /usr/src/app

WORKDIR /usr/src/app
COPY package.json package.json

RUN npm install && npm cache clean --force

COPY . .

CMD [ "/sbin/tini", "--", "node", "./bin/www"]


  
# - then it should create directory /usr/src/app for app files with 'mkdir -p /usr/src/app'
# - Node uses a "package manager", so it needs to copy in package.json file
# - then it needs to run 'npm install' to install dependencies from that file
# - to keep it clean and small, run 'npm cache clean --force' after above
# - then it needs to copy in all files from current directory
# - then it needs to start container with command '/sbin/tini -- node ./bin/www'

# docker build -t testnode .
# docker container run --rm -p 81:3000 testnode
