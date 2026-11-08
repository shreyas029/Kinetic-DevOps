
# Use Node.js LTS
FROM node:22-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy dependency files
COPY package*.json ./

# Install production dependencies
RUN npm ci --omit=dev

# Copy backend application files
COPY . .

# Backend application port
EXPOSE 5000

# Start the Node.js server
CMD ["node", "server.js"]
