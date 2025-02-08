## 共享函数组件

### 控制阀和用户控制阀 - (可选，但强烈推荐)

控制阀和用户控制阀用于允许用户提供动态配置，如 API 密钥或配置选项。这将在函数的图形用户界面菜单中创建可填充字段或开关按钮。

控制阀仅限管理员配置，而用户控制阀可由所有用户配置。

<details>
<summary>示例</summary>

```
# 定义控制阀
    class Valves(BaseModel):
        priority: int = Field(
            default=0, description="过滤操作的优先级"
        )
        test_valve: int = Field(
            default=4, description="用于控制数值的控制阀"
        )
        pass

    # 定义用户控制阀
    class UserValves(BaseModel):
        test_user_valve: bool = Field(
            default=False, description="控制开关状态的用户控制阀"
        )
        pass

    def __init__(self):
        self.valves = self.Valves()
        pass
```
</details>

### 事件触发器
事件触发器用于向聊天界面添加补充信息。与过滤器出口类似，事件触发器能够向聊天内容追加信息，但与过滤器出口不同，它们不能移除信息。此外，触发器可以在函数执行的任何阶段被激活。

事件触发器分为两种类型：

#### 状态
用于在执行过程中为消息添加状态信息。这可以在函数的任何阶段执行。状态信息会显示在消息内容的正上方。这对于需要延迟 LLM 响应或处理大量数据的函数特别有用，可以实时向用户展示处理进度。

```
await __event_emitter__(
            {
                "type": "status", # 设置类型为状态
                "data": {"description": "在聊天中显示的状态信息", "done": False}, 
                # done 为 False 表示状态更新仍在继续
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

        :param test: 测试参数
        """

        await __event_emitter__(
            {
                "type": "status", # 设置类型为状态
                "data": {"description": "在聊天中显示的状态信息", "done": False}, 
                # done 为 False 表示状态更新仍在继续
            }
        )

        # 执行其他逻辑
        await __event_emitter__(
            {
                "type": "status",
                "data": {"description": "任务已完成", "done": True},
                # done 为 True 表示状态更新结束
            }
        )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"出现错误：{e}", "done": True},
                }
            )

            return f"向用户报告：{e}"
```
</details>

#### 消息
此类型用于在函数执行的任何阶段向 LLM 添加消息。您可以在 LLM 响应前、响应中或响应后添加消息、嵌入图像，甚至渲染网页内容。

```
await __event_emitter__(
                    {
                        "type": "message", # 设置类型为消息
                        "data": {"content": "此内容将添加到聊天中"},
                        # 消息类型无需设置完成状态
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

        :param test: 测试参数
        """

        await __event_emitter__(
                    {
                        "type": "message", # 设置类型为消息
                        "data": {"content": "此内容将添加到聊天中"},
                        # 消息类型无需设置完成状态
                    }
                )

        except Exception as e:
            await __event_emitter__(
                {
                    "type": "status",
                    "data": {"description": f"出现错误：{e}", "done": True},
                }
            )

            return f"向用户报告：{e}"
```
</details>
