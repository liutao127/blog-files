---
title: SQL之路
date: 2022-03-19 16:34:59
tags:
---

# 2022-3-19

## 1、查询结果去重

<!-- more -->

{% asset_img  结果去重.png %}

```sql
两个解法：
使用DISTINCT：SELECT DISTINCT university from user_profile;
使用group by：SELECT university from user_profile GROUP BY university;
```

## 2、结果限制返回行数

```sql
题目：现在运营只需要查看前2个用户明细设备ID数据，请你从用户信息表 user_profile 中取出相应结果。

select device_id from user_profile limit 0,2

--LIMIT n：从第0+1(m=0)条开始，取n条数据，是LIMIT 0,n的缩写
SELECT device_id
FROM user_profile
LIMIT 2 

--LIMIT m,n:从第m+1条开始，取n条数据
SELECT device_id
FROM user_profile
LIMIT 0,2

--LIMIT n OFFSET m：从第m+1条开始，取n条数据
SELECT device_id
FROM user_profile
LIMIT 2 OFFSET 0

```

## 3、查询后重命名列名

```sql
题目：现在你需要查看前2个用户明细设备ID数据，并将列名改为 'user_infos_example',，请你从用户信息表取出相应结果。

select device_id from user_profile as user_infos_example limit 2
```

