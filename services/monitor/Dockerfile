# Needs to be run from the project root context
FROM node:18.18.2-bullseye as builder
RUN mkdir -p /home/app
WORKDIR /home/app

COPY  . .

RUN npm ci --workspace=sourcify-monitor --include-workspace-root
RUN npx lerna run build --scope sourcify-monitor

######################
## Production image ##
######################
FROM node:18.18.2-bullseye-slim as production

RUN mkdir -p /home/app/services/monitor

WORKDIR /home/app/services/monitor
COPY services/monitor/package.json ./package.json
COPY services/monitor/chains.json ./chains.json

RUN npm install --omit=dev 
COPY --from=builder /home/app/services/monitor/dist ./dist

LABEL org.opencontainers.image.source https://github.com/ethereum/sourcify
LABEL org.opencontainers.image.licenses MIT

ARG ALCHEMY_API_KEY
ARG INFURA_API_KEY
ARG CF_ACCESS_CLIENT_ID
ARG CF_ACCESS_CLIENT_SECRET

# Set default value for ARG
ARG NODE_ENV=production

# Set environment variable
ENV NODE_ENV=${NODE_ENV}

WORKDIR /home/app/services/monitor

CMD ["node", "dist/index.js", "--chainsPath", "chains.json" ]
