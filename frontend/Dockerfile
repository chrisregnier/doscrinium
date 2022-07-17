# syntax=docker/dockerfile:1
FROM node:16 as build

WORKDIR /app

COPY package*.json .
RUN npm install

COPY . .
RUN npm run build


FROM nginx
COPY nginx/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html