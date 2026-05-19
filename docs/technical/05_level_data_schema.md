# 《折光回廊》关卡数据结构设计

## 1. 文档目标
本文用于定义《折光回廊》后续代码阶段的关卡数据结构，确保关卡是数据驱动的，而不是写死在渲染逻辑或场景脚本中。

## 2. 设计原则
- 使用 TypeScript 风格接口描述关卡结构。
- 字段命名统一、可扩展，但不过度设计。
- Prototype 0.1 只实现第 1 关“错位的光”。
- 后续机制如分光聚能点、颜色出口只做字段预留，不做实现。

## 3. 基础类型示例
```ts
interface Point {
  x: number;
  y: number;
}

interface Size {
  width: number;
  height: number;
}

interface Rect {
  x: number;
  y: number;
  width: number;
  height: number;
}
```

## 4. 关卡核心接口
```ts
interface LevelConfig {
  id: string;
  name: string;
  chapter: number;
  designGoal: string;
  bounds: Rect;
  placementBounds: Rect;
  sources: LightSourceConfig[];
  exits: ExitConfig[];
  mirrors: MirrorConfig[];
  fixedMirrors: MirrorConfig[];
  obstacles: ObstacleConfig[];
  condensers: CondenserConfig[];
  scoring: ScoringConfig;
  tutorialTips: string[];
  prototypeNotes: string[];
  energyDecayRate: number;
  maxBounces: number;
}

interface LightSourceConfig {
  id: string;
  position: Point;
  direction: number;
  color: "white" | "blue" | "violet";
  initialEnergy: number;
}

interface ExitConfig {
  id: string;
  position: Point;
  radius: number;
  acceptedColors: Array<"white" | "blue" | "violet">;
  requiredBeams: number;
}

interface MirrorConfig {
  id: string;
  position: Point;
  angle: number;
  length: number;
  draggable: boolean;
  rotatable: boolean;
  angleStep: number;
  mirrorType: "standard" | "fixed";
}

interface ObstacleConfig {
  id: string;
  shape: "rect" | "circle" | "polygon";
  position: Point;
  size: Size;
  obstacleType: "solid" | "absorber";
}

interface CondenserConfig {
  id: string;
  position: Point;
  radius: number;
  restoreEnergy: number;
  condenserType: "standard" | "split";
}

interface ScoringConfig {
  pass: {
    requiredExitIds: string[];
  };
  twoStarMaxMirrors: number;
  threeStarMaxMirrors: number;
  threeStarMinEnergyPercent: number;
}
```

## 5. 字段说明
### 5.1 `LevelConfig`
- `id`：唯一关卡编号。
- `name`：关卡标题。
- `chapter`：章节编号。
- `designGoal`：本关要教会玩家什么。
- `bounds`：关卡逻辑边界。
- `placementBounds`：镜子允许活动的边界。
- `sources`：光源列表。
- `exits`：出口列表。
- `mirrors`：玩家可操作镜子列表。
- `fixedMirrors`：固定镜列表。
- `obstacles`：障碍和吸光块列表。
- `condensers`：聚光点和分光聚能点列表。
- `scoring`：通关和星级目标。
- `tutorialTips`：首关或教学关的短提示。
- `prototypeNotes`：仅供开发阶段参考的备注。
- `energyDecayRate`：单位距离的光能衰减率。
- `maxBounces`：最大反射次数。

### 5.2 `LightSourceConfig`
- `direction` 使用角度制，单位为度。
- `color` 在 Prototype 0.1 固定使用 `white`，后续保留颜色扩展。
- `initialEnergy` 建议统一使用 `0 ~ 1` 区间。

### 5.3 `ExitConfig`
- `acceptedColors` 预留颜色出口能力。
- `requiredBeams` 预留汇光出口能力。
- Prototype 0.1 只需要单出口、单束光，因此 `requiredBeams = 1`。

