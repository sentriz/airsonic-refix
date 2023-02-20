FROM node:22-alpine AS builder
WORKDIR /src
COPY package.json yarn.lock ./
RUN yarn --pure-lockfile
COPY . .
RUN yarn run build

FROM nginx:alpine
EXPOSE 80
RUN apk add --no-cache gettext
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf
COPY docker/env.js.template /env.js.template
COPY docker/docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh
COPY --from=builder /src/dist/ /var/www/html/
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
