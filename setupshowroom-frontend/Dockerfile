FROM --platform=$BUILDPLATFORM node:20-alpine AS build

ARG TARGETPLATFORM
ARG BUILDPLATFORM

WORKDIR /app

RUN npm install -g pnpm

COPY package.json pnpm-lock.yaml ./

RUN pnpm install --frozen-lockfile

COPY . .

RUN pnpm run build --configuration production

FROM nginx:1.25-alpine

COPY nginx.conf /etc/nginx/nginx.conf

COPY --from=build /app/dist/browser /usr/share/nginx/html

RUN apk add --no-cache curl

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=10s --start-period=10s --retries=3 \
  CMD curl -f http://localhost/health || exit 1

CMD ["nginx", "-g", "daemon off;"]
