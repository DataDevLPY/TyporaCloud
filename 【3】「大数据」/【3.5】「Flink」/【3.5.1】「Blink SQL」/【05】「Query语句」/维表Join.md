## 维表JOIN语法

```sql
SELECT column-names
FROM table1  [AS <alias1>]
[LEFT] JOIN table2 FOR SYSTEM_TIME AS OF PROCTIME() [AS <alias2>]
ON table1.column-name1 = table2.key-name1;
```

事件流JOIN白名单维表，示例如下。

```sql
SELECT e.*, w.*
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF PROCTIME() AS w
ON e.id = w.id;
```



## 示例

- 测试数据

  | id（bigint） | name（varchar） | age（bigint） |
  | :----------- | :-------------- | :------------ |
  | 1            | lilei           | 22            |
  | 2            | hanmeimei       | 20            |
  | 3            | libai           | 28            |

  | name（varchar） | phoneNumber（bigint） |
  | :-------------- | :-------------------- |
  | dufu            | 18867889855           |
  | baijuyi         | 18867889856           |
  | libai           | 18867889857           |
  | lilei           | 18867889858           |

- 测试语句

  ```sql
  CREATE TABLE datahub_input1 (
  id            BIGINT,
  name        VARCHAR,
  age           BIGINT
  ) WITH (
  type='datahub'
  );
  
  create table phoneNumber(
  name VARCHAR,
  phoneNumber bigint,
  primary key(name),
  PERIOD FOR SYSTEM_TIME
  )with(
  type='rds'
  );
  
  CREATE table result_infor(
  id bigint,
  phoneNumber bigint,
  name VARCHAR
  )with(
  type='rds'
  );
  
  INSERT INTO result_infor
  SELECT
  t.id,
  w.phoneNumber,
  t.name
  FROM datahub_input1 as t
  JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w
  ON t.name = w.name;
  ```

- 测试结果

  | id（bigint） | phoneNumber（bigint） | name（varchar） |
  | :----------- | :-------------------- | :-------------- |
  | 1            | 18867889858           | lilei           |
  | 3            | 18867889857           | libai           |

