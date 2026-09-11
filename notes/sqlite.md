# SQLite

**是什么**：轻量级关系型数据库。把结构化数据存在本地一个文件里，用 SQL 做查询、修改和统计。

**在哪用**：个人桌面软件、手机应用、小型网站、本地数据分析工具、浏览器和客户端的本地存储、项目原型、自动化脚本、测试环境、单机 AI 应用、嵌入式设备。凡是本地单机、数据量不大、写入不密集的场景都合适。

**特点**：

1. **不需要单独安装数据库服务器**。MySQL、PostgreSQL 是 `程序 → 网络连接 → 数据库服务器`；SQLite 直接嵌进 Python 程序，是 `程序 → 本地数据库文件`。所以配置极简，适合个人项目和本地应用。
2. **数据在单个文件里**。例如 `data/study_organizer.db`，这个文件里就是库中的表、字段和记录——**复制这个文件基本等于复制了整个数据库**。
3. **支持 SQL**。建表、INSERT、SELECT、UPDATE、DELETE、排序 / 分组 / 统计、事务、索引、表关联都支持。
4. **Python 自带**。标准库里的 `sqlite3`，通常不需要额外安装。

## 典型用法

```python
import sqlite3

conn = sqlite3.connect("data/study_organizer.db")
conn.row_factory = sqlite3.Row            # 结果可以按列名取，比按下标取可读

conn.execute("CREATE TABLE IF NOT EXISTS docs (id INTEGER PRIMARY KEY, title TEXT, body TEXT)")
conn.execute("INSERT INTO docs (title, body) VALUES (?, ?)", ("讲义1", "正文…"))   # 参数化，别拼字符串
conn.commit()

rows = conn.execute("SELECT * FROM docs WHERE title LIKE ?", ("%讲义%",)).fetchall()
conn.close()
```

配合 pandas：`pd.read_sql_query(sql, conn)` 读、`df.to_sql("docs", conn, if_exists="replace", index=False)` 写。

## 注意事项

1. **是动态类型**：只保证 5 种存储类（NULL / INTEGER / REAL / TEXT / BLOB），没有真正的 bool 和 datetime。日期一般存 ISO 格式字符串；写进去什么类型、读出来什么类型不保证一致。
2. **必须用参数化查询**（`?` 占位符），不要把用户输入拼进 SQL 字符串。
3. **别忘 `commit()`**，否则数据不落盘。注意 `with sqlite3.connect(...) as conn:` 只自动提交事务，**并不会关闭连接**，还是得自己 `close()`。
4. 用完及时关连接。Windows 上连接没关会导致文件被占用，删不掉、覆盖不了。
5. 并发写入会锁库，报 `database is locked`。缓解办法：连接时给 `timeout=`，或开 WAL 模式 `PRAGMA journal_mode=WAL`。
6. **`.db` 是二进制单文件，不要提交进 git**——它既含数据，又每次改动整个文件都变，会让仓库迅速膨胀。应该写进 `.gitignore`。
7. 数据量上来后，常用作查询条件的列要建索引（`CREATE INDEX`）。

### 什么时候不该用它

不适合大量用户同时写入、多台服务器共访同一个库、非常高的并发请求、复杂的权限管理、以及数据库需要独立远程部署的场景。本质是为**单机单写入者**设计的；本地的个人小工具一般碰不到这些边界。
