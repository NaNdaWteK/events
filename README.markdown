# Ruby on rails

1.- Add this Dockerfile and docker-compose.yml

```
FROM ruby:2.4.2

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

RUN mkdir /myapp
WORKDIR /myapp

RUN gem install bundler --pre

ADD . /myapp
```

```
version: '2'
services:
  web:
    build: .
    volumes:
      - .:/myapp
    ports:
      - '3000:3000'
```
