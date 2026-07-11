# Use a Node.js image for building the app
FROM node:22-alpine3.21 AS build
WORKDIR /app

# Copy package files and install dependencies
COPY package.json package-lock.json ./
RUN npm install --frozen-lockfile

# Copy the rest of the project files
COPY . .

# Build the application for production
RUN npm run build

# Use Nginx for serving the built frontend
FROM nginx:stable-alpine3.20-slim
WORKDIR /usr/share/nginx/html

# Remove the default Nginx static files
RUN rm -rf ./*

# Copy the built frontend from the previous stage
COPY --from=build /app/dist .

# Custom config so React Router routes don't 404 on refresh
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose the port Nginx listens on
EXPOSE 80

HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
  CMD wget -q --spider http://127.0.0.1:80 || exit 1

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
