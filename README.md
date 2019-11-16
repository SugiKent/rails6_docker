# Rails6の環境をDockerでいい感じに作る

## 環境構築手順

```
$ mkdir new_app
$ cd new_app
$ git clone git@github.com:SugiKent/rails6_docker.git
$ mv rails6_docker new_app
$ git grep -l 'myapp' | xargs sed -i '' -e 's/myapp/new_app/g'
$ docker-compose run web rails new . --force --no-deps --database=mysql --skip-test --webpacker
//=> そこそこ時間かかる

$ docker-compose build
```

### database.ymlの書き換え

`$ vim config/database.yml`

```
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV.fetch("MYSQL_USERNAME", "root") %>
  password: <%= ENV.fetch("MYSQL_PASSWORD", "password") %>
  host: <%= ENV.fetch("MYSQL_HOST", "db") %>

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp_production
  username: myapp
  password: <%= ENV['MYAPP_DATABASE_PASSWORD'] %>
```

```
$ find config -name "*.yml" | xargs grep -rl "myapp" | xargs sed -i "" -e "s/myapp/new_app/g"
$ docker-compose run web rails db:create
```

### コンテナ起動

`$ docker-compose up`

http://localhost:3000 にアクセスできるはず

## 環境構築後

```
$ chmod 755 start-docker.sh ←一度だけ実行
$ git grep -l 'rails6_docker' | xargs sed -i '' -e 's/rails6_docker/new_app/g'
```

以降は `$ ./start-docker.sh` でいい感じに立ち上がる
