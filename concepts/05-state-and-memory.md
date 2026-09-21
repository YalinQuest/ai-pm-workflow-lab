# State 和 Memory：AI 助手如何记住“正在做什么”和“关于你什么”

刚开始接触 AI Agent 时，我把 State 和 Memory 都理解成“让模型记住信息”。后来发现，它们虽然都能帮助系统保留信息，负责的却不是同一件事。

我现在会这样区分：

> **State 记录当前任务进行到哪一步、已经知道什么；Memory 记录以后可能还用得上的背景或偏好。**

## State：当前任务的工作台

假设我让旅行助手规划一趟旅行：“我想 11 月去美国，预算两万元。”系统可以把目前收集到的信息放进 State：

```json
{
  "destination": "美国",
  "departure_month": "2026-11",
  "budget_cny": 20000,
  "departure_city": null,
  "stage": "collecting_information"
}
```

助手发现还不知道从哪里出发，就继续询问。等我补充“从上海出发”，State 会更新，流程再进入下一步。

所以 State 不只是订单里的“待付款、已发货、已完成”这种状态，也可以包含当前任务收集到的参数、缺失的信息、流程进度，以及是否正在等待用户确认。它像一张当前任务的工作表，让程序知道事情办到哪儿了。

## Memory：以后可能会用到的背景

如果旅行助手知道我平时喜欢慢节奏、偏爱自然景点，这些信息可能会对之后的旅行规划有帮助：

```json
{
  "travel_preferences": ["喜欢慢节奏", "偏爱自然景点"],
  "usual_departure_city": "上海"
}
```

这些信息属于 Memory。应用可以在相关任务中取出它们，作为背景交给模型，让我不必每次从头说明。不过 Memory 是参考，不是命令：如果我这次说“我想密集地逛城市景点”，当前任务的明确要求应该优先。

Memory 也不一定是系统偷偷观察后自动打的标签。它可以来自用户明确说“请记住”，也可以是应用谨慎整理的历史偏好。好的应用还应该考虑哪些信息值得保存、什么时候过期，以及如何让用户查看、修改或删除。

## 放到不同场景里看

### 1. 售后客服：查询订单与补齐信息

用户说：“帮我查一下订单有没有发货。”系统需要先判断正在处理什么、还缺什么信息，而不是马上假装查到了。

当前任务的 State 可以是：

```json
{
  "intent": "查询物流",
  "order_id": null,
  "identity_verified": false,
  "stage": "waiting_for_order_id",
  "pending_action": "询问订单号"
}
```

助手会问：“请提供订单号。”用户补充 `A20261018` 后，State 更新为：

```json
{
  "intent": "查询物流",
  "order_id": "A20261018",
  "identity_verified": false,
  "stage": "waiting_for_verification",
  "pending_action": "完成身份验证"
}
```

订单号齐全也不一定就能直接查询，程序可能还要完成身份验证。校验通过后，程序才调用订单工具。工具返回“已发货”后，这个结果是从订单系统查来的，不是 Memory：

```json
{
  "order_id": "A20261018",
  "shipping_status": "已发货",
  "tracking_number": "SF1234567890",
  "source": "订单系统"
}
```

用户偏好简短回复、习惯通过短信接收通知，则可能属于 Memory：

```json
{
  "reply_style": "简短、直接",
  "notification_preference": "短信"
}
```

**这里要分清：**正在处理的订单号、验证进度属于 State；沟通偏好可能属于 Memory；“订单到底发没发货”必须查询业务系统。

### 2. 旅行规划：逐轮补充行程条件

用户先说：“我想 11 月去美国，预算两万元。”当前 State 记录已知条件和缺失条件：

```json
{
  "task": "旅行规划",
  "destination": "美国",
  "departure_month": "2026-11",
  "departure_city": null,
  "budget_cny": 20000,
  "travelers": null,
  "stage": "collecting_information",
  "missing_fields": ["departure_city", "travelers"]
}
```

助手可以问：“从哪个城市出发？有几位旅行者？”用户答“从上海出发，两个人”，State 继续更新：

```json
{
  "task": "旅行规划",
  "destination": "美国",
  "departure_month": "2026-11",
  "departure_city": "上海",
  "budget_cny": 20000,
  "travelers": 2,
  "stage": "ready_to_search",
  "missing_fields": []
}
```

旅行助手的 Memory 可以保存相对稳定的偏好：

```json
{
  "travel_preferences": ["喜欢慢节奏", "偏爱自然景点"],
  "usual_departure_city": "上海"
}
```

