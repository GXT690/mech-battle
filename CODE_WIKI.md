# MECH BATTLE - 代码知识库

> 像素风机甲双人对战游戏 · v2.0

## 目录

- [项目概述](#项目概述)
- [项目架构](#项目架构)
- [核心模块详解](#核心模块详解)
- [关键类与函数说明](#关键类与函数说明)
- [依赖关系](#依赖关系)
- [游戏运行方式](#游戏运行方式)
- [常量与配置](#常量与配置)

---

## 项目概述

| 属性 | 值 |
|------|-----|
| 项目名称 | MECH BATTLE - 像素机甲对战 |
| 版本 | v2.0 |
| 技术栈 | HTML5 + Canvas + JavaScript |
| 依赖 | 无外部依赖，单文件运行 |
| 游戏类型 | 双人实时对战 |
| 文件大小 | 约 2400 行 |

### 主要特性

- **像素风格视觉** - 纯 Canvas 程序化绘制机甲角色，无外部图片依赖
- **双人对战** - 同一键盘，两名玩家实时对战
- **多场景切换** - 城市 / 沙漠 / 雪山 三种主题战场
- **8-bit 背景音乐** - Web Audio API 程序化合成的 Chiptune 风格 BGM
- **技能系统** - 近战攻击、远程射击、冲刺闪避、必杀技
- **能量管理系统** - 命中敌人积攒能量，释放强力技能
- **自定义按键** - 每个玩家的每个操作均可自由绑定键盘键
- **本地存储持久化** - 按键配置自动保存

---

## 项目架构

```
mech-battle.html (单文件，约2400行)
│
├── HTML 结构 (L6-405)
│   ├── 游戏容器 canvas#gameCanvas
│   ├── HUD 信息栏
│   ├── 场景选择面板
│   ├── 设置面板
│   └── 覆盖层（开始/结束界面）
│
├── CSS 样式 (L7-304)
│   ├── 全局样式
│   ├── 游戏容器样式
│   ├── HUD 样式
│   ├── 覆盖层样式
│   └── 像素风格 UI 设计
│
└── JavaScript (L407-2388)
    │
    ├── 常量定义 (L412-420)
    │
    ├── 输入系统 (L422-488)
    │   ├── keys / keyPressed 对象
    │   ├── normalizeKey() - 按键规范化
    │   ├── displayKeyName() - 按键显示名映射
    │   ├── consumeKey() - 按键消费
    │   └── 事件监听器
    │
    ├── 音频系统 (L490-982)
    │   ├── SFX 类 - 音效合成
    │   └── BGM 类 - 背景音乐
    │
    ├── 粒子系统 (L984-1021)
    │   ├── spawnParticle() - 粒子生成
    │   ├── updateParticles() - 粒子更新
    │   └── drawParticles() - 粒子渲染
    │
    ├── 游戏实体 (L1023-1617)
    │   ├── Bullet 类 - 子弹
    │   ├── Shockwave 类 - 必杀冲击波
    │   └── Mech 类 - 机甲玩家
    │
    ├── 场景系统 (L1619-1988)
    │   └── Scene 类 - 场景渲染
    │
    ├── 游戏状态管理 (L1990-2080)
    │   ├── initGame() - 游戏初始化
    │   ├── checkWinner() - 胜负判定
    │   └── updateHUD() - HUD 更新
    │
    ├── 主循环 (L2082-2163)
    │   └── gameLoop() - 游戏主循环
    │
    └── UI 绑定 (L2165-2388)
        ├── 场景选择
        ├── 设置面板
        └── 按钮事件绑定
```

---

## 核心模块详解

### 1. 输入系统 (L422-488)

管理所有键盘输入状态。

**全局变量：**
```javascript
const keys = {}        // 当前帧按键状态
const keyPressed = {} // 按键按下状态（单帧有效）
```

**关键函数：**

| 函数 | 说明 | 参数 | 返回值 |
|------|------|------|--------|
| `normalizeKey(e)` | 规范化按键名称 | `e`: KeyboardEvent | 统一的小写按键名 |
| `displayKeyName(k)` | 获取显示用按键名 | `k`: 按键名 | 可读按键名（如"↑"） |
| `consumeKey(key)` | 消费按键（单帧响应） | `key`: 按键名 | Boolean |

**默认按键绑定 (L449-453)：**
```javascript
const DEFAULT_BINDINGS = {
  1: { left: 'a', right: 'd', jump: 'w', attack: 'j', defend: 'k', shoot: 'l', dash: 'shift', ultimate: 'u' },
  2: { left: 'arrowleft', right: 'arrowright', jump: 'arrowup', attack: '1', defend: '2', shoot: '.', dash: '/', ultimate: ',' }
};
```

---

### 2. SFX 音效系统 (L490-614)

使用 Web Audio API 合成 8-bit 风格音效。

**类：`SFX`**

| 方法 | 说明 |
|------|------|
| `ensure()` | 初始化/唤醒 AudioContext |
| `tone(freqStart, freqEnd, duration, type, volume)` | 合成单音调 |
| `noise(duration, volume, freqFilter)` | 合成噪声 |
| `attack()` | 近战攻击音效 |
| `hit()` | 受击音效 |
| `jump()` | 跳跃音效 |
| `defend()` | 防御音效 |
| `dash()` | 冲刺音效 |
| `shoot()` | 射击音效 |
| `ultimate()` | 必杀技音效 |
| `victory()` | 胜利音效 |
| `bulletHit()` | 子弹命中音效 |

**音效合成原理：**
- `tone()` - 创建 Oscillator（振荡器），frequency 从起始频率滑落到结束频率
- `noise()` - 创建 BufferSource 填充随机白噪声，通过低通滤波器

---

### 3. BGM 背景音乐系统 (L616-982)

多轨循环 Chiptune 音乐合成器。

**类：`BGM`**

| 属性 | 类型 | 说明 |
|------|------|------|
| `ctx` | AudioContext | 音频上下文 |
| `melodyGain` | GainNode | 主旋律音量 |
| `bassGain` | GainNode | 低音音量 |
| `drumGain` | GainNode | 鼓点音量 |
| `chordGain` | GainNode | 和弦垫音量 |
| `currentTheme` | string | 当前主题 |
| `themes` | object | 三种场景音乐主题 |

**三种场景 BGM 风格：**

| 场景 | BPM | 风格 | 调式 |
|------|-----|------|------|
| CITY | 128 | 赛博朋克 | C 小调 |
| DESERT | 110 | 阿拉伯异域 | 和声小调 |
| SNOW | 95 | 空灵钢琴 | C 大调 |

**音乐调度流程 (L874-931)：**
1. `scheduler()` - 基于 lookahead 调度音符
2. `scheduleCurrentNotes(time)` - 在指定时间调度当前音符
3. `advanceNote()` - 推进到下一个音符

**轨道类型：**
- `melody` - 主旋律（square/sawtooth 波）
- `bass` - 低音（triangle/square 波）
- `chord` - 和弦垫（sine 波）
- `drum` - 鼓点（K=底鼓，S=军鼓）

---

### 4. 粒子系统 (L984-1021)

全局粒子数组 `particles[]`，支持受击火花、冲刺轨迹、必杀爆发等特效。

**全局变量：**
```javascript
const particles = []
```

**核心函数：**

| 函数 | 说明 |
|------|------|
| `spawnParticle(x, y, color, count, vxRange, vyRange, lifeRange)` | 生成粒子群 |
| `updateParticles()` | 更新粒子位置/生命周期 |
| `drawParticles(ctx)` | 绘制所有粒子 |

**粒子属性：**
```javascript
{
  x, y,           // 位置
  vx, vy,         // 速度
  life, maxLife,  // 生命周期
  color,          // 颜色
  size,           // 尺寸
  gravity         // 重力
}
```

---

### 5. 子弹类 Bullet (L1023-1075)

远程攻击弹道。

**构造函数参数：**
```javascript
constructor(x, y, vx, vy, owner, color)
// x, y: 初始位置
// vx, vy: 速度向量
// owner: 所属玩家 ID (1 或 2)
// color: 子弹颜色
```

**关键方法：**

| 方法 | 说明 |
|------|------|
| `update(opponent, shooter)` | 更新子弹位置，检测碰撞 |
| `rectHit(a, b)` | 矩形碰撞检测 |
| `draw(ctx)` | 绘制子弹及尾迹 |

**子弹属性：**
```javascript
this.w = 14; this.h = 8;    // 尺寸
this.life = 90;              // 生命周期
this.trail = [];             // 尾迹数组
```

---

### 6. 冲击波类 Shockwave (L1077-1157)

必杀技特效，从角色中心向外扩张的圆形冲击波。

**构造函数参数：**
```javascript
constructor(x, y, owner, color)
// x, y: 冲击波中心
// owner: 所属玩家 ID
// color: 颜色
```

**关键属性：**
```javascript
this.radius = 10;        // 当前半径
this.maxRadius = 320;   // 最大半径
this.life = 45;         // 生命周期（帧）
this.hitIds = new Set(); // 已命中目标 ID
```

**伤害机制：**
- 在半径增长过程中判定命中（检测目标与中心的距离是否在增长范围内）
- 每个目标只命中一次

---

### 7. 机甲类 Mech (L1159-1617)

玩家逻辑核心类。

**构造函数选项 (opts)：**
```javascript
{
  x, y,           // 初始位置
  facing,         // 朝向 (1=右, -1=左)
  color,          // 主色
  colorDark,      // 深色
  colorAccent,    // 强调色
  id              // 玩家 ID (1 或 2)
}
```

**属性分类：**

| 类别 | 属性 | 说明 |
|------|------|------|
| 物理 | `x, y, vx, vy, width(70), height(110)` | 位置/速度/尺寸 |
| 移动 | `speed(4), jumpPower(14), onGround` | 移动参数 |
| 战斗 | `hp(100), maxHp, mp(0), maxMp(100)` | 生命/能量 |
| 状态 | `state(idle/walk/jump/fall/attack/defend/dash/ultimate)` | 当前状态 |
| 冷却 | `attackCooldown, shootCooldown, dashCooldown, ultimateCooldown` | 技能冷却 |
| 特殊 | `isDashing, isDefending, isHurt, isAttacking, isUltimate` | 状态标志 |

**关键方法：**

| 方法 | 说明 |
|------|------|
| `update(opponent)` | 每帧更新逻辑 |
| `startAttack()` | 发动近战攻击 |
| `startShoot()` | 发射子弹（消耗30MP） |
| `startDash()` | 冲刺（消耗25MP） |
| `startUltimate(opponent)` | 释放必杀技（消耗100MP） |
| `calculateDamage(opponent)` | 计算伤害（考虑防御） |
| `takeDamage(dmg, attackerFacing)` | 受到伤害 |
| `getBodyBox()` | 获取身体碰撞箱 |
| `getAttackHitbox()` | 获取攻击判定箱 |
| `rectHit(a, b)` | 矩形碰撞检测 |
| `draw(ctx)` | 绘制机甲像素画 |

**伤害公式：**

| 攻击类型 | 基础伤害 | 正面防御 | 背面防御 |
|---------|---------|---------|---------|
| 近战 | 15~25 | 30% | 70% |
| 射击 | 10~15 | 30% | 70% |
| 必杀 | 30~40 | 50% | 50% |

---

### 8. 场景类 Scene (L1619-1988)

三种游戏场景的程序化渲染。

**类：`Scene`**

**构造函数参数：**
```javascript
constructor(type)  // 'city' | 'desert' | 'snow'
```

**场景特性：**

| 场景 | 背景特效 | 粒子 | BGM |
|------|---------|------|-----|
| CITY | 夜空/星星/月亮/建筑/窗户 | 无 | 赛博朋克 128BPM |
| DESERT | 夕阳/太阳/沙丘/云 | 沙尘 | 阿拉伯 110BPM |
| SNOW | 蓝天/极光/雪山 | 飘雪 | 钢琴 95BPM |

**关键方法：**

| 方法 | 说明 |
|------|------|
| `update()` | 更新场景动画（云/粒子） |
| `draw(ctx)` | 绘制场景 |
| `drawCity(ctx)` | 绘制城市背景 |
| `drawDesert(ctx)` | 绘制沙漠背景 |
| `drawSnow(ctx)` | 绘制雪山背景 |

---

## 关键类与函数说明

### 常量定义 (L412-420)

```javascript
const GAME_WIDTH = 960;     // 游戏宽度
const GAME_HEIGHT = 540;    // 游戏高度
const GROUND_Y = 440;       // 地面 Y 坐标
const GRAVITY = 0.7;        // 重力加速度
const FRICTION = 0.85;      // 地面摩擦力
```

### 游戏状态管理

**全局状态变量：**
```javascript
let scene;              // 当前场景对象
let p1, p2;            // 两名玩家
let gameState;          // 'start' | 'playing' | 'end'
let shakeTimer;         // 屏幕震动计时器
let shakeX, shakeY;     // 震动偏移量
let currentSceneType;    // 当前场景类型
```

**游戏状态转换：**
```
start → playing: 点击"开始战斗"按钮
playing → end: 任意玩家 HP 归零
end → playing: 点击"再战一局"按钮
```

### 主循环 gameLoop() (L2085-2163)

使用 `requestAnimationFrame` 实现 60fps 游戏循环。

**每帧执行：**
1. 检查设置面板是否打开
2. 更新场景动画
3. 更新两名玩家
4. 更新所有子弹
5. 更新所有冲击波
6. 更新粒子系统
7. 屏幕震动计算
8. 胜负判定
9. 渲染场景
10. 渲染玩家（按 x 坐标排序）
11. 渲染子弹/冲击波/粒子
12. 更新 HUD

### 初始化流程

**`initGame()` (L1998-2020)：**
1. 创建 P1 机甲（蓝色）
2. 创建 P2 机甲（红色）
3. 清空子弹/冲击波/粒子数组
4. 重置屏幕震动
5. 创建新场景

---

## 依赖关系

```
mech-battle.html (无外部依赖)
│
├── Web APIs (浏览器内置)
│   ├── Canvas 2D Context
│   ├── Web Audio API
│   │   ├── AudioContext
│   │   ├── OscillatorNode
│   │   ├── GainNode
│   │   ├── BiquadFilterNode
│   │   └── AudioBufferSourceNode
│   ├── requestAnimationFrame
│   └── localStorage
│
└── 无第三方库/框架
```

**音频上下文初始化时机：**
- 用户首次交互（click/keydown/touchstart）时自动唤醒
- 解决浏览器自动暂停 AudioContext 的限制

---

## 游戏运行方式

### 方式一：本地服务器（推荐）

```bash
# Python 3
cd <项目目录>
python -m http.server 8000
# 浏览器打开: http://localhost:8000/mech-battle.html

# Node.js
npx serve .
```

### 方式二：直接打开

双击 `mech-battle.html` 用浏览器直接打开。

> **注意**：部分浏览器需要通过服务器访问才能正常播放音频。

---

## 常量与配置

### 物理常量 (L412-420)

| 常量 | 值 | 说明 |
|------|---|------|
| `GAME_WIDTH` | 960 | 游戏宽度 |
| `GAME_HEIGHT` | 540 | 游戏高度 |
| `GROUND_Y` | 440 | 地面 Y 坐标 |
| `GRAVITY` | 0.7 | 重力加速度 |
| `FRICTION` | 0.85 | 摩擦系数 |

### 能量系统

| 操作 | 消耗 | 回复 |
|------|------|------|
| 近战命中 | - | +20 MP |
| 射击命中 | -30 MP | +10 MP |
| 冲刺 | -25 MP | - |
| 必杀 | -100 MP | - |
| 站立不动 | - | +0.01% MP/帧 |

### 冲刺系统

| 参数 | 值 |
|------|---|
| 冲刺速度 | 14 px/帧 |
| 冲刺持续 | 18 帧 |
| 冲刺冷却 | 55 帧 |
| 无敌时间 | 22 帧 |

### 攻击系统

| 参数 | 值 |
|------|---|
| 近战攻击力 | 15~25 |
| 近战攻击判定帧 | 第 12 帧 |
| 近战攻击总帧数 | 25 帧 |
| 近战攻击冷却 | 35 帧 |
| 射击冷却 | 25 帧 |
| 必杀技持续 | 60 帧 |
| 必杀技冷却 | 90 帧 |

---

## 文件结构

```
/workspace/
├── README.md              # 项目说明文档
├── LICENSE                # MIT 许可证
├── mech-battle.html       # 游戏主文件（单文件，包含所有代码）
└── MECH-BATTLE-项目文件.zip  # 项目压缩包
```

---

*文档生成时间: 2026-06-20*
*游戏版本: v2.0*
