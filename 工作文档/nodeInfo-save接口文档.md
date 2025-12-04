### 2. 新增节点信息

- **接口地址**：`POST /seity/chongqing2/nodeInfo/save`
- **请求方式**：POST
- **请求体参数**（JSON）：

| 字段名     | 类型          | 是否必填 | 说明                           |
| ---------- | ------------- | -------- | ------------------------------ |
| id         | Integer       | 否       | 主键，自增或由系统生成         |
| type       | Integer       | 是       | 1=敏感时间节点，2=重要事件节点 |
| name       | String        | 是       | 名称                           |
| descr      | String        | 否       | 说明                           |
| keywords   | String        | 否       | 关键词（逗号分隔）             |
| startTime  | LocalDateTime | 是       | 开始时间                       |
| endTime    | LocalDateTime | 是       | 结束时间                       |
| companyId  | String        | 是       | 公司ID                         |
| createBy   | String        | 否       | 创建人                         |
| createTime | LocalDateTime | 否       | 创建时间                       |
| updateBy   | String        | 否       | 修改人                         |
| updateTime | LocalDateTime | 否       | 修改时间                       |

- **请求示例**：

```
json复制编辑{
  "type": 2,
  "name": "敏感节点B",
  "descr": "这是描述内容",
  "keywords": "事件,报警",
  "startTime": "2025-04-10T08:00:00",
  "endTime": "2025-04-10T18:00:00",
  "companyId": "654321",
  "createBy": "admin",
  "createTime": "2025-04-14T13:00:00",
  "updateBy": "admin",
  "updateTime": "2025-04-14T13:00:00"
}
```

- **响应示例**：

```
json复制编辑{
  "code": 200,
  "message": "success",
  "data": true
}
```

------

## 🧩 字段说明（NodeInfo）

| 字段名     | 类型          | 描述                                 |
| ---------- | ------------- | ------------------------------------ |
| id         | Integer       | 主键                                 |
| type       | Integer       | 类型：1=敏感时间节点，2=重要事件节点 |
| name       | String        | 节点名称                             |
| descr      | String        | 节点说明                             |
| keywords   | String        | 关键词                               |
| startTime  | LocalDateTime | 开始时间                             |
| endTime    | LocalDateTime | 结束时间                             |
| companyId  | String        | 所属公司 ID                          |
| createBy   | String        | 创建人                               |
| createTime | LocalDateTime | 创建时间                             |
| updateBy   | String        | 修改人                               |
| updateTime | Local         |                                      |