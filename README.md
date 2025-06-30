# DataLens &middot; [![Release](https://img.shields.io/github/v/release/datalens-tech/datalens?logo=github&color=orange)](https://github.com/datalens-tech/datalens/releases) [![last commit](https://img.shields.io/github/last-commit/datalens-tech/datalens?logo=github)](https://github.com/datalens-tech/datalens/commits/main)

[![datalens-ui](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fgithub.com%2Fdatalens-tech%2Fdatalens%2Fraw%2Fmain%2Fversions-config.json&query=%24.uiVersion&label=ui%20version)](https://github.com/datalens-tech/datalens-ui)
[![datalens-us](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fgithub.com%2Fdatalens-tech%2Fdatalens%2Fraw%2Fmain%2Fversions-config.json&query=%24.usVersion&label=us%20version)](https://github.com/datalens-tech/datalens-us)
[![datalens-backend](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fgithub.com%2Fdatalens-tech%2Fdatalens%2Fraw%2Fmain%2Fversions-config.json&query=%24.backendVersion&label=backend%20version)](https://github.com/datalens-tech/datalens-backend)
[![datalens-auth](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fgithub.com%2Fdatalens-tech%2Fdatalens%2Fraw%2Fmain%2Fversions-config.json&query=%24.authVersion&label=auth%20version)](https://github.com/datalens-tech/datalens-auth)
[![datalens-meta-manager](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fgithub.com%2Fdatalens-tech%2Fdatalens%2Fraw%2Fmain%2Fversions-config.json&query=%24.metaManagerVersion&label=meta-manager%20version)](https://github.com/datalens-tech/datalens-meta-manager)


[**DataLens**](https://datalens.tech) is a modern business intelligence and data visualization system. It was developed and extensively used as a primary BI tool in Yandex and is also available as a part of [Yandex Cloud](https://datalens.yandex.com) platform. See also [our roadmap](https://github.com/orgs/datalens-tech/projects/1), [releases notes](https://github.com/datalens-tech/datalens/releases) and [community in telegram](https://t.me/YandexDataLens).

* [Чем отличаемся?](https://github.com/akrasnov87/datalens/blob/main/docs/features.md)
* [Что планируем?](https://github.com/akrasnov87/datalens/blob/main/docs/roadmap.md)

## Версионность

Версия `datalens v2.3.0` соответствует нашей версии `v2.3.0-night` (см. файл [CHANGELOG](CHANGELOG.md)).

## Запуск Datalens с авторизацией

__Внимание__: Текущая базовая авторизация `datalens` не будет работать совместно с `datalens-auth (us-auth)`!!!

<pre>
git clone https://github.com/akrasnov87/datalens && cd datalens
./init.sh --hc --hc-local --up
</pre>

Либо можно запустить вручную:
<pre>
docker compose -f docker-compose.dev.yaml --env-file=./.env up
</pre>

Где в .env должны быть следующие значения:
<pre>
POSTGRES_PASSWORD=postgres
US_MASTER_TOKEN=fake-master-token
CONTROL_API_CRYPTO_KEY="fake-crypto-key"
HC=1
APP_ENV=prod
HC_ENDPOINT=localhost:8080/highcharts
HC_PROTOCOL=http
</pre>

__Примечание__: если возникают проблемы, то может помочь удаление из `volumes` хранилища с наименованием `datalens_db-postgres`.

__Примечание__: процесс полного разворачивания может занять некоторое время. Если авторизация не проходит и выходит ошибка со статусом `500` - это значит backend ещё не развёрнут, ждите.

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

**Note about Docker Compose:**

- The new Docker Compose plugin is available as the `docker-compose-v2` package on Ubuntu 20.04/22.04/24.04 from the base APT repository.

- The minimal supported version of the legacy docker-compose utility (as a separate package) is `1.29.0`. It is included in the base APT repository as the `docker-compose` package only on Ubuntu 22.04.

### Running containers

Clone repository:

```bash
git clone https://github.com/datalens-tech/datalens && cd datalens
```

For the quick start use the following command to start DataLens containers:

```bash
HC=1 docker compose up
```

This command will launch all containers required to run DataLens, and the UI will be available on http://localhost:8080 (default user and password is `admin`, `admin`).

<details>
      <summary>Using different port for UI</summary>
If you want to use a different port (e.g. `8081`), you can set it using the `UI_PORT` env variable:
      
```bash
UI_PORT=8081 docker compose up
```
</details>

However, for production usage we recommend generating a compose file with random secrets:

```bash
# generate random secrets with openssl, store it to .env file and prepare production compose template
./init.sh --hc

# and then run production compose
docker compose -f ./docker-compose.production.yaml up -d

# you can also generate and run production compose file with one command
./init.sh --hc --up
```

Randomly generated admin password will be stored in the `.env` file and printed to terminal.

**Note:** You can find all script arguments by running the `./init.sh --help` command


<details>
      <summary>Notice on Highcharts usage</summary>

      Highcharts is a proprietary commercial product. If you enable Highcharts in your DataLens instance (with `HC=1` variable), you should comply with Highcharts license (https://github.com/highcharts/highcharts/blob/master/license.txt).

      When Highcharts is disabled in DataLens, we use D3.js instead. However, currently only a few visualization types are compatible with D3.js. We are actively working on adding D3 support to additional visualizations and are going to completely replace Highcharts with D3 in DataLens.

</details>

<details>
            <summary>How to enable Yandex Maps</summary>

Available since [release v1.11.0](https://github.com/datalens-tech/datalens/releases/tag/v1.11.0)

Use the following container parameters for launch:

| Parameter            | Description                                                     | Values        |
| -------------------- | --------------------------------------------------------------- | ------------- |
| `YANDEX_MAP_ENABLED` | Enable usage of Yandex Maps visualization                       | `1` or `true` |
| `YANDEX_MAP_TOKEN`   | Yandex Maps [API key](https://yandex.ru/dev/jsapi-v2-1/doc/en/) | `<string>`    |

```bash
YANDEX_MAP_ENABLED=1 YANDEX_MAP_TOKEN=XXXXXXXXX docker compose up

# or if you use init.sh script
./init.sh --yandex-map --yandex-map-token XXXXXXXXX --up
```
</details>

## How to update

To update DataLens to the latest version, simply pull git repository and restart the containers:

```bash
git pull

# if you use base compose file
docker compose up

# if you use init.sh script
./init.sh --up
```

All your user settings, connections, and created objects will be preserved as they are stored in the `db-postgres` docker volume. The update process does not affect your data.

## Deploy with Helm chart in k8s cluster

For deployment in a Kubernetes cluster, you can use Helm chart from an OCI-compatible package registry

First install Helm release:

```bash
# generating rsa keys for auth service and temporal
AUTH_TOKEN_PRIVATE_KEY=$(openssl genpkey -algorithm RSA -pkeyopt "rsa_keygen_bits:4096" 2>/dev/null)
AUTH_TOKEN_PUBLIC_KEY=$(echo "${AUTH_TOKEN_PRIVATE_KEY}" | openssl rsa -pubout 2>/dev/null)
TEMPORAL_AUTH_PRIVATE_KEY=$(openssl genpkey -algorithm RSA -pkeyopt "rsa_keygen_bits:4096" 2>/dev/null)
TEMPORAL_AUTH_PUBLIC_KEY=$(echo "${TEMPORAL_AUTH_PRIVATE_KEY}" | openssl rsa -pubout 2>/dev/null)

helm upgrade --install datalens oci://ghcr.io/datalens-tech/helm/datalens \
--namespace datalens --create-namespace \
--set "secrets.AUTH_TOKEN_PRIVATE_KEY=${AUTH_TOKEN_PRIVATE_KEY}" \
--set "secrets.AUTH_TOKEN_PUBLIC_KEY=${AUTH_TOKEN_PUBLIC_KEY}" \
--set "secrets.TEMPORAL_AUTH_PRIVATE_KEY=${TEMPORAL_AUTH_PRIVATE_KEY}" \
--set "secrets.TEMPORAL_AUTH_PUBLIC_KEY=${TEMPORAL_AUTH_PUBLIC_KEY}"
```

**Note:** Helm template engine does not provide built-in functions for creating private and public RSA keys.

Update Helm release:

```bash
helm upgrade datalens oci://ghcr.io/datalens-tech/helm/datalens --namespace datalens
```

Admin login and password will be stored in `datalens-secrets` Kubernetes secret resource

## Parts of the project

DataLens consists of three main parts:

- [**UI**](https://github.com/datalens-tech/datalens-ui) is a SPA application with corresponding Node.js part. It provides user interface, proxies requests from users to backend services, and also applies some light data postprocessing for charts.
- [**Backend**](https://github.com/datalens-tech/datalens-backend) is a set of Python applications and libraries. It is responsible for connecting to data sources, generating queries for them, and post-processing the data (including formula calculations). The result of this work is an abstract dataset that can be used in UI for charts data request.
- [**UnitedStorage (US)**](https://github.com/datalens-tech/datalens-us) is a Node.js service that uses PostgreSQL to store metadata and configuration of all DataLens objects.
- [**Auth**](https://github.com/datalens-tech/datalens-auth) is a Node.js service that provides authentication/authorization layer for DataLens.
- [**MetaManager**](https://github.com/datalens-tech/datalens-meta-manager) is a Node.js service that provides workflow workers for export/import workbooks.

## What's already available

We are releasing DataLens with a minimal set of available connectors (ClickHouse, ClickHouse over YTsaurus, and PostgreSQL) as well as other core functionality such as data processing engine, user interface, and minimal auth layer. We are planning to add missing features based on our understanding of community priorities and your feedback.

## Cloud Providers
Below is a list of cloud providers offering DataLens as a service:
1. [Yandex Cloud](https://datalens.yandex.cloud) platform

## Authentication
DataLens supports native authentication which is enabled by default.

Use the following command to start DataLens with authentication and auto-generated secrets for production:

```bash
./init.sh --up
```

**Notice:** the updated `.env` file after initialization: it contains auth access keys and admin password. Keep that file safe and do not share its contents.

After that you can login to DataLens on http://localhost:8080 using the user credentials:

| Username | Password                               |
| -------- | -------------------------------------- |
| `admin`  | `<AUTH_ADMIN_PASSWORD from .env file>` |

You can use the same credentials to add new users using admin control panel at http://localhost:8080/.

By default in DataLens with authentication enabled, all users have a `datalens.viewer` role. This allows them to use all collections and workbooks in read-only mode. They are not allowed to create or modify any objects with this role. To be able to create or edit objects, they need to have a `datalens.editor` or `datalens.admin` role. To grant these roles, open DataLens at http://localhost:8080, then find the user to whom you want to grant a new role, click on the user, and select the new role.

DataLens supports the following roles:

- `datalens.viewer`: allows viewing all collections and workbooks, but does not allow creating or editing any object.
- `datalens.editor`: includes the `datalens.viewer` role and allows creating, editing and deleting any object.
- `datalens.admin`: currently equal to `datalens.editor`. In the future releases, users with this role will be able to manage system-wide settings and perform administrative functions.

## FAQ

#### Where does DataLens store its metadata?

We use the `db-postgres` docker volume to store PostgreSQL data. If you want to start over, you can delete this volume: it will be recreated with demo objects on the next start of the `datalens` compose.

#### How can I use a custom domain or IP address for the UI container endpoint?

If you use reverse proxy with HTTPS and custom domain, you can generate docker compose file with these arguments:

`./init.sh --domain <domain> --https`

If you use IP address as endpoint, you can generate docker compose file with this argument:

`./init.sh --ip <ip>`

#### How can I specify external PostgreSQL database?

You can check a production deployment example with high availability in a Kubernetes cluster using Tofu (Terraform) in the `terraform/` directory.

If you want to use external PostgreSQL database with Docker, you can specify its connection string in environment variables:

```bash
# .env file example

POSTGRES_HOST=
POSTGRES_PORT=6432
POSTGRES_USER=pg-user
POSTGRES_PASSWORD=
```

If you want to override default database names, you can specify it in environment variables:

```bash
# .env file example

POSTGRES_DB_COMPENG='pg-compeng-db'
POSTGRES_DB_AUTH='pg-auth-db'
POSTGRES_DB_US='pg-us-db'
POSTGRES_DB_META_MANAGER='pg-meta-manager-db'
POSTGRES_DB_TEMPORAL='pg-temporal-db'
POSTGRES_DB_TEMPORAL_VISIBILITY='pg-temporal-visibility-db'
POSTGRES_DB_DEMO='pg-demo-db'
```

After that, you can start DataLens with the following command:

```bash
./init.sh --postgres-external --up
```

It generates a `docker-compose.production.yaml` compose file without postgres container.

#### How can I specify custom certificate for connecting to database?

You can add additional certificates with special argument `--postgres-cert` for `./init.sh` script. This certificate will be mounted to all containers and will be used to connect to the database.

```bash
./init.sh --postgres-external --postgres-ssl --postgres-cert ./cert.pem --up
```

If containers do not start even though you provided correct certificates, try to change `POSTGRES_ARGS` variable for connection in `.env` file to `?sslmode=prefer`.

#### I use external PostgreSQL database and the app doesn't start. What could be the reason?

We use some PostgresSQL extensions for the metadata database. Check your database user's rights for installing extensions or install them manually:

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE EXTENSION IF NOT EXISTS btree_gin;
CREATE EXTENSION IF NOT EXISTS btree_gist;
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
```

By default, automatic extensions installation is disabled with the variable `SKIP_INSTALL_DB_EXTENSIONS=1`. You can enable automatic installation by setting the variable `SKIP_INSTALL_DB_EXTENSIONS=0` in the `.env` file. Applications check for these extensions at startup and try to install them if they haven't been already installed.

If this attempt is unsuccessful, try to install dependencies by database admin.

If you're using a managed database, it's also possible that extensions for your database cluster are controlled by an external system and could be changed only using its UI or API. In such a case, consult the documentation for the managed database service you're using.

#### Why do I see two compose files: docker-compose.yaml & docker-compose.dev.yaml?

#### Хочу отключить авторизацию RPC

Просто закомментируйте `NODE_RPC_URL` в `docker-compose*.yml`

#### Как передать переменные

Лучше создать в корне проекта файл .env и записать туда:
<pre>
HC="1"
NODE_RPC_URL="http://localhost:7000/demo/rpc"
</pre>

Для запуска выполнить `docker compose --env-file ./.env up`

##### Собираем собственный datalens-postgres

<pre>
cd postgres
docker build -t akrasnov87/datalens-postgres:16 .
</pre>

##### Работа с `highcharts`

В сборка datalens-ui с версии 0.2601.2 локально сохранены библиотеки `highcharts`. 
Чтобы они по умолчанию брались из локального хранилища убедитесь, что в compose файле указаны следующие переменные для контейнера `datalens`:

<pre>
HC_ENDPOINT: ${HC_ENDPOINT:-localhost:8080/highcharts}
HC_PROTOCOL: ${HC_PROTOCOL:-http}
</pre>

Но главное помнить про особенности лицензии: https://www.highcharts.com/license

##### Список переменных
* APP_ENV: string - тип приложения, пространства. Добавляется к наименованию контейнеров.
* METADATA_POSTGRES_DSN_LIST: string - подключение к БД PostgreSQL. По умолчанию `postgres://us:us@pg-us:5432/us-db-ci_purgeable`
* METADATA_SKIP_INSTALL_DB_EXTENSIONS: integer - признак для пропуска установки расширений. По умолчанию `0`

При подключении компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth) устанавливается расширение `pgcrypto`.

* USE_DEMO_DATA: integer - признак загрузки демонстрационных данных. По умолчанию `1`
* PROJECT_ID: string - наименование проекта для public.workbooks и public.collections. По умолчанию `datalens-demo`
* HC: integer - признак подключения чартов Highcharts
* NODE_RPC_URL: string - строка подключения компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth). По умолчанию `http://us-auth/demo/rpc`
* AUTH_ENV: string - ~~пространство имён для компонета авторизации [datalens-auth](https://github.com/akrasnov87/datalens-auth). По умолчанию `demo`~~
* UI_PORT: integer - порт, на котором поднимится Datalens. По умолчанию 8080
EXPORT_DASH_EXCEL: boolean - добавляется возможность [экспорта](docs/features.md) данных в Excel для Dashboard

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
docker compose -f docker-compose.dev.yaml --env-file=./.env up -d
</pre>

Просмотр логов:
<pre>
docker compose -f docker-compose.dev.yaml --env-file=./.env logs -n 100
</pre>

File `docker-compose.dev.yaml` is a special compose file that is needed only for development purposes. When you run DataLens in production mode, you always need to use `docker-compose.yaml` file or `./init.sh` script.

#### How can I disable authentication?

If you need to run DataLens without authentication (for development or testing purposes), you can use the `--disable-auth` flag with the init script:

```bash
./init.sh --disable-auth --up
```

This will generate a compose file without the authentication service, allowing direct access to DataLens without login credentials.

#### How can I disable workbook export?

If you want to disable the workbook export feature to save resources or simplify your deployment, use the `--disable-workbook-export` flag:

```bash
./init.sh --disable-workbook-export --up
```

This will disable the workbook export to JSON feature and remove the meta-manager and ui-api services from the deployment.

#### How can I disable Temporal service for limited resources systems?

For systems with limited resources, you can disable the Temporal workflow service using the `--disable-temporal` flag:

```bash
./init.sh --disable-temporal --up
```

This will remove the Temporal service from the deployment, which significantly reduces resource usage. Note that disabling Temporal will also automatically disable the workbook export feature, as it depends on Temporal workflows.

#### What are the minimum system requirements?

* datalens-auth - 256 MB RAM

* datalens-ui - 512 MB RAM

* datalens-data-api - 1 GB RAM

* datalens-control-api - 512 MB RAM

* datalens-us - 512 MB RAM

* datalens-postgres - 512 MB RAM

Minimal configuration:

* RAM - 4 GB

* CPU - 2 CORES

These are the minimal basic system requirements for OpenSource DataLens installation. Actual resource consumption depends on the complexity of requests, connection types, number of users, and processing speed at the source level.

## Тегирование

<pre>
git tag [версия]
git push origin [версия]
</pre>

## Локальное сохранение
<pre>
docker save -o containers/datalens-control-api.tar akrasnov87/datalens-control-api:0.2253.0
docker save -o containers/datalens-data-api.tar akrasnov87/datalens-data-api:0.2253.0
docker save -o containers/datalens-us.tar akrasnov87/datalens-us:0.346.0
docker save -o containers/datalens-ui.tar akrasnov87/datalens-ui:0.2864.0
docker save -o containers/datalens-auth.tar akrasnov87/datalens-auth:0.2.4
docker save -o containers/datalens-postgres.tar akrasnov87/datalens-postgres:16
</pre>