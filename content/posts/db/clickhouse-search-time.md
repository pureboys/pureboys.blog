---
title: "ClickHouse时间查询"
summary: "ClickHouse时间查询"
date: 2023-01-09T13:00:29+08:00
categories: ["db"]
---

### ClickHouse时间查询

1. 近几个小时

```SQL
SELECT
        time
FROM
        wiki_logs.wiki_logs
where
        subtractHours(time, 7) > toDate(now())
```

2. 今天

```SQL
SELECT
        time
FROM
        wiki_logs.wiki_logs
where
        toDate(time) = today()
```

3. 昨天

```SQL
SELECT
        time
FROM
         wiki_logs.wiki_logs
where
        toDate(time) = yesterday()
```

4. 近几天

```SQL
SELECT
        time 
FROM
        wiki_logs.wiki_logs
where
        subtractDays(time, 7) < toDate(now())
```

5. 本月

```SQL
SELECT
        time
FROM
         wiki_logs.wiki_logs
where
        toMonth(time) = toMonth(now())
```

6. 按时间段（自定义）

```SQL
SELECT 
        toDate(time) t
FROM
         wiki_logs.wiki_logs
where 
        time BETWEEN yesterday() and subtractHours(time, 1)
```

7. 每天时间段内

```SQL
select
        *
from
        wiki_logs.wiki_logs
where
        toYYYYMMDDhhmmss(time)%1000000 BETWEEN '123000' and '160000'
```

8. 按小时分组

```SQL
SELECT 
        concat(toString(toHour(time)), '-', toString(toHour(time) + 1), '点') tacticsDate,
        count(*) tacticsCount
FROM
         wiki_logs.wiki_logs
GROUP BY
        toHour(time)
```

9. 按天分组

```SQL
SELECT 
        toDate(time) tacticsDate,
        count(*) tacticsCount
FROM
       wiki_logs.wiki_logs
GROUP BY
        toDate(time)
```