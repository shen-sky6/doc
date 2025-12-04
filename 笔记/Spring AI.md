### @Tool vs @McpTool 精简笔记版

| 项目       | `@Tool`                   | `@McpTool`                            |
| -------- | ------------------------- | ------------------------------------- |
| 所属框架     | Spring AI 官方              | Spring AI Community（MCP 扩展）           |
| 功能定位     | 给 **AI Agent 内部调用**       | 给 **外部客户端远程调用**                       |
| 调用方式     | 内部函数调用 / LLM 自动选择         | 通过 HTTP / SSE / WebSocket 调用          |
| 注册机制     | Spring AI 自动扫描注册          | MCP Server 扫描注册为远程可调用接口               |
| 是否依赖网络协议 | 否                         | 是（遵循 MCP 协议）                          |
| 使用场景     | Chat Agent、LLM Chain 内部工具 | Python、JS 等客户端远程调用                    |
| 关键依赖     | `spring-ai-core`          | `spring-ai-starter-mcp-server-webmvc` |
