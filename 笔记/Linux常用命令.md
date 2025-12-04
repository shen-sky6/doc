
在 Linux 里最常用的就是 **`grep`** 命令 🔍。

比如你要在日志文件里找 `ERROR`：

`grep "ERROR" app.log`

常见用法：

1. **忽略大小写**
`grep -i "error" app.log`

2. **显示行号**

`grep -n "ERROR" app.log`

3. **同时查多个关键词**（正则 OR）

`grep -E "ERROR|WARN" app.log`

4. **实时查看日志并过滤关键词

`tail -f app.log | grep "ERROR"`

5. **只看匹配到的次数

`grep -c "ERROR" app.log`

---

⚡常用组合：

- 追日志时只看报错：
	`tail -f app.log | grep --color=always "ERROR"`

### ✅ **方法 1：grep + -A/-B/-C**

这是最常用的方式。

`grep -A 3 -B 2 "关键词" /path/to/your.log`

含义：

- `-A 3`：显示匹配行**后面** 3 行（After）
    
- `-B 2`：显示匹配行**前面** 2 行（Before）
    
- `-C 5`：显示匹配行前后各 5 行（Context）