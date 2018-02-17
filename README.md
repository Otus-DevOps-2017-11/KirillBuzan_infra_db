# KirillBuzan_infra_db
Homework-13
[![Build Status](https://travis-ci.org/Otus-DevOps-2017-11/KirillBuzan_infra_db.svg?branch=master)](https://travis-ci.org/Otus-DevOps-2017-11/KirillBuzan_infra_db)

Я никогда в жизни не был так рад зеленому тесту!!! Получилось! Я запомню эту домашнюю работу на всю жизнь. 
Немножко изменил предложенные команды:
# генерируем ключ для подключения по SSH
ssh-keygen -t rsa -f gce_travis -C 'travis' -b 4096 N ''
# Добавил созданный открытый ключ gce_travis.pub в gce -> Метаданные -> SSH-ключи

# Сгенерировал новый сервисный ключ: Infra-2ed7216cd032.json

# Подключил в travis репозиторий Otus-DevOps-2017-11/KirillBuzan_infra_db
# Выполнил команды по шифрованию данных travis локально:
travis encrypt GCE_SERVICE_ACCOUNT_EMAIL='service-account@lucky-almanac.iam.gserviceaccount.com' --add
travis encrypt GCE_CREDENTIALS_FILE="$(pwd)/Infra-2ed7216cd032.json" --add
travis encrypt GCE_PROJECT_ID='lucky-almanac' --add
# Сразу сохранить изменения в удаленный репозиторий не получилось. Хотя делал все как описано в документациях. Сгенерировал в GitHub в своем профиле токен: Settings -> Developer Settings -> Personal Access Tokens. 
# К репозиторию подключался командой:
travis login --githun-token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# Подключение проходило успешно. Выдавалась ошибка при попытке внести изменения в .travis.ymp: файл .travis.yml не найден. Над правами для токена шаманил, но ничего это не дало. Делал так:
travis encrypt GCE_PROJECT_ID='lucky-almanac' --add -r 'Otus-DevOps-2017-11/KirillBuzan_infra_db'

# шифруем файлы
tar cvf secrets.tar Infra-2ed7216cd032.json gce_travis
# После выполнения команды, создался файл travis_secrets.tar.enc который и следует использовать в репозитории о чем любезно предупреждает travis.

# Добавляем в .travis.yml информацию о зашифрованном файле
travis encrypt-file secrets.tar --add
# Так же в блок env необходимо добавить информацию, где будет лежать ключ для подключения к GCE. Распаковка указана в блоке before_install
matrix:
  - GCE_CREDENTIALS_FILE="$(pwd)/cInfra-2ed7216cd032.json"

# Необходимо так же изменить playbooks.yml так как роль теперь называется не db, а KirillBuzan_infra_db
# Версия Python, которая указана в примере, оказалась подстаой))) travis выдал ошибку:
ERROR: Python version '3.6.3' not supported.  Molecule only supports python version = 2.7.
# Внес исправления к файл .travis.yml с указанием правильной версии
python:
  - '2.7'

# Slack
travis encrypt "devops-team-otus:TOKEN" --add notifications.slack -r Otus-DevOps-2017-11/KirillBuzan_infra_db
