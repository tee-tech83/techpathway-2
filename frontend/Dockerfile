# 1) Build React app (use Node 18 for openssl-legacy-provider)
FROM node:18-alpine AS build
WORKDIR /app

COPY package*.json ./
RUN npm install --legacy-peer-deps

COPY . .

# CRA + webpack needs this on Node 17+
ENV NODE_OPTIONS=--openssl-legacy-provider
RUN npm run build

# 2) Serve with Nginx
FROM nginx:alpine

# SPA routing (important for React)
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
