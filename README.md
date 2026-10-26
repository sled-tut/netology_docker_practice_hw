# netology_docker_practice_hw
Практическое задание по занятию "Практическое применение Docker"

## Задача 1
Dockerfile.python:
```
ROM python:3.12-slim as builder

WORKDIR /app

RUN python -m venv /opt/venv

ENV PATH="/opt/venv/bin:$PATH"

ENV PATH="/opt/venv/bin:$PATH"

COPY requirements.txt .

RUN pip install -r requirements.txt

FROM python:3.12-slim as runtime

WORKDIR /app

ENV PATH="/opt/venv/bin:$PATH"

COPY --from=builder /opt/venv /opt/venv

COPY . .

EXPOSE 5000

# Запускаем приложение с помощью uvicorn, делая его доступным по сети

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]
```
.dockerignore:
```
.git
.gitignore
__pycache__
.env
venv
env
```

## Задача 3
compose.yaml:
```
version: '3'
include:
  - proxy.yaml

services:
  db:
    image: mysql
    container_name: db
    restart: on-failure
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=app
    env_file:
      - .env
    networks:
      backend:
        ipv4_address: 172.20.0.10
    volumes:
      - mysql_data:/var/lib/mysql

  web:
    build:
      context: .
      dockerfile: Dockerfile.python
    container_name: web
    networks:
      backend:
        ipv4_address: 172.20.0.5
    environment:
      - DB_HOST=db
      - DB_USER=app
      - DB_PASSWORD=${MYSQL_PASSWORD}
      - DB_NAME=${MYSQL_DATABASE}
    restart: on-failure
    depends_on:
      - db

networks:
  backend:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

volumes:
  mysql_data:
```
Запущенные контейнеры проекта:
![](images/задача%203%202.png)
SQL-запрос:
![](images/задача%203%203.png)

## Задача 4
Поскольку ресурсы Yandex Cloud практически недоступны, я арендовала VPS c публичным IP-адресом 168.113.173.249.
https://github.com/sled-tut/shvirtd-example-python


## Задача 6
![](images/задача%206%201.png)
![](images/задача%206%202.png)
![](images/задача%206%203.png)
