FROM node:18-alpine

WORKDIR /app

# 1. Copia pacotes e instala TODAS deps (dev + prod para build)
COPY package*.json ./
RUN npm ci

# 2. Copia fonte TS e tsconfig
COPY src/ ./src/
COPY tsconfig.json ./

# 3. BUILD TypeScript (cria build/)
RUN npm run build

# 4. Remove dev deps, instala só prod
RUN npm ci --only=production && npm cache clean --force

# 5. Copia build/ (agora existe!)
COPY build/ ./build/

# Logs e user security (inalterado)
RUN mkdir -p logs
RUN addgroup -g 1001 -S nodejs && \
    adduser -S espocrm -u 1001 -G nodejs
RUN chown -R espocrm:nodejs /app
USER espocrm

ENV NODE_ENV=production
ENV LOG_LEVEL=info

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "console.log('Health check OK')" || exit 1

EXPOSE 3000
CMD ["node", "build/index.js"]
