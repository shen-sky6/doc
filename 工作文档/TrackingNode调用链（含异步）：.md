
### ✅ 一图看懂调用链（含异步）：
``` plaintext

[定时器调度器 / 定时任务]
        │
        ▼
callTaskRun(LocalDateTime now)
        │
        ▼
callTracking(trackingIndexDO, time, warnRuleList)
        │
        ├──> callEventDimension(...)
        │       │
        │       └──> nodeList = NodeType.values() where dimensionType == EVENT
        │               ├─ if == EVENT_VEIN → EventVeinCalc.calculate(...)
        │               ├─ if == EVENT_BOARD → EventBoardCalc.calculate(...)
        │               └─ 其他节点类型 → xxxCalc.calculate(...)
        │
        ├──> callArticleDimension(...)
        │       └──> 类似逻辑（通过 NodeType 映射找到处理类）
        │
        └──> callAuthorDimension(...)
                └──> 类似逻辑（通过 NodeType 映射找到处理类）

最终写入数据库：TrackingNodeInfo（记录每个节点类型的数据）

部分触发预警：
└──> warnService.tracking(...)
```
## 🔍 调用链中的关键交互说明

### ☑ `NodeType` 是中心调度索引

java

复制编辑

`public enum NodeType {     EVENT_BOARD(..., EVENT, BoardDO.class, BoardViewVO.class),     EVENT_VEIN(..., EVENT, String.class, String.class),     ... }`

每个 `NodeType` 都代表一个“节点类型”，你调度时就是遍历这些。

---

### ☑ `TaskService.callEventDimension()` 逻辑片段

java

复制编辑

`// 拿到 NodeType 列表，遍历 for (NodeType nodeType : NodeType.values()) {     if (nodeType.getDimensionType() != DimensionType.EVENT) continue;      NodeStepCalculate func = nodeTypeCalculateMap.get(nodeType.name());     Object data = func.calculate(trackingIndexDO, dimensionDO, trackingNodeInfoDO);     // 存库 }`

这段是 **核心多态分发机制**：

- 遇到 `EVENT_VEIN`：分发给 `EventVeinCalc`
    
- 遇到 `EVENT_BOARD`：分发给 `EventBoardCalc`
    
- 其他类似
    

这依赖于你提前配置好的：

java

复制编辑

`Map<String, NodeStepCalculate> nodeTypeCalculateMap;`

通常是用 Spring `@Component` 扫描所有实现 `NodeStepCalculate` 接口的类自动注入。

---

### ☑ 各计算器（如 `EventBoardCalc`）内部调用

以 `EventBoardCalc` 为例：

```java
public Object calculate(...) {     return eventEsTemplate.getBoardDO(trackingIndexDO, dimensionDO, nodeInfoDO); }
```



而 `EventVeinCalc` 则更复杂：

```java
public Object calculate(...) {     
	// 循环等待结果出现，查 ES     
	BaseResponse<NewReportReq> report = columnService.createReport(...);     
	return getChildEventList(reportId); 
}
```



---

## ✅ 最终数据落地

每次计算完成会调用：

```java
trackingNodeInfoMapper.insert(new TrackingNodeInfo(...));
```



记录这个节点的：

- 类型（如 EVENT_BOARD）
    
- 时间点（nodeTaskTime）
    
- 内容数据（nodeTaskData）
    
- 状态字段（是否变化、是否可见）
    

---

## 🧯 预警触发链路

如果某些节点返回结果中包含了：

- 敏感词命中
    
- 大V发声
    
- 央媒参与等
    

则调用：

```java
warnService.tracking(warnDetailList);
```

你可以配置自定义规则，触发告警通知或生成日报。

---

## 🎯 图形概览（简版）

```plaintext
        ┌──────────────┐
        │  定时任务调度 │
        └─────┬────────┘
              ▼
      [TaskService.callTaskRun()]
              ▼
      [callTracking()]
              ▼
 ┌────────────┼──────────────┐
 │            │              │
 ▼            ▼              ▼
事件维度   文章维度       作者维度
 │            │              │
 ▼            ▼              ▼
EventBoardCalc.calculate()  ...
EventVeinCalc.calculate()   ...
 │
 ▼
结果写入 TrackingNodeInfo

```


---

## 🧩 总结

|维度|内容|
|---|---|
|统一调度入口|`TaskService.callTracking()`|
|关键分发机制|`NodeType → NodeStepCalculate`|
|核心执行类|`EventBoardCalc`, `EventVeinCalc`, 其他 `XXXCalc`|
|存储结构|`TrackingNodeInfo`|
|预警机制|`warnService.tracking()`|
|支持异步回溯|`asyncBacktrack()`|