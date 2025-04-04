# Часть 4

Пример кода на SQL - триггер.

```sql
INSERT INTO legal_and_merchant.legal_data (
    legal_id, legal_entity_id, agent_id, name, country, countrysubdivision, city, zip, address, record_source_id, record_effective_date, record_terminated, record_created_date, record_created_user_id, record_archived_date, record_archived_user_id, subscriptions_restricted
)
VALUES (
    CONCAT('LID', LPAD(FLOOR(RAND() * 1000000), 9, '0')), -- legal_id (случайный 12-значный ID)
    CONCAT('LE', LPAD(FLOOR(RAND() * 1000000), 10, '0')), -- legal_entity_id (случайный 12-значный ID)
    CONCAT('AID', LPAD(FLOOR(RAND() * 1000000), 9, '0')), -- agent_id (случайный 12-значный ID)
    CONCAT('Legal Data Name ', FLOOR(RAND() * 1000)), -- name (случайное имя)
    ELT(FLOOR(RAND() * 5) + 1, 'US', 'RU', 'CN', 'IN', 'BR'), -- country (случайная страна)
    ELT(FLOOR(RAND() * 5) + 1, 'CA', 'NY', 'TX', 'FL', 'WA'), -- countrysubdivision (случайный регион)
    ELT(FLOOR(RAND() * 5) + 1, 'New York', 'Moscow', 'Beijing', 'Mumbai', 'Sao Paulo'), -- city (случайный город)
    LPAD(FLOOR(RAND() * 1000000), 6, '0'), -- zip (случайный почтовый индекс)
    CONCAT(FLOOR(RAND() * 100), ' Random Street'), -- address (случайный адрес)
    UUID(), -- record_source_id (случайный UUID)
    DATE(NOW() - INTERVAL FLOOR(RAND() * 365) DAY), -- record_effective_date (случайная дата за последний год)
    FLOOR(RAND() * 2), -- record_terminated (0 или 1)
    NOW(), -- record_created_date (текущая дата и время)
    FLOOR(RAND() * 1000), -- record_created_user_id (случайный ID пользователя)
    NOW(), -- record_archived_date (текущая дата и время)
    FLOOR(RAND() * 1000), -- record_archived_user_id (случайный ID пользователя)
    FLOOR(RAND() * 2) -- subscriptions_restricted (0 или 1)
);
```
