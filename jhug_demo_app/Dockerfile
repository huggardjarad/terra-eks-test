FROM node:10-alpine as builder
COPY package*.json ./
RUN npm install

FROM node:10-alpine
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY --from=builder node_modules node_modules

ARG GIT_VERSION
ARG GIT_COMMIT

ENV VERSION=$GIT_VERSION
ENV LAST_COMMIT=$GIT_COMMIT

COPY . .
EXPOSE 8000
CMD ["npm","start"]
