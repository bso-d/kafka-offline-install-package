# Stage 1: build the React frontend
FROM node:20-alpine AS frontend-build
WORKDIR /build
COPY frontend/package.json ./
RUN npm install
COPY frontend/ .
# override outDir so the built assets land in a known location
RUN VITE_BASE=/visualizer/ npx vite build --outDir /frontend-dist

# Stage 2: production Node server
FROM node:20-alpine
WORKDIR /app
COPY backend/package.json ./
RUN npm install --production
COPY backend/ .
COPY --from=frontend-build /frontend-dist ./public
COPY topology.config.json .
ENV PORT=3001
ENV TOPOLOGY_CONFIG=/app/topology.config.json
EXPOSE 3001

# Run as non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:3001/ || exit 1

CMD ["node", "server.js"]
