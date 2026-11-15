# No build stage — this repo is pure routing/rate-limit configuration, no
# application code to compile.
FROM nginx:1.27-alpine

COPY deploy/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
