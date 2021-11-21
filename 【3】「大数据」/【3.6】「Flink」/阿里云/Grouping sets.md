## 语法格式

```sql
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
GROUP BY 
[GROUPING SETS { groupItem [, groupItem ]* } ];
```

## 示例

- 测试数据

  | username | month | day  |
  | :------- | :---- | :--- |
  | Lily     | 10    | 1    |
  | Lucy     | 11    | 21   |
  | Lily     | 11    | 21   |

- 测试案例

  ```sql
  SELECT  
      `month`,
      `day`,
      count(distinct `username`) as uv
  FROM tmall_item
  group by 
  grouping sets((`month`),(`month`,`day`));
  ```

- 测试结果

  | month | day  | uv   |
  | :---- | :--- | :--- |
  | 10    | 1    | 1    |
  | 10    | null | 1    |
  | 11    | 21   | 1    |
  | 11    | null | 1    |
  | 11    | 21   | 2    |
  | 11    | null | 2    |