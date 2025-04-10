## **Elasticsearch 主要查询语法**的分类和详细说明，帮助您全面掌握其核心功能：

------

### 一、**全文检索查询（Full Text Queries）**

用于在文本字段中执行分词搜索（基于分析器）。

#### 1. **`match` 查询**

- **用途**：对文本字段进行分词搜索（支持模糊匹配、短语匹配等）。

- **示例**：

  ```json
  {
    "query": {
      "match": {
        "content": "elasticsearch tutorial"
      }
    }
  }
  ```
  
- **参数**：

  - `operator`：默认为 `OR`，可设为 `AND` 要求所有词项匹配。
  - `fuzziness`：允许模糊匹配（如 `"AUTO"` 或固定编辑距离）。
  - `analyzer`：指定分词器。

#### 2. **`match_phrase` 查询**

- **用途**：精确匹配短语（词序和间隔必须一致）。

- **示例**：

  ```json
  {
    "query": {
      "match_phrase": {
        "content": "quick brown fox"
      }
    }
  }
  ```
  
- **参数**：

  - `slop`：允许词项之间的最大间隔（如 `slop: 2` 表示允许插入两个词）。

#### 3. **`match_phrase_prefix` 查询**

- **用途**：匹配短语前缀（最后一个词项可以前缀匹配）。

- **示例**：

  ```json
  {
    "query": {
      "match_phrase_prefix": {
        "content": "elast sear"
      }
    }
  }
  ```

#### 4. **`multi_match` 查询**

- **用途**：在多个字段中执行相同的搜索。

- **示例**：

  ```json
  {
    "query": {
      "multi_match": {
        "query": "search",
        "fields": ["title^2", "content"]  // `title` 字段权重为 2
      }
    }
  }
  ```
  
- **类型**：

  - `best_fields`：默认，取最佳匹配字段的评分。
  - `most_fields`：合并多个字段的评分。
  - `phrase`：类似 `match_phrase`。
  - `cross_fields`：跨字段匹配（如将查询词拆分到不同字段）。

------

### 二、**精确值查询（Term-level Queries）**

用于精确匹配未经分词的字段（如 `keyword`、数值、日期等）。

#### 1. **`term` 查询**

- **用途**：精确匹配字段的单个值。

- **示例**：

  ```json
  {
    "query": {
      "term": {
        "status": "published"
      }
    }
  }
  ```

#### 2. **`terms` 查询**

- **用途**：匹配字段的多个可能值（类似 SQL 的 `IN`）。

- **示例**：

  ```json
  {
    "query": {
      "terms": {
        "tags": ["es", "database", "search"]
      }
    }
  }
  ```

#### 3. **`range` 查询**

- **用途**：匹配数值、日期等范围。

- **示例**：

  ```json
  {
    "query": {
      "range": {
        "price": {
          "gte": 100,
          "lt": 200
        }
      }
    }
  }
  ```
  
  - 操作符：`gt`（>）、`gte`（>=）、`lt`（<）、`lte`（<=）。

#### 4. **`exists` 查询**

- **用途**：匹配字段存在的文档。

- **示例**：

  ```json
  {
    "query": {
      "exists": {
        "field": "author"
      }
    }
  }
  ```

#### 5. **`prefix` 查询**

- **用途**：匹配字段值的前缀。

- **示例**：

  ```json
  {
    "query": {
      "prefix": {
        "user_id": "user_"
      }
    }
  }
  ```

#### 6. **`wildcard` 查询**

- **用途**：通配符匹配（`*` 匹配任意字符，`?` 匹配单个字符）。

- **示例**：

  ```json
  {
    "query": {
      "wildcard": {
        "title": "elast*"
      }
    }
  }
  ```

#### 7. **`regexp` 查询**

- **用途**：正则表达式匹配。

- **示例**：

  ```json
  {
    "query": {
      "regexp": {
        "ip": "192.168.0.[0-9]{1,3}"
      }
    }
  }
  ```

------

### 三、**复合查询（Compound Queries）**

组合多个查询条件的逻辑操作。

#### 1. **`bool` 查询**

- **用途**：通过逻辑子句组合查询。

- **子句**：

  - `must`：必须满足（逻辑 `AND`，影响评分）。
  - `filter`：必须满足（逻辑 `AND`，不参与评分）。
  - `should`：至少满足一个（逻辑 `OR`）。
  - `must_not`：必须不满足（逻辑 `NOT`）。

- **示例**：

  ```json
  {
    "query": {
      "bool": {
        "must": [
          { "match": { "title": "elasticsearch" } }
        ],
        "filter": [
          { "range": { "date": { "gte": "2023-01-01" } } }
        ],
        "must_not": [
          { "term": { "status": "deleted" } }
        ]
      }
    }
  }
  ```

