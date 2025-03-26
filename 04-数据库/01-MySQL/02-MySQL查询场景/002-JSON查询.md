---
createTime: 2022-12-08
---
[toc]

```sql
select JSON_EXTRACT(column, '$.CPU') from 1;
```

当查询 json 串中多个值，可使用如下方式，只适合 5.7 以上版本

```sql
SELECT 
    jt.name
FROM 
    data_set_config,
    JSON_TABLE(table_config, '$[0].columnConfig[*]'
    COLUMNS (
        name VARCHAR(255) PATH '$.name'
    )) AS jt
WHERE 
    data_set_name LIKE '%06%';
```
