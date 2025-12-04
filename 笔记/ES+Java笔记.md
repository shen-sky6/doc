# 🧭 Elasticsearch Java Client 查询结构笔记

## 一、核心类与作用

| 类名                        | 作用描述                               | 举例                                           |
| ------------------------- | ---------------------------------- | -------------------------------------------- |
| **`ElasticsearchClient`** | 客户端对象，用于执行请求（连接 ES）                | `client.search(request, MyDoc.class)`        |
| **`SearchRequest`**       | 一次完整的搜索请求配置（相当于 DSL 根节点）           | 包含 index、query、from、size、sort、aggregations 等 |
| **`Query`**               | 表示任意一种查询（match、term、range、bool...） | `Query.of(q -> q.match(...))`                |
| **`BoolQuery`**           | 组合多个查询的逻辑查询（and/or/not/filter）     | 包含 must、should、must_not、filter 四种逻辑          |
| **`SearchResponse`**      | 返回结果的封装类                           | 可通过 `response.hits().hits()` 获取结果            |

---

## 二、类之间的层级关系
```cpp
ElasticsearchClient        // 执行入口
 └── SearchRequest          // 一次完整查询请求
       └── Query             // 单一查询条件（match/term/range/bool...）
             └── BoolQuery    // 组合查询（must/should/filter/must_not）

```

---

## 三、简单查询示例（不用 BoolQuery）

> 查询标题中包含“美国”的文档
```java
SearchRequest request = new SearchRequest.Builder()
        .index("news")
        .query(q -> q.match(m -> m
                .field("title")
                .query("美国")
        ))
        .build();

SearchResponse<MyDoc> response = client.search(request, MyDoc.class);

```

对应的 DSL：
```json
{
  "query": {
    "match": {
      "title": "美国"
    }
  }
}

```
---

## 🧩 三、结构层级（例子）

你执行的典型逻辑是这样的：

```java
// 1️⃣ 获取 Elasticsearch 客户端
ElasticsearchClient client = esClientManager.getClientByIndex("my_index");

// 2️⃣ 构造一个 bool 查询
BoolQuery boolQuery = new BoolQuery.Builder()
        .must(q -> q.match(m -> m.field("title").query("iPhone")))
        .mustNot(q -> q.term(t -> t.field("author").value("广告号")))
        .should(q -> q.match(m -> m.field("content").query("Apple")))
        .minimumShouldMatch("1")
        .build();

// 3️⃣ 构建 SearchRequest（相当于整个查询体）
SearchRequest searchRequest = new SearchRequest.Builder()
        .index("my_index")                      // 查询哪个索引
        .query(Query.of(q -> q.bool(boolQuery))) // 这里装入 BoolQuery
        .size(100)                               // 取多少条
        .sort(s -> s.field(f -> f.field("publish_timestamp").order(SortOrder.Desc)))
        .build();

// 4️⃣ 执行查询
SearchResponse<Map> response = client.search(searchRequest, Map.class);

// 5️⃣ 解析结果
response.hits().hits().forEach(hit -> {
    System.out.println(hit.source());
});

```

---

## 🧩 四、类与类之间的关系（更直白）

|类名|属于谁|主要方法|
|---|---|---|
|`ElasticsearchClient`|独立的对象|`.search(request, class)` 发起查询|
|`SearchRequest`|由你创建，传给客户端|`.index()`, `.query()`, `.sort()`|
|`Query`|放进 SearchRequest.query()|`.of(q -> q.bool(...))` 或 `.of(q -> q.match(...))`|
|`BoolQuery`|是 Query 的一种|`.must()`, `.should()`, `.mustNot()`|

---

## 🧱 五、你现在项目里的层级（结合你代码）

你的方法中这几行：

`SearchRequest.Builder builder = new SearchRequest.Builder(); builder.index(indices); builder.size(count); ... builder.query(q -> q.bool(b -> b.must(mustQueries).mustNot(mustNotQueries))); SearchRequest searchRequest = builder.build(); SearchResponse<Map> response = esClient.search(searchRequest, Map.class);`

可以这样理解：

|代码|意义|
|---|---|
|`builder`|我正在写一封“查询请求信”|
|`.index(indices)`|告诉它我想查哪些索引|
|`.query()`|信的核心内容（也就是查询条件）|
|`.bool()`|查询条件的逻辑结构|
|`.must()` / `.should()` / `.mustNot()`|相当于 SQL 的 `AND`、`OR`、`NOT`|
|`esClient.search()`|把这封“信”发出去给 Elasticsearch|
|`response`|Elasticsearch 回信了（包含搜索结果）|

---

## 🧩 六、简单类比总结

|SQL语句|Elasticsearch Java客户端等价|
|---|---|
|`SELECT * FROM table WHERE title LIKE '%iPhone%' AND price > 500`|`SearchRequest.query(Query.of(q -> q.bool(b -> b.must(match).must(range))))`|
|`WHERE A AND (B OR C)`|`BoolQuery.must(A).should(B).should(C).minimumShouldMatch("1")`|
|`ORDER BY date DESC`|`.sort(s -> s.field(f -> f.field("date").order(Desc)))`|

---

## 💡 最后建议

可以这么记：

> **ElasticsearchClient** 发请求  
> **SearchRequest** 是请求体  
> **Query** 是条件  
> **BoolQuery** 是 Query 的一种（逻辑组合查询）

```java
.type(TextQueryType.BestFields)
.operator(Operator.And)
```

| 类型                                             | `operator` 是否生效 | 含义                 |
| ---------------------------------------------- | --------------- | ------------------ |
| `phrase`                                       | ❌ 无效            | 短语匹配（按顺序）          |
| `best_fields` / `most_fields` / `cross_fields` | ✅ 有效            | 控制多个词之间是 AND 还是 OR |
| `phrase_prefix`                                | ❌ 无效            | 短语前缀匹配             |
