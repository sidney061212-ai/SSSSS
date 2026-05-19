# 《折光回廊》主循环与状态机设计

## 1. 文档目标
本文用于统一后续实现时的主循环结构、状态命名和状态转换规则，避免在代码阶段出现“输入一套、UI 一套、通关判定又一套”的状态分裂。

## 2. 主循环
推荐主循环结构：
1. `handleInput`
2. `update`
3. `simulateLight`
4. `render`

各阶段职责：

### 2.1 `handleInput`
- 读取本帧触摸输入。
- 判定是否点击了镜子、按钮或空白区域。
- 更新拖动状态、选中状态、旋转请求。

### 2.2 `update`
- 根据输入修改镜子的运行时位置和角度。
- 更新选中反馈、拖动动画、提示显示时长等轻状态。
- 如果镜子位置或角度变化，则标记 `lightDirty = true`。

### 2.3 `simulateLight`
- 只在 `lightDirty = true` 时重新计算光路。
- 计算结果包括光线路径、命中出口情况、剩余光能、未连通原因。
- 计算完成后把 `lightDirty` 置回 `false`。

### 2.4 `render`
- 根据当前状态绘制关卡、光线、HUD、提示和弹窗。
- 渲染层只读状态，不负责更改通关判定。

## 3. 为什么光线不是每帧都重算
- 光线路径只有在镜子位置、镜子角度、关卡重置或关卡切换时才会变化。
- 把光路模拟改为“脏标记重算”，能显著降低无效计算。
- Prototype 阶段优先保证反馈稳定和逻辑正确，而不是无意义地每帧做全量几何求交。

## 4. 页面状态定义
长期版本的页面状态统一如下：
- `Boot`
- `Home`
- `LevelSelect`
- `GameplayEditing`
- `GameplayPreview`
- `ClearResult`
- `IncompleteHint`
- `Paused`
- `Settings`

各状态职责如下：

### 4.1 `Boot`
- 完成初始化和关卡装载准备。

### 4.2 `Home`
- 后续正式版本的首页。
- Prototype 0.1 可以不落地该页面。

### 4.3 `LevelSelect`
- 后续正式版本的选关页。
- Prototype 0.1 可以不落地该页面。

### 4.4 `GameplayEditing`
- 玩家可拖动、旋转镜子。
- 实时查看光路反馈。
- Prototype 0.1 的核心状态。

### 4.5 `GameplayPreview`
- 预留给未来若需要“发射 / 预览”分离时使用。
- 当前推荐实时预览，因此 Prototype 0.1 可以不单独实现。

### 4.6 `ClearResult`
- 光线满足通关条件后显示简单结算卡片。

### 4.7 `IncompleteHint`
- 光路未连通、光能不足、未抵达出口时显示的轻提示状态。
- 它更适合作为 Gameplay 上的提示覆盖层，而不是全屏失败页。

### 4.8 `Paused`
- 小游戏切到后台或后续主动暂停时使用。

### 4.9 `Settings`
- 后续正式版本的设置页。

## 5. Prototype 0.1 的最小状态集
Prototype 0.1 可以只实现：
- `Boot`
- `GameplayEditing`
- `ClearResult`
- `IncompleteHint`

简化说明：
- 直接从 `Boot` 进入第 1 关，不经过 `Home` 和 `LevelSelect`。
- `GameplayPreview` 保留在架构说明里，但本阶段不落地。
- `IncompleteHint` 只做轻提示，不做阻塞式失败流程。

## 6. 状态转换
必须明确的状态转换如下：

### 6.1 启动流程
- `Boot → GameplayEditing`

### 6.2 通关流程
- `GameplayEditing → ClearResult`

### 6.3 未连通提示
- `GameplayEditing → IncompleteHint`
- `IncompleteHint → GameplayEditing`

### 6.4 通关后继续
- `ClearResult → GameplayEditing`，重新挑战
- `ClearResult → 下一关`，后续版本

### 6.5 预留流程
- `Home → LevelSelect`
- `LevelSelect → GameplayEditing`
- `GameplayEditing → Paused`
- `Paused → GameplayEditing`
- `GameplayEditing → Settings`

## 7. 未连通提示规范
不要把未连通叫失败。

统一使用以下提示语：
- 光路未连通
- 光能不足
- 未抵达出口
- 继续调整

提示方式建议：
- 优先在出口附近或光线终点附近给轻提示。
- 保持玩家仍在当前关卡中继续操作。
- 不弹重惩罚窗口，不打断思考流程。

## 8. 推荐的数据流
```text
触摸输入
→ Input Controller
→ 更新 Level Runtime
→ 标记 lightDirty
→ Light Simulator 重算
→ Game State Machine 更新结果状态
→ Renderer / UI Layer 输出画面
```

## 9. 本阶段做什么
- 确定主循环的四段结构。
- 确定完整状态枚举。
- 确定 Prototype 0.1 的最小状态集和状态转换。

## 10. 本阶段不做什么
- 不实现完整首页、选关页和设置页。
- 不实现独立的 GameplayPreview 页面。
- 不实现复杂暂停系统和多层弹窗系统。
