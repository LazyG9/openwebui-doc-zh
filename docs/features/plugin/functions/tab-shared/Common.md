## 共享函数组件

### 阀门和用户阀门 - (可选，但强烈建议使用)

阀门和用户阀门用于允许用户提供动态详细信息，如 API 密钥或配置选项。这些将在给定函数的 GUI 菜单中创建一个可填充字段或布尔开关。

阀门仅由管理员配置，而用户阀门可由任何用户配置。

<details>
<summary>示例</summary>

```
# 定义阀门
    class Valves(BaseModel):
        priority: int = Field(
            default=0, description="过滤器操作的优先级。"
        )
        test_valve: int = Field(
            default=4, description="控制数值的阀门"
        )
        pass

    # 定义用户阀门
    class UserValves(BaseModel):
        test_user_valve: bool = Field(
            default=False, description="控制 True/False（开/关）开关的用户阀门"
        )
        pass

    def __init__(self):
        self.valves = self.Valves()
        pass
```
</details>

### 事件发射器
事件发射器用于向聊天界面添加额外信息。与过滤器出口类似，事件发射器能够向聊天中追加内容。与过滤器出口不同，它们不能删除信息。此外，发射器可以在函数的任何阶段被激活。

有两种不同类型的事件发射器：

#### 状态
这用于在执行步骤时向消息添加状态。这可以在函数的任何阶段完成。这些状态显示在消息内容的正上方。这对于延迟 LLM 响应或处理大量信息的函数非常有用。这允许您实时告知用户正在处理什么。

```
await __event_emitter__(
            {
                "type": "status", # 在这里设置类型
                "data": {"description": "在聊天中显示的消息", "done": False}, 
                # 注意这里 done 为 False，表示我们仍在发出状态
            }
        )
```

<details>
<summary>示例</summary>

```
async def test_function(
        self, prompt: str, __user__: dict, __event_emitter__=None
    ) -> str:
        """
        这是一个演示

        :param test: 这是一个测试参数
        """

        await __event_emitter__(
            {
                "type": "status", # 在这里设置类型
                "data": {"description": "在聊天中显示的消息", "done": False}, 
                # 注意这里 done 为 False，表示我们仍在发出状态
            }
        )

        # 在这里执行其他逻辑
        await __event_emitter__(
            {
                "type": "status",
                "data": {"description": "完成任务的消息", "done": True},
                # 注意这里 done 为 True，表示我们已完成发出状态
            }
        )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"发生错误：{e}", "done": True},
                }
            )

            return f"告诉用户：{e}"
```
</details>

#### 消息
此类型用于在函数的任何阶段向 LLM 追加消息。这意味着您可以在 LLM 响应之前、之后或期间追加消息、嵌入图像，甚至渲染网页。

```
await __event_emitter__(
                    {
                        "type": "message", # 在这里设置类型
                        "data": {"content": "此消息将被追加到聊天中。"},
                        # 注意，对于消息类型，我们不必设置完成条件
                    }
                )
```

<details>
<summary>示例</summary>

```
async def test_function(
        self, prompt: str, __user__: dict, __event_emitter__=None
    ) -> str:
        """
        这是一个演示

        :param test: 这是一个测试参数
        """

        await __event_emitter__(
                    {
                        "type": "message", # 在这里设置类型
                        "data": {"content": "此消息将被追加到聊天中。"},
                        # 注意，对于消息类型，我们不必设置完成条件
                    }
                )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"发生错误：{e}", "done": True},
                }
            )

            return f"告诉用户：{e}"
```
</details>
