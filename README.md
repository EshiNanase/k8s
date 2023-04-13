# Django site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри конейнера Django запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Как запустить dev-версию

### Клонировать репозиторий

```shell-session
$ git clone https://github.com/EshiNanase/k8s.git
```

### Запустить кластер minikube

https://minikube.sigs.k8s.io/docs/

```shell-session
$ minikube start
```

### Создать django-config.yml

Подробнее о переменных окружения в разделе "Переменные окружения" ниже

```shell-session
apiVersion: v1
kind: ConfigMap
metadata:
  name: django-config-v1
  labels:
    app: django-server
data:
  SECRET_KEY:
  DEBUG:
  DATABASE_URL:
  ALLOWED_HOSTS: "127.0.0.1,star-burger.test"
  POSTGRES_USER:
  POSTGRES_PASSWORD:
  POSTGRES_DB:
```

Подробнее о переменных окружения:
`SECRET_KEY` -- обязательная секретная настройка Django. Это соль для генерации хэшей. Значение может быть любым, важно лишь, чтобы оно никому не было известно. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#secret-key).

`DEBUG` -- настройка Django для включения отладочного режима. Принимает значения `TRUE` или `FALSE`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DEBUG).

`ALLOWED_HOSTS` -- настройка Django со списком разрешённых адресов. Если запрос прилетит на другой адрес, то сайт ответит ошибкой 400. Можно перечислить несколько адресов через запятую, например `127.0.0.1,192.168.0.1,site.test`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#allowed-hosts).

`DATABASE_URL` -- адрес для подключения к базе данных PostgreSQL. Другие СУБД сайт не поддерживает. [Формат записи](https://github.com/jacobian/dj-database-url#url-schema).

`POSTGRES_USER` -- имя юзера для подключения к базе данных PostgreSQL

`POSTGRES_PASSWORD` -- пароль для подключения к базе данных PostgreSQL [Формат записи](https://github.com/jacobian/dj-database-url#url-schema).

`POSTGRES_DB` -- название вашей базы данных PostgtreSQL

### Добавить в кластер все манифесты

Необходимо добавить в кластер все манифесты в репозитория + созданный django-config.yml
```shell-session
$ kubectl apply -f
```

### Открыть сайт по ссылке

```shell-session
http://star-burger.test
```

