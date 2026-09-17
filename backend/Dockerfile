# ============================================================
# Stage 1: deps — install dependencies (incl. compiling bcrypt,
# which needs native build tools not present in a bare Alpine image)
# ============================================================
FROM node:20-alpine AS deps

WORKDIR /app

RUN apk add --no-cache python3 make g++

COPY package*.json ./
RUN npm ci --omit=dev

# ============================================================
# Stage 2: production — slim runtime image
# ============================================================
FROM node:20-alpine AS production

WORKDIR /app

RUN addgroup -g 1001 -S nodejs && \
    adduser -S playvora -u 1001

COPY --from=deps /app/node_modules ./node_modules

COPY --chown=playvora:nodejs . .

USER playvora

ENV NODE_ENV=production
EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:8000/health || exit 1

CMD ["node", "src/index.js"]