## Под капотом

Механизм авторизации и безопасности хранится в специальной схеме `core` БД PostgreSQL. По умолчанию подключение к ней доступно по следующей строке `postgres://us:us@pg-us:5432/us-db-ci_purgeable`.

### Таблицы

* core.pd_roles - роли пользователей `datalens`;
* core.pd_users - пользователи;
* core.pd_userinroles - связка пользователей и ролей;
* core.pd_projects - проекты (дополнительный уровень абстракции для разграничения прав);
* core.pd_accesses - таблица прав доступа.
* public.dl_access - динамическое представление прав доступа.

### Функции

* core.of_users(sender: jsonb, params jsonb) - список доступных пользователей в интерфейсе `datalens`, где sender - это объект из функции core.sf_users(_f_user integer), а params - это параметры фильтрации;
* core.pf_update_user_roles(_user_id: integer, _claims: json) - обновление ролей у пользователя;
* core.sf_create_oidc_user - Создание пользователя авторизовавшегося через OIDC
* core.sf_create_user - Создание пользователя
* core.sf_reset_pwd - Сброс пароля пользователя
* core.sf_users(_f_user: integer) - Системная функция. Получение информации о пользователе

__Примечание__: это не полный набор функций, остальные больше системные и лучше их напрямую не вызывать.