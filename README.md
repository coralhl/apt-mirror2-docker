# apt-mirror2-docker

[![license](https://img.shields.io/github/license/coralhl/apt-mirror2-docker)](https://github.com/coralhl/apt-mirror2-docker/blob/main/LICENSE)
[![commits](https://img.shields.io/github/commit-activity/t/coralhl/apt-mirror2-docker)](https://github.com/coralhl/apt-mirror2-docker/commits/main/)
![last commit](https://badgen.net/github/last-commit/coralhl/apt-mirror2-docker)
[![actions](https://img.shields.io/github/actions/workflow/status/coralhl/apt-mirror2-docker/docker-image.yml)](https://github.com/coralhl/apt-mirror2-docker/actions)
![docker size](https://img.shields.io/docker/image-size/coralhl/apt-mirror2/latest)
![docker pulls](https://img.shields.io/docker/pulls/coralhl/apt-mirror2)

## Введение

**[v7](https://gitlab.com/apt-mirror2/apt-mirror2/-/releases/v7) С выходом версии 7 появился официальный образ, предоставленный автором apt-mirror2. Образ с тегом Alpine весит всего около 20 МБ, что не может не радовать.**

Этот репозиторий используется для сборки [coralhl/apt-mirror2](https://hub.docker.com/r/coralhl/apt-mirror2), одной из реализаций [apt-mirror2](https://gitlab.com/apt-mirror2/apt-mirror2 "apt-mirror2") для Docker. В эту сборку включены `Nginx` и `Cron`. Если вам не нужно управлять расписаниями внутри контейнера, рекомендуется использовать официальный образ для внешнего определения расписаний `Cron`.

Образ использует `python:alpine` в качестве первого этапа сборки, `alpine:3` в качестве базового образа и установленный `Nginx`. Размер образа составляет около `30 Мб`.

Автор исправил проблемы с сетью в обновлении v8; подробности см. в [Версия 8](https://gitlab.com/apt-mirror2/apt-mirror2/-/releases/v8#bug-fixes).

В версии `latest-pure` не установлен `Nginx`. Тогда вам придётся развернуть Nginx или другой веб-сервер отдельно.

## Использование

- Перед запуском контейнера настройте [`mirror.list`](https://gitlab.com/apt-mirror2/apt-mirror2/-/blob/master/mirror.list "mirror.list").
- Файл `mirror.list` в контейнере расположен по умолчанию в `/etc/apt/mirror.list`.
- В `mirror.list` указана директория для сохранения пакетов — `/var/spool/apt-mirror`, которая также является корневым каталогом Nginx по умолчанию. Не изменяйте её.
- `Nginx` работает на порту `80`.

### Использовать напрямую

```bash
docker run -d \
    --name=apt-mirror2 --network=bridge --restart unless-stopped \
    -p 81:80 \
    -e "CRON_SCHEDULE=0 2,8,14,20 * * *" \
    -e "TZ=Europe/Moscow" \
    -v /path/apt-mirror:/var/spool/apt-mirror \
    -v /path/mirror.list:/etc/apt/mirror.list \
    coralhl/apt-mirror2:latest
```

### Docker Compose

```yaml
version: '3'

services:
  apt-mirror2:
    image: coralhl/apt-mirror2:latest
    network_mode: "bridge"
    container_name: apt-mirror2
    restart: unless-stopped
    ports:
      - 81:80
    environment:
      - CRON_SCHEDULE=0 2,8,14,20 * * *
      - TZ=Europe/Moscow
    volumes:
      - /path/apt-mirror:/var/spool/apt-mirror
      - /path/mirror.list:/etc/apt/mirror.list
```

## Переменные ENV

|      Имя      |  Значение по умолчанию  |   Назначение   |
|:-------------:|:-----------------------:|:--------------:|
|      TZ       |       Europe/Moscow     |  Настройки часового пояса  |
| CRON_SCHEDULE |   0 2,8,14,20 \* \* \*  | Расписание cron |

## Благодарности

- [apt-mirror2](https://gitlab.com/apt-mirror2/apt-mirror2 "apt-mirror2")

- [apt-mirror2-docker](https://github.com/coralhl/apt-mirror2-docker "apt-mirror2-docker")

## Лицензия

Соответствует первоначальному проекту.

GNU General Public License v3.0 or later
