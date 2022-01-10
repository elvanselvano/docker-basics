# We can extend/change an existing official image from Docker Hub
# We don't have to specify EXPOSE or CMD because they're in the FROM

# Beyond dev, we should pin the versions
FROM nginx:latest

# Change working dir to root of nginx webhost
# WORKDIR is preferred compared to 'RUN cd /some/path'
WORKDIR /usr/share/nginx/html

COPY index.html index.html