### 5.4 `MirrorConfig`
- `mirrorType` 在 Prototype 0.1 只使用 `standard`。
- 固定镜可在后续阶段通过 `draggable = false`、`rotatable = false` 和 `mirrorType = "fixed"` 实现。

### 5.5 `ObstacleConfig`
- `solid` 表示阻挡光线。
- `absorber` 表示吸光块。
- Prototype 0.1 先不放障碍物，字段只保留。

### 5.6 `CondenserConfig`
- `standard` 对应聚光点。
- `split` 对应分光聚能点。
- Prototype 0.1 不实现，但结构从一开始保留。

### 5.7 `ScoringConfig`
- `pass` 表示满足哪些出口即算通关。
- `twoStarMaxMirrors`、`threeStarMaxMirrors` 控制用镜目标。
- `threeStarMinEnergyPercent` 控制剩余光能门槛。

## 6. 第 1 关数据草案
第 1 关：错位的光
- 左下光源
- 右上出口
- 1 面普通镜子
- 无障碍
- 无聚光点
- 目标：用一面镜子反射到出口
- 三星：1 面镜子，剩余光能高于 70%

数据草案示例：
```ts
const level001: LevelConfig = {
  id: "level-001",
  name: "错位的光",
  chapter: 1,
  designGoal: "教玩家用一面普通镜子完成一次反射",
  bounds: { x: 24, y: 120, width: 327, height: 520 },
  placementBounds: { x: 48, y: 180, width: 279, height: 420 },
  sources: [
    {
      id: "source-001",
      position: { x: 52, y: 560 },
      direction: 0,
      color: "white",
      initialEnergy: 1
    }
  ],
  exits: [
    {
      id: "exit-001",
      position: { x: 320, y: 240 },
      radius: 18,
      acceptedColors: ["white"],
      requiredBeams: 1
    }
  ],
  mirrors: [
    {
      id: "mirror-001",
      position: { x: 180, y: 500 },
      angle: 135,
      length: 72,
      draggable: true,
      rotatable: true,
      angleStep: 45,
      mirrorType: "standard"
    }
  ],
  fixedMirrors: [],
  obstacles: [],
  condensers: [],
  scoring: {
    pass: {
      requiredExitIds: ["exit-001"]
    },
    twoStarMaxMirrors: 1,
    threeStarMaxMirrors: 1,
    threeStarMinEnergyPercent: 70
  },
  tutorialTips: [
    "拖动镜子到光路中",
    "点击镜子可按 45° 旋转",
    "让光进入右上出口"
  ],
  prototypeNotes: [
    "首关默认镜子角度接近正确解",
    "实时重算光路",
    "未连通时只给轻提示"
  ],
  energyDecayRate: 0.0012,
  maxBounces: 8
};
```

## 7. Prototype 0.1 预留但不实现的字段能力
- `LightSourceConfig.color` 的多颜色玩法。
- `ExitConfig.acceptedColors` 的颜色出口判定。
- `ExitConfig.requiredBeams` 的汇光出口判定。
- `MirrorConfig.mirrorType = "fixed"` 的固定镜玩法。
- `ObstacleConfig.obstacleType = "absorber"` 的吸光块。
- `CondenserConfig.condenserType = "split"` 的分光聚能点。

## 8. 为什么一定要从第 1 关开始数据驱动
- 第 1 关虽然简单，但它已经能验证数据结构是否合理。
- 后续扩到 3 关、6 关、12 关时，只需要补数据，不需要重写渲染器。
- 能避免“先写死首关，后面再返工拆配置”的重复劳动。

## 9. 本阶段做什么
- 定义关卡、光源、出口、镜子、障碍、聚光点、评分的统一数据结构。
- 给出第 1 关“错位的光”的数据草案。
- 明确哪些字段是预留，哪些字段是 Prototype 0.1 真正会用到的。

## 10. 本阶段不做什么
- 不创建 `level-001.ts`。
- 不实现 JSON 解析器或关卡编辑器。
- 不实现分光聚能点。
- 不实现颜色出口。
