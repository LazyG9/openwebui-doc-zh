---
sidebar_position: 3
title: "⚙️ Valves（阀门）"
---

# Valves（阀门）

Valves（阀门）是针对每个管道（Pipeline）设置的配置参数。它们被定义为 `Pipeline` 类的子类，并在 `Pipeline` 类的 `__init__` 方法中进行初始化。

在向管道添加 Valves 时，需要确保管理员能够在 Web UI 中动态配置这些参数。你可以通过以下几种方式实现：

- 使用 `os.getenv()` 设置环境变量，并指定默认值。当环境变量未设置时，将使用默认值。示例如下：

```
self.valves = self.Valves(
    **{
        "LLAMAINDEX_OLLAMA_BASE_URL": os.getenv("LLAMAINDEX_OLLAMA_BASE_URL", "http://localhost:11434"),
        "LLAMAINDEX_MODEL_NAME": os.getenv("LLAMAINDEX_MODEL_NAME", "llama3"),
        "LLAMAINDEX_EMBEDDING_MODEL_NAME": os.getenv("LLAMAINDEX_EMBEDDING_MODEL_NAME", "nomic-embed-text"),
    }
)
```

- 将 Valve 设置为 `Optional` 类型，这样即使某个参数未设置值，管道仍然可以正常加载：

```
class Pipeline:
    class Valves(BaseModel):
        target_user_roles: List[str] = ["user"]
        max_turns: Optional[int] = None
```

如果你没有提供在 Web UI 中更新 Valves 的机制，当尝试将管道添加到 Web UI 时，Pipelines 服务器日志中会出现以下警告：
`WARNING:root:No Pipeline class found in <pipeline name>`
