FROM node:22-alpine AS builder
WORKDIR /src
RUN npm install -g pnpm@10
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile
RUN node node_modules/.pnpm/vue-demi@*/node_modules/vue-demi/scripts/postinstall.js
COPY . .
RUN pnpm run build

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
