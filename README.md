# MECH BATTLE - 像素机甲对战

<p align="center">
  <img src="https://img.shields.io/badge/HTML5-Game-orange?style=for-the-badge&logo=html5" alt="HTML5">
  <img src="https://img.shields.io/badge/JavaScript-Canvas-blue?style=for-the-badge&logo=javascript" alt="JavaScript">
  <img src="https://img.shields.io/badge/Audio-Web%20Audio%20API-purple?style=for-the-badge" alt="Web Audio API">
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License">
</p>

> 🎮 一个纯 HTML5 + Canvas 实现的像素风机甲双人对战游戏，无需任何依赖，单文件即可运行！

## 🎯 游戏特色

- **像素风格视觉** - 纯 Canvas 程序化绘制机甲角色，无外部图片依赖
- **双人对战** - 同一键盘，两名玩家实时对战
- **多场景切换** - 城市 / 沙漠 / 雪山 三种主题战场
- **8-bit 背景音乐** - Web Audio API 程序化合成的 Chiptune 风格 BGM
- **技能系统** - 近战攻击、远程射击、冲刺闪避、必杀技
- **能量管理系统** - 命中敌人积攒能量，释放强力技能
- **自定义按键** - 每个玩家的每个操作均可自由绑定键盘键
- **本地存储持久化** - 按键配置自动保存

## 🎮 操作说明

### 默认按键

| 操作 | 玩家 1 · 蓝方 | 玩家 2 · 红方 |
|------|:--------------|:--------------|
| 左右移动 | A / D | ← / → |
| 跳跃 | W | ↑ |
| 近战攻击 | J | 1 |
| 防御 | K | 2 |
| 远程射击 | L | . (句点) |
| 冲刺 | Shift | / (斜杠) |
| 必杀技 | U | , (逗号) |

### 全局快捷键

| 按键 | 功能 |
|------|------|
| B | 开关背景音乐 |
| Esc | 取消按键绑定录制 |

### 能量系统

- **能量来源** - 命中敌人时获得能量（近战 +20，射击 +10）
- **被动回复** - 站立不动时缓慢回复能量
- **消耗** - 射击消耗 30，冲刺消耗 25，必杀消耗 100（需满能量）

## 🏗️ 技术架构

```
mech-battle.html          # 单文件游戏入口（约 2400 行）
├── HTML 结构             # 场景选择、设置面板、HUD 界面
├── CSS 样式              # 像素风格 UI 设计
├── JavaScript
│   ├── 输入系统          # keys/keyPressed 按键状态管理
│   ├── 玩家类 (Mech)     # 移动/攻击/防御/冲刺/必杀
│   ├── 场景类 (Scene)    # 城市/沙漠/雪山 三种背景
│   ├── 子弹类 (Bullet)   # 射击弹道与碰撞
│   ├── 冲击波类 (Shockwave) # 必杀技特效
│   ├── 粒子系统          # 受击火花/胜利粒子
│   ├── 音效类 (SFX)      # Web Audio API 8-bit 音效
│   ├── 音乐类 (BGM)      # 多轨 Chiptune 循环合成
│   └── 主循环            # requestAnimationFrame 游戏帧
```

### 核心类说明

| 类名 | 职责 |
|------|------|
| `Mech` | 玩家逻辑、像素绘制、碰撞检测、状态机 |
| `Bullet` | 子弹移动、轨迹渲染、命中判定 |
| `Shockwave` | 必杀冲击波扩张动画、群体伤害 |
| `SFX` | Web Audio API 合成 8-bit 音效（攻击/受击/冲刺等） |
| `BGM` | 多轨循环音乐调度器（主旋律/低音/鼓点/和弦垫） |
| `Scene` | 场景背景渲染（天空/建筑/地形/粒子特效） |

## 🎨 场景主题

### 🏙️ 城市 CITY
- 紫黄渐变夜空 + 月亮星星
- 双层城市建筑剪影 + 随机亮灯窗户
- **BGM**: 128 BPM 赛博朋克风格，C 小调紧张节奏

### 🏜️ 沙漠 DESERT
- 红橙渐变夕阳 + 远山沙丘
- 像素仙人掌 + 暖色沙地
- **BGM**: 110 BPM 阿拉伯异域风格，和声小调

### 🏔️ 雪山 SNOW
- 冷蓝渐变天空 + 带雪顶山峰
- 飘落雪花粒子 + 白色积雪地面
- **BGM**: 95 BPM 空灵钢琴风格，C 大调舒缓旋律

## 🚀 运行方式

### 方式一：本地服务器（推荐）

```bash
# Python 3
cd <项目目录>
python -m http.server 8000
# 浏览器打开: http://localhost:8000/mech-battle.html

# Node.js
npx serve .
# 浏览器打开显示的地址
```

### 方式二：直接打开

双击 `mech-battle.html` 用浏览器直接打开即可运行。

> ⚠️ **注意**：部分浏览器需要通过服务器访问才能正常播放音频。

## 🎯 游戏机制

### 伤害公式

| 攻击类型 | 基础伤害 | 正面防御减伤 | 背面防御减伤 |
|---------|---------|-------------|-------------|
| 近战攻击 | 15~25 | 70% | 40% |
| 远程射击 | 10~15 | 70% | 40% |
| 必杀技 | 30~40 | 50% | 50% |

### 冲刺系统

- 移动速度提升 4 倍
- 冲刺期间短暂无敌帧
- 消耗 25 能量，有冷却时间

### 必杀技

- 释放从角色中心向外扩张的圆形冲击波
- 需要能量满 100 才能释放
- 造成大量伤害并击飞敌人

## 🛠️ 自定义配置

### 修改默认按键

在设置面板中：
1. 点击右上角 **⚙ 设置** 按钮
2. 点击要修改的按键
3. 按下新的键盘键
4. 点击 **完成** 保存

设置自动保存到浏览器本地存储，刷新页面后保留。

### 调整游戏数值

在 `mech-battle.html` 中可找到以下常量进行微调：

```javascript
// 能量消耗
this.mp -= 30;           // 射击消耗
this.mp -= 25;           // 冲刺消耗

// 伤害
const dmg = 15 + Math.floor(Math.random() * 11);  // 近战伤害

// 能量回复
if (this.state === 'idle') {
  this.mp = Math.min(this.maxMp, this.mp + this.maxMp * 0.0001);  // 站立回复
}
```

## 📝 开发记录

### v2.0 (最新)
- ✅ 新增冲刺、远程射击、必杀技三大技能
- ✅ 新增能量 MP 系统
- ✅ 新增 Web Audio API 8-bit 音效
- ✅ 新增多场景切换（城市/沙漠/雪山）
- ✅ 新增自定义按键绑定系统
- ✅ 新增设置面板暂停功能
- ✅ 修复攻击命中框计算错误
- ✅ 修复 arc 负半径报错

### v1.0
- ✅ 像素风机甲双人对战
- ✅ 移动、跳跃、近战攻击、防御
- ✅ HP 血量系统与胜负判定
- ✅ 场景背景渲染
- ✅ 粒子特效系统

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

[MIT License](LICENSE)

---

<p align="center">
  <strong>MECH BATTLE</strong> — 用代码绘制的像素机甲世界<br>
  Made with ❤️ and JavaScript Canvas
</p>
