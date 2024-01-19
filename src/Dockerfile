FROM nginx:1.25-alpine
USER nginx
WORKDIR /usr/share/nginx/html/{{PROJECT_NAME}}
COPY ./{{PROJECT_NAME}} .
USER root