***Павлюков Владимир Владимирович, группа*** **QAMID45**

# Домашнее задание к занятию «3.1. Docker»

<details><summary>Вводная часть.</summary>

**Важно**: прежде чем приступать, обязательно прочитайте [руководство по установке Docker](https://github.com/netology-code/aqa-homeworks/blob/master/docker/installation.md).

В качестве результата пришлите ссылки на ваши GitHub-проекты в личном кабинете студента на сайте [netology.ru](https://netology.ru).

Все задачи этого занятия нужно делать **в разных репозиториях**.

**Важно**: если у вас что-то не получилось, то оформляйте issue [по установленным правилам](https://github.com/netology-code/aqa-homeworks/blob/master/report-requirements.md).

**Важно**: не делайте ДЗ всех занятий в одном репозитории. Иначе вам потом придётся достаточно сложно подключать системы Continuous integration.

## Как сдавать задачи

1. Инициализируйте на своём компьютере пустой Git-репозиторий.
1. Добавьте в него готовый файл [.gitignore](https://github.com/netology-code/aqa-homeworks/blob/master/.gitignore).
1. Добавьте в этот же каталог код, требуемый в ДЗ.
1. Сделайте необходимые коммиты.
1. Создайте публичный репозиторий на GitHub и свяжите свой локальный репозиторий с удалённым.
1. Сделайте пуш — удостоверьтесь, что ваш код появился на GitHub.
1. Ссылку на ваш проект отправьте в личном кабинете на сайте [netology.ru](https://netology.ru).
1. Задачи, отмеченные как необязательные, можно не сдавать, это не повлияет на получение зачёта.

**Важно**: задачи этого занятия не предполагают подключения к CI.

### Plugin IDEA

Этот раздел не является частью ДЗ, но он позволяет вам облегчить себе взаимодействие с Docker и Docker compose на первое время, воспользовавшись графическим интерфейсом.

Откройте IntelliJ IDEA, перейдите в раздел настроек:
* Windows/Linux: File -> Settings
* MacOS: IntelliJ IDEA -> Preferences

Найдите в поиске раздел Plugins:

![](img/plugins.png)

Нажмите на кнопку `Install`, после установки перезапустите IDEA.

Теперь при открытии файлов `Dockerfile`, `docker-compose.yml` IDEA будет предлагать автодополнение и возможность запуска прямо из окна редактора:

![](img/editor.png)

![](img/run.png)

После запуска откроется окно `Services`, где вы можете посмотреть образы, контейнеры и запущенные с помощью Docker compose сервисы:

![](img/services.png)

</details>

## Задача №1: PostgreSQL

<details><summary>Развернуть Задача №1: PostgreSQL</summary>

Вам необходимо подготовить приложение к тестированию на СУБД PostgreSQL. Используйте образ 12-alpine, если он недоступен, то берите последний опубликованный на Docker Hub. Возьмите собранный JAR-файл `db-api.jar`, аналогично примеру на лекции положите рядом файл `application.properties`, но в строке:
`jdbc:mysql://...` поменяйте `mysql` на `postgresql`.

Вам нужно дописать остальные настройки: хост, порт, БД, имя пользователя и пароль.

Кроме того, вам нужно подготовить файл `docker-compose.yml`, в котором прописать настройки для запуска контейнера PostgreSQL. Всю информацию о его запуске вы найдёте на официальной странице образа на Docker Hub.

Запустите сначала `docker-compose up` и только после того, как БД запустится, запустите целевое приложение: `java -jar db-api.jar`. Если нужно поменять порт запуска, по умолчанию он 9999, то добавьте в файл `application.properties` строку `server.port=<нужный номер порта>`.

Если вы сделали всё правильно, то приложение запустится и на `GET http://localhost:9999/api/cards` выдаст вам JSON с картами:
```json
[ 
   { 
      "id":1,
      "name":"Альфа-Карта Premium",
      "description":"Альфа-Карта вернёт ваши деньги",
      "imageUrl":"/alfa-card-premium.png"
   },
   { 
      "id":2,
      "name":"Alfa Travel Premium",
      "description":"Самая выгодная карта для путешествий",
      "imageUrl":"/alfa-card-travel.png"
   },
   { 
      "id":3,
      "name":"CashBack Premium",
      "description":"Заправь свою карту. Кешбэк на АЗС, в кафе и ресторанах",
      "imageUrl":"/alfa-card-cashback.png"
   }
]
```

В результате выполнения этой задачи вы должны положить в репозиторий следующие файлы:
* db-api.jar,
* application.properties,
* docker-compose.yml.

**Важно**: для удаления всех данных и начала с чистого листа сделайте следующее:
* `docker-compose down` в каталоге с файлом `docker-compose.yml`,
* удалите каталог для хранения данных `data`,
* запустите заново `docker-compose up`, после того как всё исправите.

Важно: команда `docker-compose rm` в каталоге с файлом `docker-compose.yml` удаляет сам контейнер.

</details>

# Запуск

* Запуск контейнера:
```sh
docker-compose up
```
(_должен быть docker-compose.yml в текущей директории_)
* Запуск db-api:
```sh
java -jar db-api.jar & echo $! > ./testserver.pid
```
(_id процесса сохраняется в файл, чтобы потом проще послать ему сигнал корректно завершиться_)
* Для проверки, что все правильно, в браузере пройти по ссылке: "http://localhost:9999/api/cards" (_равносильно отправке GET запроса по этому url_)
* Для перезапуска:
  * Завершить приложение:
  ```sh
  kill -TERM $(cat ./testserver.pid)
  ```
  * Остановить контейнер.
  ```sh
    docker-compose stop
  ```
  * Удалить контейнер:
  ```sh
    docker-compose rm
  ```
  * Узнать <IMAGE ID>:
  ```sh
    docker image ls --all
  ```
  * Удалить образ:
  ```sh
    docker rmi <IMAGE ID>
  ```
  * Запустить удаление каталога хранения данных:
  ```sh
    sudo rm -R ./data
  ```
  * Выполнить инструкции запуска.

### Памятка (_все команды можно получить с помощью --help_)

* Посмотреть все образы:
  ```sh
    docker image ls --all
  ```
* Удалить образ:
  ```sh
    docker rmi <IMAGE ID>
  ```
* Удалить ВСЕ ИМЕЮЩИЕСЯ образы:
  ```sh
    docker rmi $(docker images --all -q)
  ```
* Посмотреть все контейнеры:
  ```sh
    docker container ls --all
  ```
* Приостановить контейнер:
  ```sh
    docker container pause <CONTAINER ID>
  ```
* Возобновить работу после приостановки:
  ```sh
    docker container pause <CONTAINER ID>
  ```
* Остановить контейнер:
  ```sh
    docker container stop <CONTAINER ID>
  ```
* Удалить контейнер:
  ```sh
    docker rm <CONTAINER ID>
  ```
* Остановить контейнеры по шаблону:
  ```sh
    docker stop $(docker ps --filter name=reportportal_ -q) 
  ```
* Удалить все остановленные контейнеры:
  ```sh
    docker container prune --force
  ```
