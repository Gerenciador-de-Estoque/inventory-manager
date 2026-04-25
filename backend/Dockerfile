FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build && mkdir -p public/avatars
EXPOSE 3001
CMD ["sh", "-c", "npx drizzle-kit migrate && npx tsx src/server.ts"]
