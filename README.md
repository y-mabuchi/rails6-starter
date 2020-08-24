# Rails6-Starter

Rails6-Starter is Docker container for Ruby on Rails 6.  
I refered to [【Rails6】Docker+Rails6+puma+nginx+mysql【環境構築＊初心者必見】](https://qiita.com/kohki4115/items/c37ff8550b01bbc75df2).

# Requirement

* Docker 2.3.0.

# Installation

## Clone repository
```bash
git clone git@github.com:y-mabuchi/rails6-starter.git
```

## Create rails files
``` bash
cd rails6-starter
docker-compose run --rm app rails new . --force --database=mysql --skip-bundle
```

## Edit puma.rb(/config/puma.rb)
```puma.rb
threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }.to_i
threads threads_count, threads_count
port        ENV.fetch("PORT") { 3000 }
environment ENV.fetch("RAILS_ENV") { "development" }
plugin :tmp_restart

app_root = File.expand_path("../..", __FILE__)
bind "unix://#{app_root}/tmp/sockets/puma.sock"

stdout_redirect "#{app_root}/log/puma.stdout.log", "#{app_root}/log/puma.stderr.log", true
```

## Edit database.yml(/config/database.yml)
```database.yml
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV.fetch('MYSQL_USER') { 'root' } %>
  password: <%= ENV.fetch('MYSQL_PASSWORD') { 'password' } %>
  host: db

development:
  <<: *default
  database: webapp_development

test:
  <<: *default
  database: webapp_test
```

## Build docker image
```bash
docker-compose build
```

## Start cotainer
```bash
docker-compose up -d
```

## Install webpacker
```bash
docker-compose exec app rails webpacker:install
```

## Change mod database user
```bash
docker-compose exec db bash
mysql -u root -p
GRANT ALL ON *.* TO 'user'@'%';
exit;
```

## Create DB
```
docker-compose exec app rails db:create
```

## Check your browser
[http://localhost](http://localhost)

## Update remote repository
```
git remote set-url origin {YOUR-NEW-REPOSITORY-URL}
```

# Author

* Yusuke Mabuchi
* mabu.you@gmail.com
