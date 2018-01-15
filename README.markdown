# Ruby on rails

1.- Add this Dockerfile and docker-compose.yml

* Dockerfile

```
FROM ruby:2.4.2

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

RUN mkdir /myapp
WORKDIR /myapp

RUN gem install bundler --pre

ADD . /myapp
```

* docker-compose.yml

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

2.- Enter in docker container and install rails. Modify docker-compose and Dockerfile.

* Enter into container

`$ docker-compose run web bash`

* Install rails and create project

```
/myapp# gem install rails
/myapp# rails new . -T
```

```
FROM ruby:2.4.2

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

RUN mkdir /myapp
WORKDIR /myapp

RUN gem install bundler --pre

ADD . /myapp

RUN bundler install
```

```
version: '2'
services:
  web:
    build: .
    command: bundle exec rake db:migrate
    command: bundle exec rake db:seed
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/myapp
    ports:
      - '3000:3000'
```

* Build the project

`$ docker-compose up --build`

Rails is now running on port 3000

3.- Add rspec for rails

* Add rspec-rails into Gemfile make it with user permissions

`$ sudo chown -R $user ../your_path`

```
group :test do
  gem 'rspec-rails', '~> 3.7'
end
```

`$ docker-compose up --build`

* Initialize the spec/ directory

`$ docker-compose run web bash`

`/myapp# rails generate rspec:install`

* Add rspec configuration to .rspec

```
--require spec_helper
--format documentation
--order random
--color
```

* Add user permissions to spec folder

`$ sudo chown -R $user ../your_path`

4.- Integrate it with travis and test the system

* Create travis.yml

```
language: ruby
rvm:
  - 2.4.2
script:
  - bin/rake db:migrate RAILS_ENV=test
  - bundle exec rspec
```

* Test the system

spec/system_spec.rb

```
require 'rails_helper'

describe 'Test System' do
  it 'is working' do
    expect(true).to be true
  end
end
```
