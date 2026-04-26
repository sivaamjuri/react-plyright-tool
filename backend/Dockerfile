FROM mcr.microsoft.com/playwright:v1.50.0-jammy

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

# Expose the port the app runs on (Assuming 3000, adjust if needed)
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
