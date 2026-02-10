# 1) Build React app (CRA 4 works best on Node 16)
FROM node:16-alpine AS build
WORKDIR /app

COPY package*.json ./
RUN npm install --legacy-peer-deps

COPY . .
RUN npm run build

# 2) Serve with Nginx
FROM nginx:alpine

# SPA routing
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]


