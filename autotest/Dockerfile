FROM busybox as downloader

RUN cd /tmp && wget -c https://github.com/hyperledger/cello/archive/master.zip && \
    unzip master.zip

FROM circleci/node:latest-browsers as builder

LABEL maintainer="github.com/hyperledger/cello"

WORKDIR /usr/src/app/
USER root
COPY --from=downloader /tmp/cello-master/src/dashboard ./
RUN yarn
RUN yarn run build

FROM nginx:1.15.12

COPY --from=builder /usr/src/app/dist /usr/share/nginx/html
COPY --from=downloader /tmp/cello-master/build_image/docker/common/dashboard/config-nginx.sh /
RUN chmod +x /config-nginx.sh
COPY --from=downloader /tmp/cello-master/build_image/docker/common/dashboard/default.conf.tmpl /etc/nginx/conf.d/default.conf.tmpl

EXPOSE 80

CMD ["bash", "-c", "/config-nginx.sh && nginx -g 'daemon off;'"]
