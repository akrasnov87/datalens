## Связанные объекты

В разделе "Связанные объекты" добавлен функционал обеспечивающий "перенос" текущих прав объекта на связанные. Такой механизм позволяет быстро применить права доступа на связанные объекты. Например, если доступ предоставлен к dashboard, то благодаря этому механизму можно быстро перенести права на чатры, просто передвинув "переключатель".

__Примечание__: "отбор" прав осуществляется аналогично назначению, нужно передвинуть переключатель, чтобы системы применила новые права доступа.

![Связанное наследование](../../pics/image10.png)

__Примечание__: данный механизм не позволяет "отбирать" права. Назначение прав через этот функционал - это лишь копирование прав родительского объекта на связанные. При этом предыдущие права "перетираются".


### Особенности

1. В некоторых ситуациях назначенные права не влияют на отображение. Примером может быть назначение прав на объекты `Датасеты` и `Подключения` для роли `Пользователь` (datalnes).

Даже если принудительно указать, что этот объект должен быть доступен для редактирования (или просмотра) - в списке они отображаться не будут. 

2. Если для роли `user` давать права на редактирование объектов - они работать не будут, хотя отображение функционала будет доступно. Эта особанность так же связана с тем, что у этой роли права только для чтения.