# KirillBuzan_infra_db
Homework-13
[![Build Status](https://travis-ci.org/Otus-DevOps-2017-11/KirillBuzan_infra_db.svg?branch=master)](https://travis-ci.org/Otus-DevOps-2017-11/KirillBuzan_infra_db)

Я никогда в жизни не был так рад зеленому тесту!!! Получилось! Я запомню эту домашнюю работу на всю жизнь. 
Немножко изменил предложенные команды:
1) Генерируем ключ для подключения по SSH
``` bash
ssh-keygen -t rsa -f gce_travis -C 'travis' -b 4096 N ''
```

2) Добавил созданный открытый ключ gce_travis.pub в gce -> Метаданные -> SSH-ключи
3) Сгенерировал новый сервисный ключ: Infra-2ed7216cd032.json
4) Подключил в travis репозиторий Otus-DevOps-2017-11/KirillBuzan_infra_db
5) Выполнил команды по шифрованию данных travis локально:
```bash
travis encrypt GCE_SERVICE_ACCOUNT_EMAIL='service-account@lucky-almanac.iam.gserviceaccount.com' --add
travis encrypt GCE_CREDENTIALS_FILE="$(pwd)/Infra-2ed7216cd032.json" --add
travis encrypt GCE_PROJECT_ID='lucky-almanac' --add
```
Сразу сохранить изменения в удаленный репозиторий не получилось. Хотя делал все как описано в документациях. Сгенерировал в GitHub в своем профиле токен: Settings -> Developer Settings -> Personal Access Tokens. 
К репозиторию подключался командой:
```bash
travis login --githun-token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
Подключение проходило успешно. Выдавалась ошибка при попытке внести изменения в .travis.yml: файл .travis.yml не найден. Над правами для токена шаманил, но ничего это не дало. Делал так:
```bash
travis encrypt GCE_PROJECT_ID='lucky-almanac' --add -r 'Otus-DevOps-2017-11/KirillBuzan_infra_db'
```
6) шифруем файлы
```bash
tar cvf secrets.tar Infra-2ed7216cd032.json gce_travis
```
После выполнения команды, создался файл travis_secrets.tar.enc который и следует использовать в репозитории о чем любезно предупреждает travis.

7) Добавляем в .travis.yml информацию о зашифрованном файле
```bash
travis encrypt-file secrets.tar --add
```

8) Так же в блок env необходимо добавить информацию, где будет лежать ключ для подключения к GCE. Распаковка указана в блоке 
```yml
before_install
matrix:
  - GCE_CREDENTIALS_FILE="$(pwd)/cInfra-2ed7216cd032.json"
```
9) Необходимо так же изменить playbooks.yml так как роль теперь называется не db, а KirillBuzan_infra_db
10) Версия Python, которая указана в примере, оказалась подстаой))) travis выдал ошибку:
ERROR: Python version '3.6.3' not supported.  Molecule only supports python version = 2.7.
Внес исправления к файл .travis.yml с указанием правильной версии
```yml
python:
  - '2.7'
```
11) Slack
``` bash
travis encrypt "devops-team-otus:TOKEN" --add notifications.slack -r Otus-DevOps-2017-11/KirillBuzan_infra_db
```
Ссылка Slack:
https://devops-team-otus.slack.com/messages/C8BFGAUAV/apps/A0F81FP4N/
