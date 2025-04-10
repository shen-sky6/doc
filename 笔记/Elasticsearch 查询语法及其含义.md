# Elasticsearch 查询核心语法及其含义：

### 1. `bool` 查询

- **作用**：组合多个子查询条件，支持逻辑操作（`must`、`should`、`must_not`、`filter`）。
- **关键子句**：
  - `filter`：过滤条件，不参与相关性评分，仅筛选符合条件的文档。
  - `should`：逻辑“或”，满足任意一个条件即可（需结合 `minimum_should_match` 使用）。
  - `must_not`：逻辑“非”，排除符合条件的文档。

### 2. **`range` 查询**

- **作用**：按数值或时间范围过滤。

- **示例**：

  ```json
  "range": {
    "publish_date": {
      "from": 1743558220000,
      "to": 1743644620000,
      "include_lower": true,
      "include_upper": false
    }
  }
  ```

- **参数**：

  - `from`/`to`：范围的起点和终点。
  - `include_lower`：是否包含下限（`>=`）。
  - `include_upper`：是否包含上限（`<=`）。

### 3. **`term` 查询**

- **作用**：精确匹配某个字段的值（适用于不分词的字段，如 `keyword`）。
- **示例**：
  ```json
  "term": {
    "author.site_type": {
      "value": "微博",
      "boost": 1.0
    }
  }
  ```

- **参数**：
  - `value`：要匹配的值。
  - `boost`：权重因子（影响相关性评分）。
### 4. **`terms` 查询**

- **作用**：匹配字段的多个可能值（类似 SQL 的 `IN`）。
- **示例**：

  ```json
  "terms": {
    "item_type": ["original", "repost", "comment"],
    "boost": 1.0
  }
  ```

### 5. **`exists` 查询**

- **作用**：检查字段是否存在（非空）。
- **示例**：
  ```json
  "exists": {
    "field": "sentiment",
    "boost": 1.0
  }
  ```

### 6. **`multi_match` 查询**

- **作用**：在多个字段中搜索文本（支持分词）。

- **示例**：

  ```json
  "multi_match": {
    "query": "dreams",
    "fields": ["asr_text^1.0", "content.wordsENN^1.0", ...],
    "type": "phrase",
    "operator": "AND"
  }
  ```

- **参数**：

  - `type`：匹配类型（如 `phrase` 表示短语匹配）。
  - `operator`：逻辑操作（`AND` 要求所有词项都匹配）。
  - `slop`：允许词项之间的最大间隔（用于模糊短语匹配）。

### 7. **嵌套逻辑组合**

- **示例**：

  ```json
  "bool": {
    "should": [
      {"term": {"author.site_type": "微博"}},
      {"term": {"site_name": "小红书"}}
    ],
    "minimum_should_match": 1
  }
  ```

  - 表示 `author.site_type` 为“微博” **或** `site_name` 为“小红书”。
  - `minimum_should_match: 1` 要求至少满足一个条件。

### 8. **排除条件 (`must_not`)**

- **示例**：

  ```json
  "must_not": {
    "term": {"labels": "政治敏感"}
  }
  ```

  - 排除 `labels` 字段为“政治敏感”的文档。

### 9. **分页与排序**

- **参数**（仅第二个查询包含）：

  ```json
  "from": 0,
  "size": 20,
  "sort": [{"publish_date": {"order": "desc"}}]
  ```

  - `from`：起始位置（偏移量）。
  - `size`：返回的文档数量。
  - `sort`：按 `publish_date` 降序排列。

### 10. **`_source` 过滤**

- **作用**：控制返回的字段。

- **示例**：

  ```json
  "_source": {
    "excludes": ["cluster_result", "crawl_timestamp", ...]
  }
  ```

  - `excludes`：排除指定字段。

### 11. **其他参数**

- `adjust_pure_negative`：优化纯否定查询的性能（默认 `true`）。
- `boost`：调整查询条件的权重（影响相关性评分）。

### 总结

这两个查询通过复杂的 `bool` 嵌套组合了多种条件，包括：

1. 时间范围过滤（`range`）。
2. 精确匹配（`term`/`terms`）。
3. 存在性检查（`exists`）。
4. 文本搜索（`multi_match`）。
5. 逻辑排除（`must_not`）。
6. 分页、排序和字段过滤。

它们的主要目的是筛选出符合特定时间范围、平台类型（微博/小红书）、内容关键词（`dreams`/`fear`）、情感分析字段（`sentiment`）的文档，同时排除敏感内容。