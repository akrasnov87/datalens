### Авторизация

Доступ в интерфейс Datalens предоставляется только авторизованным пользователям:

![Авторизация](pics/image3.png)

Отображение текущего авторизованного пользователя в "шапке" главной страницы.

![Логин авторизованного пользователя](pics/image4.png)

### Авторизация через OIDC

Доступна возможность авторизации через `OIDC` провайдер, можно подключить до `4`. Все авторизованные таким образом пользователи по умолчанию имеют права только для чтения (роль `datalens` в таблице `core.pd_roles`).

![OIDC](pics/image8.png)

### Права доступа

В интерфейс встроена примитивная "ролевая" модель, где есть:

* Супер (мастер) пользователь - неограниченный доступ.
* Администратор - доступ только в рамках текущего проекта. Решение предусматривает, что могут быть несколько проектов, которые "крутятся" в одной инсталяции Datalens, и у каждого проекта свой администратор и пользователи.
* Пользователь - доступна возможность только просмотра.

![Назначение прав](pics/image5.png)

По умолчанию все объекты запрещены, чтобы было отображение, требуется выдавать права на это.

### Поделиться

Доступна возможность передать чарт или дашборд, как ссылку:

![Поделиться](pics/image6.png)

__Безопасность__

В интерфейсе "Поделиться объектом" генерируется уникальная ссылка, которая хранит "автора", который передаёт ссылку и отчёго имени будет разрешён просмотр:

![Поделиться объектом](pics/image7.png)

`Внимание`: Пользователь, который получит ссылку будет иметь доступ только для просмотра этого объекта и больше никуда. Редактирование будет запрещено.

Если нужно отменить выданную ссылку, то нужно нажать кнопку "Обновить ссылку". Один объект - одна ссылка.

### Использование встроенных параметров для QL-чартов

По умолчанию в интерфейс передаются:

* __user_id: integer - идентикатор текущего авторизованного пользователя
* __embed: integer - признак перехода из расшаренной ссылки, -1 - Нет, 1 - Да

Чтобы это параметры были доступны в SQL запросах и требуется определить в параметрах:

![Параметры](pics/image1.png)

![Запрос](pics/image2.png)

Благодаря этим системным параметрам можно внутри запроса QL-чарта фильтровать данные.

__Описание для параметров__

Это специальное поле, которые было заведено для сохранение дополнительных значений, которые можно передать в третьи системы (особенность версии, которая делалсь заказчику).