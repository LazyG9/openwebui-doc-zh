---
sidebar_position: 3
title: "⚙️ Valves（阀门）"
---

# Valves（阀门）

Valves（阀门）是为每个Pipeline设置的输入变量。Valves被设置为`Pipeline`类的子类，并在`Pipeline`类的`__init__`方法中进行初始化。

当向Pipeline添加Valves时，你需要确保管理员能够在Web界面中重新配置这些Valves。这里提供几种实现方式：

- 使用`os.getenv()`来设置Pipeline所需的环境变量，并为未设置的环境变量提供默认值。示例代码如下：

```
self.valves = self.Valves(
    **{
        "LLAMAINDEX_OLLAMA_BASE_URL": os.getenv("LLAMAINDEX_OLLAMA_BASE_URL", "http://localhost:11434"),
        "LLAMAINDEX_MODEL_NAME": os.getenv("LLAMAINDEX_MODEL_NAME", "llama3"),
        "LLAMAINDEX_EMBEDDING_MODEL_NAME": os.getenv("LLAMAINDEX_EMBEDDING_MODEL_NAME", "nomic-embed-text"),
    }
)
```

- 将Valve设置为`Optional`类型，这样即使某个Valve没有设置值，Pipeline也能正常加载。示例代码如下：

```
class Pipeline:
    class Valves(BaseModel):
        target_user_roles: List[str] = ["user"]
        max_turns: Optional[int] = None
```

如果你没有提供在Web界面中更新Valves的方法，当你尝试将Pipeline添加到Web界面时，会在Pipelines服务器日志中看到如下警告：
`WARNING:root:No Pipeline class found in <pipeline name>`（警告：在<pipeline名称>中未找到Pipeline类）
