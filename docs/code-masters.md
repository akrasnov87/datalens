### Обращение к серверным процедурам из datalens-ui

`datalens-ui` состоит из двух компонент это серверная часть и клиентская. Для обращения к функциям из `datalens-auth` можно использовать:

#### обращение из клиентского интерфейса

Подключаем файл `~/src/utils/utils.ts` и обращаемся методу класса Utils

<pre>
Utils.universalService(propsData: any)
</pre>

Где `propsData` - это объект со следующей структурой:

<pre>
{   
    action: string, 
    method:string, 
    data: any[], 
    tid: number
}
</pre>

* action - имя серверного класса
* method - имя серверного метода
* data - массив данных для передачи в серверный метод
* tid - нумерной идентификатор, по умолчанию 0

## обращение из серверного интерфейса

Подключаем файл `~/src/server/components/sdk/us.ts` и обращаемся методу класса `US`:

<pre>
var result = await US.universalService({
    "action": "datalens", 
    "method": "currentUser", 
    "data": [{}]
}, req.headers, req.ctx);
</pre>

* action - имя серверного класса
* method - имя серверного метода
* data - массив данных для передачи в серверный метод