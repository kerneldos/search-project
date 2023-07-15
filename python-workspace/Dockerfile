FROM python:3.11-alpine

ARG TZ='UTC'

# https://wiki.alpinelinux.org/wiki/Setting_the_timezone
RUN echo "${TZ}" && apk --update add tzdata && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    apk del tzdata

RUN apk add --update --no-cache bash wget git openssh-client

RUN apk add --update --no-cache --virtual build-essentials \
    icu-dev icu-libs zlib-dev g++ make automake autoconf libzip-dev

WORKDIR /usr/src/app

RUN pip install --no-cache-dir flask gunicorn langchain openai

COPY . .

CMD [ "gunicorn", "-b", "0.0.0.0:8000", "langchain-search:app" ]