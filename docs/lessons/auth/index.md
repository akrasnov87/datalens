## Авторизация

### OIDC

В интерфейс Datalens можно получить доступ не только через внутреннюю авторизацию, но и через внешнюю. Основой этого механизма является OIDC провайдер, который интегрирован в образ `datalens-ui`.

Чтобы этот способ авторизации был доступен требуется в файл с переменными (в нашем случаи .env.demo) добавить следующие строки:

<pre>
OIDC=true
OIDC_ISSUER=https://accounts.google.com/.well-known/openid-configuration
OIDC_BASE_URL=https://your.datalens.ru/auth/v1/oidc
OIDC_CLIENT_ID=528815932068-1srcvm37rg4ne3epc.apps.googleusercontent.com
OIDC_SECRET=GOCSPX-xQMuiGWGw4JwFVl
OIDC_NAME="GOOGLE (пример)"
</pre>

<b>Примечание</b>: чтобы воспользоваться данным функционал на локальном компьютере потребуется дополнительное ПО, подробнее по [ссылке](ngrok_use.md).

`Datalens` не ограничен только подключением одного провайдера `OIDC`. Доступна поддержка до 4 одновременно подключённых провайдеров. Для этого в переменных нужно указать несколько провайдеров с их номером (потребуется ещё прокунуть эти переменные и в yml-файл):

<pre>
OIDC_2=true
OIDC_ISSUER_2=https://accounts.google.com/.well-known/openid-configuration
OIDC_BASE_URL_2=https://your.datalens.ru/auth/v2/oidc
OIDC_CLIENT_ID_2=528815932068-1srcvm37rg4ne3epc.apps.googleusercontent.com
OIDC_SECRET_2=GOCSPX-xQMuiGWGw4JwFVl
OIDC_NAME_2="GOOGLE 2 (пример)"
</pre>

В указанном выше примере появился суффикс `_2` после каждой переменной OIDC, плюс в переменной `OIDC_BASE_URL` указывается версия `v2` (далее по аналогии v3 и v4).

Таким образом пользователю будет доступно два способа авторизации, через внешнии системы.

__Примечание__: можно подключать любые авторизации через внешнии системы, главное чтобы они поддерживали OIDC.

[Как проверить?](ngrok_use.md)