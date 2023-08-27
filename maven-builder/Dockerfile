FROM maven:3-eclipse-temurin-17-alpine
RUN --mount=type=cache,target=/var/cache/apk,sharing=locked \
    apk update && \
    apk add git && \
    apk add docker-cli-buildx
