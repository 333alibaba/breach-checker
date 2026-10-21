FROM nginx:alpine
LABEL maintainer="anatola_dwornik"
LABEL author="Anatola Dwornik" data="2026-10-15"
RUN apk add --no-cache nodejs npm && npm install -g htmlhint
COPY . /usr/share/nginx/html
RUN date > /usr/share/nginx/html/build.txt
RUN htmlhint /usr/share/nginx/html/index.html
EXPOSE 80