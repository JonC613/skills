FROM node:22-alpine AS build
WORKDIR /app
COPY src/SkillCatalog/web/package*.json ./
RUN npm ci
COPY src/SkillCatalog/web ./
RUN npm run build

FROM nginx:1.27-alpine
COPY src/SkillCatalog/web/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
