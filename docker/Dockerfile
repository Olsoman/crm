# syntax=docker/dockerfile:1.4

# ─── Builder Stage ───────────────────────────────────────────────────
FROM node:18-alpine AS builder
WORKDIR /app

# Install root-level dependencies (if needed)
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile

# Copy the entire repo, including sites/
COPY . .

# Copy the common_site_config.json into the frontend src so Vite can bundle it
RUN cp sites/common_site_config.json frontend/src/common_site_config.json

# Build the frontend
RUN cd frontend \
    && yarn install --frozen-lockfile \
    && yarn build


# ─── Production Stage ────────────────────────────────────────────────
FROM node:18-alpine AS runner
WORKDIR /app

# Install only production deps
COPY package.json yarn.lock ./
RUN yarn install --production --frozen-lockfile

# Copy built assets from builder
COPY --from=builder /app/frontend/dist ./dist

# Expose port (adjust if different)
EXPOSE 3000

# Set production env
ENV NODE_ENV=production

# Start the app (ensure you have a start script or use a static server)
CMD ["npx", "serve", "-s", "dist"]