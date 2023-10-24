# Use the official Node.js image
FROM node

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy application code
COPY . .

# Expose the port your app runs on
EXPOSE 5005

# Start the application
CMD ["node", "app.js"]
