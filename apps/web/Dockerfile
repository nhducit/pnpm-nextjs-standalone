# Install dependencies only when needed
FROM node:16-alpine AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
# TODO: why curl doesn't work here
# the command is success but pnpm is not found
# RUN curl -fsSL https://get.pnpm.io/install.sh | sh -
RUN npm i -g pnpm@7
WORKDIR /app
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
# TODO: as the app scale we don't want to manage all the `web` dependencies need to be
# configured manually, are there any better way
# or we can depend on turborepo dependency graph to generate Dockerfile?
COPY apps/web/package.json apps/web/package.json
COPY packages/eslint-config-custom/package.json packages/eslint-config-custom/package.json
COPY packages/tsconfig/package.json packages/tsconfig/package.json
COPY packages/ui/package.json packages/ui/package.json
# TODO: Tried pnpm fetch the recommended command from document
# but nodules is not generated correctly
# TODO: try a faster way https://github.com/pnpm/pnpm/issues/3114#issuecomment-1036842493
RUN pnpm install

# Rebuild the source code only when needed
FROM node:16-alpine AS builder
WORKDIR /app
# TODO are there any any way to detect
COPY --from=deps /app/node_modules ./node_modules
COPY --from=deps /app/apps/web/node_modules ./app/apps/web/node_modules
# COPY --from=deps /app/packages/eslint-config-custom/node_modules ./app/packages/eslint-config-custom/node_modules
# COPY --from=deps /app/packages/tsconfig/node_modules ./app/packages/tsconfig/node_modules
# COPY --from=deps /app/packages/ui/node_modules ./app/packages/ui/node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
ENV NEXT_TELEMETRY_DISABLED 1

# TODO: why is pnpm is not found here?
# is it because pnpm is installed globally in deps stage
# and that global package is not brough to builder stage
RUN cd apps/web && yarn run build

# Production image, copy all the files and run next
FROM node:16-alpine AS runner
WORKDIR /app

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# You only need to copy next.config.js if you are NOT using the default configuration
COPY --from=builder /app/apps/web/next.config.js ./
COPY --from=builder /app/apps/web/public ./public
COPY --from=builder /app/apps/web/package.json ./package.json

# TODO: this is a experimental feature
# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]