规划本次行程时，应用可以把这些偏好作为参考。假如用户这次特别说“这趟想密集地逛城市景点”，本次 State 应体现这个新要求，不能让旧 Memory 覆盖用户当前意愿。航班时间、票价和天气则需要查询相应服务，不能从 Memory 推断。

### 3. 购物助手：当前购买条件与长期偏好

用户说：“帮我挑一台轻一点、能剪视频的笔记本。”State 记录当前任务的要求：

```json
{
  "product_category": "笔记本电脑",
  "requirements": ["轻便", "适合视频剪辑"],
  "budget_cny": null,
  "operating_system": null,
  "stage": "need_budget",
  "missing_fields": ["budget_cny"]
}
```

助手发现预算缺失，可以追问预算。用户平时喜欢 macOS、偏好 14 英寸屏幕，这些可能是 Memory：

```json
{
  "preferred_operating_system": "macOS",
  "preferred_screen_size": "约 14 英寸"
}
```

当用户本次说“这次只看 Windows，预算一万元以内”，State 应记录本次明确条件：

```json
{
  "product_category": "笔记本电脑",
  "requirements": ["轻便", "适合视频剪辑"],
  "budget_cny": 10000,
  "operating_system": "Windows",
  "stage": "searching_products",
  "overrides_memory": ["本次选择 Windows，优先于平时偏好 macOS"]
}
```

Memory 帮忙提供个性化方向；当前 State 表达这次实际要买什么。至于实时价格和库存，仍要向商品服务确认。

### 4. 写作助手：当前文稿与写作习惯

用户说：“把这些材料整理成给新员工看的介绍，控制在 500 字以内。”当前写作任务的 State 可以是：

```json
{
  "task": "撰写内部介绍",
  "audience": "新员工",
  "max_words": 500,
  "tone_for_this_document": null,
  "draft_version": 1,
  "stage": "drafting"
}
```

用户常说“先讲结论、少用行话、语气自然”，这些偏好可以保存在 Memory：

```json
{
  "writing_preferences": ["先讲结论", "少用行话", "语气自然"]
}
```

如果用户接着说“这篇请正式一点”，当前文稿的 State 可以更新：

```json
{
  "task": "撰写内部介绍",
  "audience": "新员工",
  "max_words": 500,
  "tone_for_this_document": "正式",
  "draft_version": 2,
  "stage": "revising"
}
```

通用写作偏好仍可供参考，但不能压过“这篇请正式一点”这样的本次指令。

### 5. 项目助手：多步骤任务与流程进度

假设用户让助手准备一场新品发布会。State 可以跟踪各步骤以及等待中的确认：

```json
{
  "project": "新品发布会",
  "steps": {
    "collect_requirements": "completed",
    "draft_agenda": "completed",
    "confirm_venue": "in_progress",
    "send_invitation": "not_started"
  },
  "waiting_for_user_approval": true,
  "next_action": "请用户确认场地"
}
```

用户确认场地后，State 更新，Agent 才推进到发送邀请这一步。用户习惯的会议时长、常用模板或常用场地可能是 Memory：

```json
{
  "meeting_preferences": ["会议控制在 60 分钟内"],
  "preferred_invitation_template": "简洁版"
}
```

Memory 可以帮助助手更快准备草案；但发送邀请是一个真实动作，仍应该由程序按权限和确认规则执行，不能只因为 Memory 里有偏好就擅自发送。

## State、Memory、聊天记录和数据库不是一回事

- **State**：当前任务的工作信息，例如目的地、预算、订单号和流程阶段。
- **Memory**：未来可能仍有用的背景，例如稳定偏好或经过整理的历史摘要。
- **聊天记录**：之前说过的话的原始对话。记录很长，不代表每句话都适合变成 Memory。
- **业务数据库**：订单状态、库存、账户信息等事实的权威来源。AI 应通过应用或工具查询它，而不是把模型记忆当成实时数据。

State 常用结构化数据保存；Memory 可以是用户偏好字段，也可以是整理后的摘要或可检索内容。上面的 JSON 是示意结构，实际字段由应用的需求决定。两者都可能以 JSON 等形式表达，但“格式相似”不代表“作用相同”。Schema 是字段和规则的定义，State 则是当前填入这些字段的数据。

## 我的总结

> **State 帮 AI 助手把眼前这件事继续办下去；Memory 帮它在未来的相关任务里记住有用背景。**

Agent 应该根据当前任务更新 State，在需要时检索相关 Memory，再把合适的信息交给模型。与此同时，订单、库存等真实业务事实仍要向对应的数据源核实。这样既能保持任务连续，也不会把“记得用户喜欢什么”和“知道现实中发生了什么”混为一谈。
