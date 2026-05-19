# 《折光回廊》未来工程目录规划

## 1. 文档目标
本文只规划后续代码阶段的目录组织方式，用于保证模块边界清晰。本文不创建任何目录，不生成任何代码文件。

## 2. 建议目录结构
```text
minigame/
├─ game.ts
├─ game.json
├─ project.config.json
├─ src/
│  ├─ main.ts
│  ├─ app/
│  │  ├─ bootstrap.ts
│  │  ├─ scene-manager.ts
│  │  └─ game-state.ts
│  ├─ core/
│  │  ├─ types.ts
│  │  ├─ constants.ts
│  │  ├─ geometry.ts
│  │  ├─ light-simulator.ts
│  │  ├─ collision.ts
│  │  └─ level-runtime.ts
│  ├─ levels/
│  │  ├─ level-001.ts
│  │  └─ index.ts
│  ├─ input/
│  │  └─ touch-controller.ts
│  ├─ render/
│  │  ├─ renderer.ts
│  │  ├─ draw-light.ts
│  │  ├─ draw-mirror.ts
│  │  ├─ draw-obstacle.ts
│  │  ├─ draw-ui.ts
│  │  └─ draw-effects.ts
│  ├─ ui/
│  │  ├─ home-scene.ts
│  │  ├─ gameplay-scene.ts
│  │  ├─ clear-result-panel.ts
│  │  └─ settings-scene.ts
│  ├─ storage/
│  │  └─ local-storage.ts
│  └─ assets/
│     └─ asset-manifest.ts
└─ assets/
   ├─ images/
   └─ audio/
```

## 3. 目录设计原则
### 3.1 `app/`
- 放启动和场景调度逻辑。
- 负责把模块串起来，不承担玩法细节。

### 3.2 `core/`
- 只放与具体页面、具体关卡脚本无关的核心逻辑。
- 适合放类型、常量、几何计算、碰撞检测、光路模拟、关卡运行时状态。
- 不放按钮绘制，不放页面切换，不放具体关卡文本。

### 3.3 `levels/`
- 只放关卡数据。
- 第 1 关到后续 12 关都应该以数据文件形式放在这里。
- 不在这里写渲染逻辑和输入逻辑。

### 3.4 `input/`
- 只负责把触摸输入转换成游戏操作。
- 例如命中镜子、开始拖动、结束拖动、点击旋转、点击重置。
- 不负责绘制，不直接写状态结算规则。

### 3.5 `render/`
- 只负责绘制。
- 不负责改游戏状态。
- 所有画布绘制函数都应从运行时状态读取数据，而不是自己修改镜子位置或通关结果。

### 3.6 `ui/`
- 只放场景和面板逻辑。
- 包括 Gameplay 场景、通关卡片、后续的首页和设置页。
- UI 层可以读游戏状态，但不替代核心模拟器。

### 3.7 `storage/`
- 只放本地读写能力。
- Prototype 0.1 可以只有接口或占位实现。

### 3.8 `assets/`
- `src/assets/asset-manifest.ts` 记录资源清单。
- 根目录 `assets/` 放真实位图和音频资源。
- 核心玩法元素优先代码绘制，因此根资源目录在 Prototype 0.1 会保持很轻。

## 4. Prototype 0.1 的实际落地范围
- Prototype 0.1 不一定创建所有目录。
- 但目录规划要能支撑后续 12 关扩展。
- 代码阶段的最小必需目录通常会是 `app/`、`core/`、`levels/`、`input/`、`render/`、`ui/`。
- `storage/`、`assets/`、`audio` 可以先轻量预留。

## 5. 建议的文件职责边界
- `main.ts`：创建应用入口，启动 Bootstrap。
- `bootstrap.ts`：初始化画布、状态机、主循环。
- `game-state.ts`：定义全局状态枚举和状态切换函数。
- `geometry.ts`：向量、角度、投影、反射等基础运算。
- `collision.ts`：射线与镜子、出口、边界等几何检测。
- `light-simulator.ts`：根据关卡运行时数据计算光路结果。
- `level-runtime.ts`：根据 LevelConfig 生成本局可变状态。
- `level-001.ts`：第 1 关“错位的光”的数据配置。
- `touch-controller.ts`：触摸输入解析与命中优先级。
- `renderer.ts`：统一渲染入口和绘制顺序。

## 6. 为什么不在第一阶段搭大型目录
- 当前目标是第 1 关垂直切片。
- 目录规划必须清楚，但不需要一次性生成全部工程骨架。
- 先写最小可运行结构，后续再按同一边界补齐文件。

## 7. 本阶段做什么
- 只给出后续代码阶段的目录建议。
- 明确模块边界和职责。

## 8. 本阶段不做什么
- 不创建 `minigame/`。
- 不创建 `src/`。
- 不创建任何代码文件。
- 不生成资源目录中的素材文件。
