FROM python:3.9-slim

LABEL maintainer="michel.pouleijn@simac.com"

ENV PARSEDMARC_VERSION 8.3.2

RUN groupadd --gid 1000 parsedmarc \
  && useradd --uid 1000 --gid parsedmarc --shell /bin/bash --create-home parsedmarc

WORKDIR /home/parsedmarc
ENTRYPOINT [ "parsedmarc" ]

RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y libxml2-dev libxslt-dev python-dev --no-install-recommends \
    && pip install -U parsedmarc==$PARSEDMARC_VERSION \
    && apt-get purge -y gcc libxml2-dev libxslt-dev libz-dev \
    && apt-get autoremove -y --purge \
    && apt-get clean \
    && rm -rf /var/lib/{apt,dpkg}/ && rm -rf /root/.cache/

USER parsedmarc
