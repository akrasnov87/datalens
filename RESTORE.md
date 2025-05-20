# Описание

После перехода на версию `v2.1.0` в интерфейсе `Datalens` нет возможность, что-то сделать с подключением - выдаётся ошибка.


__Примечание__: ниже описана инструкция по восстановлению этого подключения. ВЫПОЛНЯЕТСЯ НА СВОЙ СТРАХ И РИСК!!!

## Восстановление подключения

1. В папке с проектом выполняем команду:

<pre>
sudo chown 777 ./postgres/*.sh
</pre>

2. Находим файл ./postgres/us-restore.sh и вносим следующие изменения:

Строку `NULL_PASSWORD=$(python /init/crypto.py "${CONTROL_API_CRYPTO_KEY}" null)`

Заменяем на `NULL_PASSWORD=$(python3 crypto.py "${CONTROL_API_CRYPTO_KEY}" null)`

__Примечание__: 

* замена делается только для блока `if [ "${IS_RESET_PASSWORDS}" == "true" ]; then`
* команда `python` меняется на `python3` по требованию

3. Выполняем команду:

<pre>
env POSTGRES_HOST=[Адрес PostgreSQL сервера] POSTGRES_PORT=5432 POSTGRES_USER_US=[пользователь для подключения] POSTGRES_PASSWORD_US=[пароль для подключения] CONTROL_API_CRYPTO_KEY=[ключ из control-api и data-api] POSTGRES_DB_US=[имя БД] ./us-restore.sh --reset-passwords
</pre>

Пример: 
<pre>
env POSTGRES_HOST="192.168.1.1" POSTGRES_PORT=5432 POSTGRES_USER_US="pg-user" POSTGRES_PASSWORD_US=12345 CONTROL_API_CRYPTO_KEY="h1ZpilcYLYRdWp7Nk8X1M1kBPiUi8rdjz9oBfHyUKIk=" POSTGRES_DB_US="database-name" ./us-restore.sh --reset-passwords
</pre>

4. Выполняем команду:

<pre>
env POSTGRES_HOST=[Адрес PostgreSQL сервера] POSTGRES_PORT=5432 POSTGRES_USER_US=[пользователь для подключения] POSTGRES_PASSWORD_US=[пароль для подключения] CONTROL_API_CRYPTO_KEY=[ключ из control-api и data-api] POSTGRES_DB_US=[имя БД] ./us-restore.sh --reset-crypto-key
</pre>

<pre>
env POSTGRES_HOST="192.168.1.1" POSTGRES_PORT=5432 POSTGRES_USER_US="pg-user" POSTGRES_PASSWORD_US=12345 CONTROL_API_CRYPTO_KEY="h1ZpilcYLYRdWp7Nk8X1M1kBPiUi8rdjz9oBfHyUKIk=" POSTGRES_DB_US="database-name" ./us-restore.sh --reset-crypto-key
</pre>

5. Переходим в интерфейс `Datalens` и для подключений меняем пароль (в карточку должно пустить). Выше мы как раз сбрасываем его на шаге 3.