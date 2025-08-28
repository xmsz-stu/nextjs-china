# surrealdb





可以默认设置create\_at和update\_at



基础的任务表

```

DEFINE TABLE task_record TYPE ANY SCHEMALESS PERMISSIONS NONE;

-- FIELDS

DEFINE FIELD created_at ON task_record TYPE datetime DEFAULT time::now() VALUE $before OR time::now() PERMISSIONS FULL; DEFINE FIELD status ON task_record TYPE string PERMISSIONS FULL; DEFINE FIELD task ON task_record TYPE record<task> PERMISSIONS FULL; DEFINE FIELD updated_at ON task_record TYPE datetime VALUE time::now() PERMISSIONS FULL; DEFINE FIELD user ON task_record TYPE record<user> PERMISSIONS FULL;

-- INDEXES

DEFINE INDEX idx_status ON task_record FIELDS status; DEFINE INDEX idx_task ON task_record FIELDS task; DEFINE INDEX idx_user ON task_record FIELDS user;
```



基础任务表

```
DEFINE TABLE tasks TYPE ANY SCHEMALESS PERMISSIONS FOR select WHERE true, FOR create, update, delete WHERE user = $auth.id;
```





取值

**结论**

* 默认子查询返回数组（即使只有一条记录）
* 用 `[0]` 取出第一个对象，user\_status 就是对象（或 NONE）
* 用 `[0].status` 取出值，user\_status 就是具体值





组合唯一

```
DEFINE INDEX idx_task_user_unique ON task_record FIELDS tasks, user UNIQUE;
```



计数

```
SELECT *,
    (
        SELECT count() FROM task_record WHERE tasks = $parent.id
    ) AS current_interaction_count
FROM some_table;
```



计数累计

```
RETURN count(SELECT * FROM task_record WHERE tasks = $parent.id);

```



查询变量

```
LET $cnt = count(SELECT * FROM task_record WHERE tasks = $parent.id);
RETURN $cnt;
```



&#x20;有效期查询

在 SurrealQL 中，如果你想查询字段 expireAt 大于当前时间（以毫秒为单位的时间戳），可以使用下面的语法：

```
SELECT * FROM your_table WHERE expireAt > time::now();
```

其中：

* your\_table 替换为你的实际表名。
* time::now() 返回当前的时间戳（精确到毫秒），可以用于和 expireAt 字段比较。

如果你的 expireAt 字段是以毫秒时间戳（Unix epoch 毫秒数）存储，而 time::now() 返回的是 SurrealDB 的时间类型（通常是 ISO 8601 字符串），需要将 time::now() 转换为毫秒时间戳格式进行比较。可以这样写：

```
SELECT * FROM your_table WHERE expireAt > time::unix(time::now());
```

\




#### 正确判断子查询有无结果

你需要判断返回数组的长度是否大于 0，推荐写法如下：

```
AND array::len(
  SELECT * FROM task_record WHERE tasks = id LIMIT 1
) > 0
```

这样只有当有匹配记录时才为真。如果你只关心某个字段（比如 status），可以用：

```
AND array::len(
  SELECT status FROM task_record WHERE tasks = id AND user = $user LIMIT 1
) > 0
```

或者，直接判断第一个元素是否不是 NONE：

```
AND (
  SELECT status FROM task_record WHERE tasks = id AND user = $user LIMIT 1
)[0] IS NOT NONE
```

这种方式能正确过滤掉没有数据的情况。





y