#### 2. **`boosting` 查询**

- **用途**：降低某些条件的权重。

- **示例**：

  ```json
  {
    "query": {
      "boosting": {
        "positive": { "match": { "title": "search" } },
        "negative": { "match": { "content": "slow" } },
        "negative_boost": 0.2  // 降低匹配 `negative` 条件的文档评分
      }
    }
  }
  ```

#### 3. **`constant_score` 查询**

- **用途**：固定评分（常用于 `filter` 场景）。

- **示例**：

  ```json
  {
    "query": {
      "constant_score": {
        "filter": { "term": { "status": "active" } },
        "boost": 1.2
      }
    }
  }
  ```

------

### 四、**地理位置查询（Geo Queries）**

用于地理坐标数据的搜索。

#### 1. **`geo_distance` 查询**

- **用途**：搜索指定半径内的点。

- **示例**：

  ```json
  {
    "query": {
      "geo_distance": {
        "distance": "10km",
        "location": {
          "lat": 40.715,
          "lon": -73.988
        }
      }
    }
  }
  ```

#### 2. **`geo_bounding_box` 查询**

- **用途**：搜索矩形区域内的点。

- **示例**：

  ```json
  {
    "query": {
      "geo_bounding_box": {
        "location": {
          "top_left": { "lat": 40.8, "lon": -74.0 },
          "bottom_right": { "lat": 40.7, "lon": -73.9 }
        }
      }
    }
  }
  ```

------

### 五、**嵌套与父子查询（Nested & Joins）**

用于处理嵌套对象或父子关系文档。

#### 1. **`nested` 查询**

- **用途**：查询嵌套对象字段。

- **示例**：

  ```json
  {
    "query": {
      "nested": {
        "path": "comments",
        "query": {
          "bool": {
            "must": [
              { "match": { "comments.text": "awesome" } }
            ]
          }
        }
      }
    }
  }
  ```

#### 2. **`has_child` 和 `has_parent` 查询**

- **用途**：基于父子关系的查询。

- **示例**（查找有子文档的父文档）：

  ```json
  {
    "query": {
      "has_child": {
        "type": "comment",
        "query": { "match": { "text": "elasticsearch" } }
      }
    }
  }
  ```

------

### 六、**其他高级查询**

#### 1. **`script` 查询**

- **用途**：通过脚本自定义查询逻辑。

- **示例**：

  ```json
  {
    "query": {
      "script": {
        "script": {
          "source": "doc['price'].value > params.min_price",
          "params": { "min_price": 100 }
        }
      }
    }
  }
  ```

#### 2. **`function_score` 查询**

- **用途**：自定义评分函数（如根据热度、时间衰减等调整评分）。

- **示例**：

  ```json
  {
    "query": {
      "function_score": {
        "query": { "match": { "title": "elasticsearch" } },
        "functions": [
          { "exp": { "date": { "scale": "30d" } } }  // 时间衰减函数
        ]
      }
    }
  }
  ```

------

### 七、**聚合查询（Aggregations）**

虽然聚合主要用于数据分析，但常与查询结合使用。

#### 1. **指标聚合（Metric Aggregations）**

- `avg`、`sum`、`min`、`max`、`stats`（综合统计）、`cardinality`（唯一值计数）。

#### 2. **桶聚合（Bucket Aggregations）**

- `terms`：按字段值分组。
- `date_histogram`：按时间区间分组。
- `range`：按自定义范围分组。

#### 3. **管道聚合（Pipeline Aggregations）**

- `avg_bucket`、`derivative`（导数）等，基于其他聚合结果进一步计算。

------

### 八、**查询与过滤上下文**

- **Query Context**：参与相关性评分（如 `must`）。
- **Filter Context**：仅过滤文档，不参与评分（如 `filter`、`must_not`）。

------

### 总结

Elasticsearch 的查询语法覆盖了从简单到复杂的各种场景：

1. **全文检索**：`match`, `multi_match`, `match_phrase`。
2. **精确查询**：`term`, `terms`, `range`, `exists`。
3. **逻辑组合**：`bool`, `constant_score`。
4. **地理位置**：`geo_distance`, `geo_bounding_box`。
5. **高级功能**：`script`, `function_score`, 嵌套查询。

实际使用时，需根据字段类型（是否分词）、性能需求（是否使用 `filter` 缓存）和业务场景选择合适的查询方式。建议结合官方文档 ([Elasticsearch Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)) 进一步学习！