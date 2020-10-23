## 学习过程

`pymysql`是Python3版本用于连接MySQL服务器的一个库，可以使用pip命令安装。



#### 连接数据库：

```python
import pymysql

# 连接MySQL数据库（注意：charset参数是utf8而不是utf-8）
conn = pymysql.connect(host='localhost', 
                       user='root', 
                       password='MySQL密码', 
                       db='数据库名称', 
                       charset='utf8')

cur = conn.cursor() # 创建游标对象
```



#### 建库建表：

```python
# 创建数据库
cur.execute("create database 数据库名称")

# 使用数据库
cur.execute("use 数据库名称")

# 创建表
sql_create = """
                create table 表名称( 
                列名称 INT NOT NULL,
                列名称 CHAR(10) NOT NULL
                )
"""

try:
    cur.execute(sql_create)
except Exception as e:
    print(e)
    conn.rollback()
    print('数据库创建操作错误回滚')
```

> NOT NULL：对于一些可填可不填的字段，把它设置为非空
>
> TINYINT：从 0 到 255 的整型数据，存储大小为 1 字节
>
> PRIMARY KEY & UNIQUE：约束唯一标识数据库表中的每条记录
>
> AUTO INCREMENT：在新记录插入表中时生成一个唯一的数字



#### 增删查改：

```python
# 增：insert into
sql_insert = """
                INSERT INTO 表名称(列名称, 列名称)
                VALUES(%d, %s)
"""

data = [
    (int, string),
    (int, string)
]

try:
    # 批量插入
    cur.executemany(sql_insert, data)
    # 提交执行
    conn.commit()
except Exception as e:
    print(e)
    conn.rollback()
    print('数据库插入操作错误回滚')
    
# 查询：select
sql_select = "SELECT * FROM 表名称 WHERE 列名称 = '值'"

try:
    # 执行SQL语句
    cur.execute(sql_select)
    # 获取所有记录列表
    results = cur.fetchall()
    # print(results)
    for row in results:
        song = row[0]
        album = row[1]
        # 打印结果
        print ("列名称：%d，列名称：%s" % (列名称, 列名称))
except Exception as e:
    print(e)
    print ("Error: unable to fetch data")
    
# 改：UPDATE
sql_update = "UPDATE 表名称 SET 列名称 = '值' WHERE 列名称 = '%s'" % ('值')

try:
   # 执行SQL语句
   cur.execute(sql_update)
   # 提交到数据库执行
   conn.commit()
except Exception as e:
    print(e)
    conn.rollback()
    print('数据库更新操作错误回滚')
    
# 删：DELETE
sql_delete = "DELETE FROM 表名称 WHERE 列名称 = '值'"

try:
   # 执行SQL语句
   cur.execute(sql_delete)
   # 提交修改
   conn.commit()
except Exception as e:
    print(e)
    conn.rollback()
    print('数据库删除操作错误回滚')
```



##### 其他操作：

```python
# 使用fetchone()方法获取单条数据
data = cur.fetchone()

# 使用fetchall()方法获取所有数据
data = cur.fetchall()

cur.close() # 关闭游标
conn.close() # 关闭数据库
```



## 爬虫

以前都是把爬虫爬取的数据存入csv文件等等，现在尝试一下直接把爬取的数据存入数据库。

```python
import csv
import pymysql
import requests

# 连接MySQL数据库（注意：charset参数是utf8而不是utf-8）
conn = pymysql.connect(host='localhost', user='root', password='xjyxjy0723', db='music', charset='utf8')

# 创建游标对象
cur = conn.cursor()

url = 'https://c.y.qq.com/soso/fcgi-bin/client_search_cp'
name = '华晨宇'
page = 2

for x in range(page):
    params = {
    'ct':'24',
    'qqmusic_ver': '1298',
    'new_json':'1',
    'remoteplace':'sizer.yqq.song_next',
    'searchid':'64405487069162918',
    't':'0',
    'aggr':'1',
    'cr':'1',
    'catZhida':'1',
    'lossless':'0',
    'flag_qc':'0',
    'p':str(x+1),
    'n':'20',
    'w':name,
    'g_tk':'5381',
    'loginUin':'0',
    'hostUin':'0',
    'format':'json',
    'inCharset':'utf8',
    'outCharset':'utf-8',
    'notice':'0',
    'platform':'yqq.json',
    'needNewCode':'0'    
    }
    
    res = requests.get(url, params=params)
    json = res.json()
    lis = json['data']['song']['list']
    
    data = []
    for music in lis:
        song_name = music['name'] # 以song_name为键，查找歌曲名，把歌曲名赋值给name
        album = music['album']['name'] # 查找专辑名，把专辑名赋给album
        data.append([song_name,album])

    for each in data:
        i = tuple(each)
        sql = "INSERT INTO hcy VALUES" + str(i)
        cur.execute(sql) #执行SQL语句
    
    conn.commit() # 提交数据
cur.close() # 关闭游标
conn.close() # 关闭数据库
print("成功存入数据库")
```



## 总结

1. 最近学习有点后劲不足
2. 课内知识有一些没跟上



## 计划

- 爬虫
  - 模拟登录
  - 代理池
  - Ajax
- 机器学习
  - 随机森林
  - GBDT

