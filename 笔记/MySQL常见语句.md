#### 🟢 一、修改数据库字符集

如果你想直接改整个数据库：

`ALTER DATABASE your_database_name   CHARACTER SET = utf8mb4   COLLATE = utf8mb4_0900_ai_ci;`

> ⚠️ 注意：这个只会影响**新建的表或列**，不会自动改已有表和列。

---

#### 🟢 二、修改表默认字符集

改表级别：

`ALTER TABLE your_table_name   CONVERT TO CHARACTER SET utf8mb4   COLLATE utf8mb4_0900_ai_ci;`

**这个语句：**  
✅ 会把所有`CHAR`, `VARCHAR`, `TEXT`列一起修改  
✅ 数据也会自动转换  
✅ 原索引也会自动重建

---

#### 🟢 三、修改列字符集

如果只想改部分列：

`ALTER TABLE your_table_name   MODIFY COLUMN column_name VARCHAR(255)   CHARACTER SET utf8mb4   COLLATE utf8mb4_0900_ai_ci;`