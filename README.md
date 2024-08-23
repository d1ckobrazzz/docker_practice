# docker_practice


# Домашнее задание к занятию 5. «Практическое применение Docker»

## Задача 1
1. Сделайте в своем github пространстве fork репозитория ```https://github.com/netology-code/shvirtd-example-python/blob/main/README.md```.   
2. Создайте файл с именем ```Dockerfile.python``` для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ ```python:3.9-slim```. 
Обязательно используйте конструкцию ```COPY . .``` в Dockerfile. Не забудьте исключить ненужные в имадже файлы с помощью dockerignore. Протестируйте корректность сборки.  
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker в venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) По образцу предоставленного python кода внесите в него исправление для управления названием используемой таблицы через ENV переменную.
---

## Решение задачи 1
1. Ссылка на [fork](https://github.com/d1ckobrazzz/shvirtd-example-python)
2.
   ![task1_1](https://github.com/user-attachments/assets/d193a35d-8f3b-44ff-afa0-2fb9ea1cd277)


## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## Решение задачи 2
  ![task2](https://github.com/user-attachments/assets/5fb98f43-a727-41bb-9751-c8ea567638b4)


## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете ```NULL``` --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;```.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

## Решение задачи 3
   ### compose.yaml
   ![task3](https://github.com/user-attachments/assets/ec24c2ac-18ea-4285-b4f2-e12cbb828514)

   ### подключение к БД
   ![task3_3_1](https://github.com/user-attachments/assets/e370fa75-cffa-4b5f-9390-6a480a048251)

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. ПРИМЕЧАНИЕ: Приложение весьма вероятно упадет под нагрузкой, но успеет обработать часть запросов - этого достаточно.
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. В качестве ответа повторите  sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.

## Решение задачи 4
   ### проверка http подключений
   ![task4_6](https://github.com/user-attachments/assets/9cb07c4e-1b51-42d6-96d5-5d22e7dbd1f0)

   ### remote ssh context
   ![task4_5](https://github.com/user-attachments/assets/a67c6af2-d740-4d11-b3ec-1f7c8d9a4b31)

   ### bash-скрипт
   ```bash
#!/bin/bash

#обновление реп и установка git
sudo apt update && sudo apt install git -y

#переход в рабочий каталог

cd /opt
#cd /tmp/test

#загрузка репо
sudo git clone "https://github.com/d1ckobrazzz/shvirtd-example-python"

#переход в директорию проекта
cd shvirtd-example-python


#создание compose.yaml
echo "
include:
  - proxy.yaml

services:
  db:
    image: mysql:8
    restart: always
    networks:
        backend:
          ipv4_address: 172.20.0.10
    ports:
    - "127.0.0.1:3306:3306"
    expose:
      - "3306"
    env_file: ".env"
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      timeout: 6s
      retries: 10

  web:
#    image: cr.yandex/crpcl6r0cqu21rv4bcae/basic-app:test
    build:
      context: .
      dockerfile: Dockerfile.python
#    tags:
#      - "basic-app:selfbuild"
    restart: always
    networks:
      backend:
        ipv4_address: 172.20.0.5
    ports:
      - "127.0.0.1:5000:5000"
    expose:
      - "5000"
    env_file: ".env_app"
    depends_on:
        db:
            condition: service_healthy
" > compose.yaml

#создание env
echo "
DB_HOST="172.20.0.10"
DB_USER="app"
DB_PASSWORD="QwErTy1234"
DB_NAME="virtd"
" >  .env_app

#запуск compose

docker compose up -d
```

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Решение задачи 5

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

## Решение задачи 6
ID образа 909a9d4540ae
![task6_2](https://github.com/user-attachments/assets/ee290aeb-1732-4f4c-a5fa-005704a55191)

в интерфейсе dive видно, что искомый исполняемый файл находится на слое 2e4c7a391cbe470bdd510791978859db7e09e412dc2587c5b27451aef764cf1b
![task6_3](https://github.com/user-attachments/assets/a2744cbf-fa92-4489-9a49-d76ff699e8a3)

после чего он был успешно скопирован
