# DataLens

[**DataLens**](https://datalens.tech) is a modern business intelligence and data visualization system. It was developed and extensively used as a primary BI tool in Yandex and is also available as a part of [Yandex Cloud](https://datalens.yandex.com) platform. See also [our roadmap](https://github.com/orgs/datalens-tech/projects/1), [releases notes](https://github.com/datalens-tech/datalens/releases) and [community in telegram](https://t.me/YandexDataLens).

* [Чем отличаемся?](https://github.com/akrasnov87/datalens/blob/main/docs/features.md)
* [Что планируем?](https://github.com/akrasnov87/datalens/blob/main/docs/roadmap.md)

## Запуск Datalens с авторизацией

__Внимание__: авторизация `Zitadel` не будет работать совместно с `datalens-auth`!!!

<pre>
git clone https://github.com/akrasnov87/datalens && cd datalens
docker compose -f docker-compose-demo.yml --env-file ./.env.demo up -d
</pre>

__Примечание__: процесс полного разворачивания может занять некоторое время. Если авторизация не проходит и выходит ошибка со статусом `500` - это значит backend ещё не развёрнут, ждите.

__Внимание__: предварительно лучше удалить каталог `metadata`, если он есть. Но будьте __осторожны__ т.к. в папке храняться Ваши предыдущие данные!!! 

Для тестирования новых возможностей иногда используется dev-сборка, её можно запустить командой

<pre>
docker compose -f docker-compose-dev.yml --env-file ./.env up -d
</pre>

Основное отличие её от демонстрационной это - появление новых функций, которые тестируются или дорабатываются.

### Авторизация

БД по умолчанию есть три пользователя:

* master - пользователь с максимальными правами
* admin - пользователь с правами для просмотра и редактирования информации по указаному `project_id`
* user - пользователь для просмотра данных

Пароль по умолчанию у всех: `qwe-123`

### Мои примечания
С репозитория `https://github.com/datalens-tech/datalens` были скачены и изменены:

* datalens-backend
* datalens-ui
* datalens-us
* datalens

Изменённые версии:

* [datalens-backend](https://github.com/akrasnov87/datalens-backend)
* [datalens-us](https://github.com/akrasnov87/datalens-us)
* [datalens-ui](https://github.com/akrasnov87/datalens-ui)
* [datalens](https://github.com/akrasnov87/datalens)

Создан новый компонент для авторизации:

* [datalens-auth](https://github.com/akrasnov87/datalens-auth)

Исходный код БД [us-db-ci_purgeable](https://github.com/akrasnov87/us-db-ci_purgeable)

### Структура

При работе с решением `datalens`, где включена авторизация в базе данных создаётся новая схема `core`. В ней присутствуют таблицы и функции, которые обеспечивают механизм авторизации.

__Примечание__: если требуется отключить создание данных для авторизации, то указываем параметр USE_AUTH_DATA=0

#### Описание основных таблицы:

__pd_users - Пользователи__

Предназначена для хранение учётных записей.

__Примечание__: в таблице есть колонка `c_password`, по умолчанию она пустая. В ней можно хранить пароль в открытом виде (без шифрования), но при этом нужно удалить данные из колонки `s_hash`.

__pd_roles - Роли__

Предназначена для хранения списка ролей, которые поддерживаются системой. По умолчанию загружено 3 (три) роли - их лучше не трогать!!! Новые можно создавать в неограниченном количестве.

* master - роль с неограниченными правами. Сама по себе роль бессмыслена, должна быть в связке с ролью `admin`.
* admin - роль, позволяющая создавать и редактировать новые сущности.
* datalens - роль для просмотра информации. Редактирование или изменение запрещено;
* oidc - роль обозначает, что пользователь попал через внешнюю систему авторизации.

Для связи пользователей и ролей используется таблица `pd_userinroles`. Данная таблица позволяет привязать одного пользователя к нескольким ролям (например, пользователь может быть и "мастером", и "администратором").

Все базовые роли помещены признаком `b_base=true`. Все новые или производные роли должны быть частью этих ролей за счёт таблицы `core.pd_userinroles`.

__pd_accesses - Права доступа__

Предназначена для хранения прав. Принwип работы заключается в том, что по умолчанию всё запрещено. И чтобы появились "разрешения" нужно создать их. По умолчанию в системе они уже заведены в виде записей, где заполена информация в колонках:

* f_role (f_user) - идентификатор роли из pd_roles (pd_users)
* c_name - наименование сущности или объекта (по умолчанию требуемые рахзрешения уже заполнены).
* c_function - наименование "динамических" функций в `datalens-auth`;
* dl_id: bigint - служебный идентификатор для JOIN'а с основными таблицами.

__Расшифровка прав__

Права в колонке c_function расшифровываются, как:

* DL - это пространство имён функций предназначенных для `datalens`.
* вторым элементом идёт идентификатор сушности. Он отображается в адресной строке
* третий - действие, которое может применятся к сущности:
  * "символ звёздочка (снежинка)" - полные права
  * Select или Query - права на чтение
  * Add - права на создание
  * Update - права на редактирование
  * Delete - права на удалениеы 

### Авторы доработки

* Александр Краснов - https://github.com/akrasnov87
* Кирилл Автономов -  https://github.com/kirillva

## Getting started

### Installing Docker

DataLens requires Docker to be installed. Follow these instructions depending on the platform you use:

- [macOS](https://docs.docker.com/desktop/install/mac-install/)
- [Linux](https://docs.docker.com/engine/install/)
- [Windows](https://docs.docker.com/desktop/install/windows-install/)

\* Note about docker compose

- New docker compose plugin available as `docker-compose-v2` package on Ubuntu 20.04/22.04/24.04 from base APT repository

- Minimal supported version of legacy docker-compose utility as separate package is `1.29.0`. It includes in base APT repository as `docker-compose` package only on Ubuntu 22.04

### Running containers

Use the following command to start DataLens containers:

```bash
git clone https://github.com/datalens-tech/datalens && cd datalens

HC=1 docker compose up

# or with an external metadata database
METADATA_POSTGRES_DSN_LIST="postgres://{user}:{password}@{host}:{port}/{database}" HC=1 docker compose up
```

This command will launch all containers required to run DataLens and UI will be available on http://localhost:8080

If you want to use a different port (e.g. `8081`), you can set it using the `UI_PORT` env variable:

```bash
UI_PORT=8081 docker compose up
```

Для подключения компонета авторизации ([datalens-auth](https://github.com/akrasnov87/datalens-auth)) требуется передать параметр `NODE_RPC_URL`

```bash
NODE_RPC_URL=http://localhost:7000/demo/rpc docker compose up
```


<details>
      <summary>Notice on Highcharts usage</summary>

      Highcharts is a proprietary commercial product. If you enable highcharts in your DataLens instance (with `HC=1`` variable), you should comply with Highcharts license (https://github.com/highcharts/highcharts/blob/master/license.txt).

      When Highcharts is disabled in DataLens, we use D3.js instead. However, currently only few visualization types are compatible with D3.js. We are actively working on adding D3 support to additional visualizations and are going to completely replace Highcharts with D3 in DataLens.

</details>

<details>
            <summary>How to enable Yandex Map</summary>

Available since [release v1.11.0](https://github.com/datalens-tech/datalens/releases/tag/v1.11.0)

Use the following container parameters for launch:

| Parameter            | Description                                                    | Values        |
| -------------------- | -------------------------------------------------------------- | ------------- |
| `YANDEX_MAP_ENABLED` | Enable usage of Yandex Map visualization                       | `1` or `true` |
| `YANDEX_MAP_TOKEN`   | Yandex Map [API key](https://yandex.ru/dev/jsapi-v2-1/doc/en/) | `<string>`    |

```bash
YANDEX_MAP_ENABLED=1 YANDEX_MAP_TOKEN=XXXXXXXXX docker compose up
```
</details>

## How to update

Just pull the new `docker-compose.yml` and restart.

```bash
docker compose down
git pull
docker compose up
```

All your user settings will be stored in the `metadata` folder.

## Parts of the project

DataLens consists of the three main parts:

- [**UI**](https://github.com/datalens-tech/datalens-ui) is a SPA application with corresponding Node.js part. It provides user interface, proxies requests from users to backend services and also applies some light data postprocessing for charts.
- [**Backend**](https://github.com/datalens-tech/datalens-backend) is a set of Python applications and libraries. It is responsible for connecting to data sources, generating queries for them and post-processing the data (including formula calculations). The result of this work is an abstract dataset that can be used in UI for charts data request.
- [**UnitedStorage (US)**](https://github.com/datalens-tech/datalens-us) is a Node.js service that uses PostgreSQL to store metadata and configuration of all DataLens objects.

## What's already available

We are releasing DataLens with first minimal set of available connectors (clickhouse, clickhouse over ytsaurus and postgresql) as well as other core functionality such as data processing engine and user interface. However, to kick off this project in a reasonable timeframe we have chosen to drop some of the features out of the first release: this version does not contain middleware and components for user sessions, object ACLs and multitenancy (although code contains entry-points for such extensions). We are planning to add missing features based on our understanding of community priorities and your feedback.

## Cloud Providers
Below is a list of cloud providers offering DataLens as a service:
1. [Yandex Cloud](https://datalens.yandex.com) platform
2. [DoubleCloud](https://double.cloud/services/doublecloud-visualization/) platform

## Authentication (beta)
DataLens supports authentication via [Zitadel](https://zitadel.com/) identity platform.

Use the following command to initialize Zitadel **(you need to do this only once)**:

```bash
bash init.sh
```

Notice the updated `.env` file after initialization: it contains Zitadel access keys. Keep that file safe and do not share it's contents.

After initialization you can start DataLens containers using special version of docker compose file:

```bash
HC=1 docker compose -f docker-compose.zitadel.yml up
```

After that you can login to DataLens on http://localhost:8080 using the default user credentials:

| Username                          | Password     |
| --------------------------------- | ------------ |
| `zitadel-admin@zitadel.localhost` | `Password1!` |

You can use the same credentials to configure Zitadel and add new users using Zitadel control panel at http://localhost:8085/. **Don't forget to login there at least once to change the default password.**

By default in DataLens with authentication enabled, all users have a datalens.viewer role. This allows them to use all collections and workbooks in read-only mode. They are not allowed to create or modify any objects with this role. To be able to create or edit objects, they need to have a datalens.editor or datalens.admin role. To grant these roles, open Zitadel at http://localhost:8085/ui/console/grants, then find the user to whom you want to grant a new role and click on the user and select the new role.

DataLens supports the following roles:

- `datalens.viewer`: allows viewing all collections and workbooks, but does not allow creating or editing any object.
- `datalens.editor`: includes the `datalens.viewer` role and allows creating, editing and deleting any object. 
- `datalens.admin`: currently equal to `datalens.editor`. In the future releases, users with this role will be able to manage system-wide settings and perform administrative functions.

## FAQ

#### Where does DataLens store it's metadata?

We use the `metadata` folder to store PostgreSQL data. If you want to start over, you can delete this folder: it will be recreated with demo objects on the next start of the `datalens-us` container.

#### I use the `METADATA_POSTGRES_DSN_LIST` param for external metadata database and the app doesn't start. What could be the reason?

We use some PostgresSQL extensions for the metadata database and the application checks them at startup and tries to install them if they haven't been already installed. Check your database user's rights for installing extensions by trying to install them manually:

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE EXTENSION IF NOT EXISTS btree_gin;
CREATE EXTENSION IF NOT EXISTS btree_gist;
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
```

If this attempt is unsuccessful, try to install dependencies by database admin and add param `METADATA_SKIP_INSTALL_DB_EXTENSIONS=1` on startup, this parameter allows the app to skip installing extensions.

If you're using managed database, it's also possible that extensions for your database cluster are controlled by external system and could be changed only using it's UI or API. In such case, consult with documentation for managed database service which you're using. Don't forget to add `METADATA_SKIP_INSTALL_DB_EXTENSIONS=1` after installing extensions this way.

#### My PostgresSQL cluster has multiple hosts, how can I specify them in `METADATA_POSTGRES_DSN_LIST` param?

You can write all cluster hosts separated by commas:

`METADATA_POSTGRES_DSN_LIST="postgres://{user}:{password}@{host_1}:{port}/{database},postgres://{user}:{password}@{host_2}:{port}/{database},postgres://{user}:{password}@{host_3}:{port}/{database}" ...`

#### How can I specify custom certificate for connecting to metadata database?

You can add additional certificates to the database in `./certs/root.crt`, they will be used to connect to the database from the `datalens-us` container.

If `datalens-us` container does not start even though you provided correct certificates, try to change `METADATA_POSTGRES_DSN_LIST` like this:
`METADATA_POSTGRES_DSN_LIST="postgres://{user}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert=/certs/root.crt"`


#### Why do i see two compose files: docker-compose.yml & docker-compose-dev.yml?

`docker-compose-dev.yml` is a special compose file that is needed only for development purposes. When you run DataLens in production mode, you always need to use `docker-compose.yml`. The `docker-compose up` command uses it by default. 

#### Хочу отключить авторизацию RPC

Просто закомментируйте `NODE_RPC_URL` в `docker-compose*.yml`

#### Как передать переменные

Лучше создать в корне проекта файл .env и записать туда:
<pre>
HC="1"
NODE_RPC_URL="http://localhost:7000/demo/rpc"
</pre>

Для запуска выполнить `docker compose --env-file ./.env up`

##### Список переменных
* APP_ENV: string - тип приложения, пространства. Добавляется к наименованию контейнеров.
* METADATA_POSTGRES_DSN_LIST: string - подключение к БД PostgreSQL. По умолчанию `postgres://us:us@pg-us:5432/us-db-ci_purgeable`
* METADATA_SKIP_INSTALL_DB_EXTENSIONS: integer - признак для пропуска установки расширений. По умолчанию `0`

При подключении компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth) устанавливается расширение `pgcrypto`.

* USE_DEMO_DATA: integer - признак загрузки демонстрационных данных. По умолчанию `1`
* PROJECT_ID: string - наименование проекта для public.workbooks и public.collections. По умолчанию `datalens-demo`
* HC: integer - признак подключения чартов Highcharts
* NODE_RPC_URL: string - строка подключения компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth). По умолчанию `http://us-auth/demo/rpc`
* AUTH_ENV: string - пространство имён для компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth). По умолчанию `demo`
* UI_PORT: integer - порт, на котором поднимится Datalens. По умолчанию 8080

Для указание настроек OIDC авторизации, требуется передать:

* OIDC: boolean - признак подключения OIDC-провайдера авторизации
* OIDC_ISSUER: string - адрес страницы для получения параметров OIDC авторизации
* OIDC_BASE_URL: string - обработчик авторизации (локальный, например ~/auth/v1/oidc)
* OIDC_CLIENT_ID: string - идентификатор клиента
* OIDC_SECRET: string - секретный ключ
* OIDC_NAME: string - наименование провайдера на клиенте

Пример:

<pre>
OIDC=true
OIDC_ISSUER=https://accounts.google.com/.well-known/openid-configuration
OIDC_BASE_URL=https://1b39-94-232-56-134.ngrok-free.app/auth/v1/oidc
OIDC_CLIENT_ID=528815932068-dibsaju2o48pri31.apps.googleusercontent.com
OIDC_SECRET=GOCSPX-xQMuLZue5aWGw4JwFVl
OIDC_NAME="GOOGLE"
</pre>

Компонет `datalens-ui` может принимать несколько OIDC провадеров (до 4). Чтобы передать их просто требуется указывать номер, например

<pre>
OIDC_2=true
OIDC_ISSUER_2=https://accounts.google.com/.well-known/openid-configuration
OIDC_BASE_URL_2=https://1b39-94-232-56-134.ngrok-free.app/auth/v1/oidc
OIDC_CLIENT_ID_2=528815932068-dibsaju2o48pri31.apps.googleusercontent.com
OIDC_SECRET_2=GOCSPX-xQMuLZue5aWGw4JwFVl
OIDC_NAME_2="GOOGLE"
</pre>

#### Почему при запуске через авторизацию система не работает

Лучше вначале запусить создание БД с выключенным параметром `NODE_RPC_URL` для контейнера `datalens-us`, а уже затем только включить этот аргумент. В таком случаи не должно быть ошибок связанных с "версией дадасета" (код ошибок 500).

## Получение последних изменений

<pre>
git remote add upstream https://github.com/datalens-tech/datalens.git
git pull upstream main
</pre>

## Инструкция по разворачиванию локальной копии

- запустить первичный проект
- сделать копию [us-db-ci_purgeable](https://github.com/akrasnov87/us-db-ci_purgeable)
- через pg_admin сделать резервное копирование данных (только данных) - в настройках использовать INSERT
- применить инструкцию в проекте (нужно исправить ошибку с функцией naturalsort)

### Запуск
В корне проекта есть специальный `compose` файл:

<pre>
docker compose -p datalens_demo -f docker-compose-demo.yml --env-file ./.env.demo up -d
</pre>

Просмотр логов:
<pre>
docker compose -p datalens_demo -f docker-compose-demo.yml --env-file ./.env.demo logs -n 100
</pre>

## Локальное сохранение
<pre>
docker save -o containers/datalens-control-api.tar akrasnov87/datalens-control-api:0.2139.0
docker save -o containers/datalens-data-api.tar ghcr.io/datalens-tech/datalens-data-api:0.2139.0
docker save -o containers/datalens-us.tar akrasnov87/datalens-us:0.246.0
docker save -o containers/datalens-ui.tar akrasnov87/datalens-ui:0.2140.1
docker save -o containers/datalens-auth.tar akrasnov87/datalens-auth:0.2.2
</pre>

#### What are the minimum system requirements?

* datalens-auth - 256 MB RAM

* datalens-ui - 512 MB RAM

* datalens-data-api - 1 GB RAM

* datalens-control-api - 512 MB RAM

* datalens-us - 512 MB RAM

* datalens-pg-compeng - 1 GB RAM

* datalens-pg-us - 512 MB RAM

Summary:

* RAM - 4 GB

* CPU - 2 CORES

This is minimal basic system requirements for OpenSource DataLens installation. Аctual consumption of VM resources depends on the complexity of requests to connections, connections types, the number of users and processing speed at the source level
