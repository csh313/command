### 将数据库文件sql导入到数据库中
#### 如果是db文件需要转化成sql
1.安装sqlite3工具
```bash
sudo apt install sqlite3
sqlite3 --version  # 查看版本，确认安装成功
sqlite3 mydatabase.db  # 创建或打开名为 mydatabase.db 的数据库
# 进入交互模式后，可执行 SQL 命令，例如：
CREATE TABLE test (id INT, name TEXT);
INSERT INTO test VALUES (1, 'sqlite test');
SELECT * FROM test;
.quit  # 退出交互模式
# 使用 sqlite3 的 .dump 命令导出所有数据和结构
sqlite3 your_database.db .dump > sqlite_data.sql
# 移除 SQLite 特有语句（如 BEGIN TRANSACTION 等）
sed -i '/^BEGIN TRANSACTION/d' sqlite_data.sql
sed -i '/^COMMIT/d' sqlite_data.sql
sed -i '/^PRAGMA/d' sqlite_data.sql
sed -i "s/\"//g" sqlite_data.sql  # 移除双引号（MySQL 用反引号）
sed -i 's/AUTOINCREMENT/AUTO_INCREMENT/g' sqlite_data.sql
```
2. dps 查看mysql容器名
3. docker cp sqlite_data.sql mysql-master:/tmp/  #将文件导入到mysql容器内 
4. docker exec -it mysql-master /bin/bash 
   mysql -uroot -p123456 hmshop < /tmp/sqlite_data.sql
    
