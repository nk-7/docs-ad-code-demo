# Указание ttl в liquibase для Cassandra

Для БД Cassandra в liquibase скриптах необходимо явно указывать время жизни, даже если значение совпадает с дефолтным.
Если при инсерте указывается ttl отличный от того, что задан при создании таблицы, нужно обосновать это решение в комментариях.

Пример:
```
create table if not exists cash_register_param.entity
(
    qrc_id              text,
    params_id           text,
    amount              text,
    currency            text,
    payment_purpose     text,
    alive_until_time    timestamp,
    fraud_score         text,
    trx_id              text,
    primary key ((qrc_id), params_id)
    )
    with default_time_to_live = 86400
        and gc_grace_seconds = 10800
        and compaction = {
        'class':  'TimeWindowCompactionStrategy',
        'compaction_window_unit': 'MINUTES',
        'compaction_window_size': 60
        };
```
**default_time_to_live** это TTL (время жизни) в секундах (0 TTL означает, что срок жизни не установлен, поэтому ваши записи будут сохраняться навсегда)

**gc_grace_seconds** этот параметр контролирует, как долго Cassandra будет хранить tombstone


## Обоснование
При отсутсвии явно указанного времени для жизни данные будут храниться бессрочно, важно, чтобы это было осознанным выбором.
