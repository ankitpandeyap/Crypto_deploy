# Stage 1: Build the React application
FROM node:18-alpine as builder

WORKDIR /app

# Copy package.json and package-lock.json (or yarn.lock)
COPY package.json ./
# If you use yarn: COPY yarn.lock ./

# Install dependencies
RUN npm install --frozen-lockfile
# If you use yarn: RUN yarn install --frozen-lockfile

# Copy the rest of the application code
COPY . .

# Build the React application for production
RUN npm run build

# Stage 2: Serve the React application with Nginx
FROM nginx:alpine

# Copy the Nginx default configuration and replace it with a simple config for React
# This Nginx config ensures all non-file paths fallback to index.html (important for React Router)
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Remove default Nginx welcome page
RUN rm -rf /usr/share/nginx/html/*

# Copy the built React app from the builder stage to Nginx's serving directory
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 80 (standard HTTP port for web servers)
EXPOSE 80

# Command to start Nginx
CMD ["nginx", "-g", "daemon off;"]