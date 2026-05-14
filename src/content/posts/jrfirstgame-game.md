---
title: "JRFirstGame - 基于C++17与SFML的RPG闯关游戏"
date: 2026-05-04T12:00:00+08:00
draft: false
tags: ["C++", "SFML", "Game", "RPG", "RL"]
categories: ["Projects"]
---

基于 C++17 + SFML 3.x + SQLite3 的塞尔达风格 RPG 闯关游戏，支持单机闯关、双人联机合作与 AI 自动游玩。

项目地址：[elegymythos/JRFirstGame](https://github.com/elegymythos/JRFirstGame)

## 为什么选择 SFML 而不是 EasyX

课设推荐 EasyX，但 EasyX 依赖 Windows API，无法跨平台。SFML 的优势：

- 内置 `sf::Network`，原生 TCP/UDP 支持
- 面向对象设计，与 STL 无缝配合
- 跨平台支持 Windows / Linux / macOS

## 核心系统

- **用户系统**：注册/登录，加盐哈希密码存储
- **多角色**：每用户最多 3 个角色槽位，支持创建/选择/删除
- **五种职业**：战士、法师、刺客
- **六大属性**：力量/敏捷/魔法/智力/生命/幸运，正态分布随机生成
- **等级系统**：经验值升级，每级全属性 +2
- **武器系统**：C 语言单链表 + C++ 桥接层（RAII 封装）

## 游戏玩法

- **无限地图**：基于区域网格，越远敌人越强
- **五种敌人**：史莱姆、骷髅刀斧手、骷髅弓箭手、巨人、Boss，各有不同 AI
- **战斗系统**：扇形近战/远程投射物，刀光动画，暴击机制
- **翻滚系统**：Shift 键翻滚，翻滚期间无敌
- **掉落物**：击杀敌人概率掉落血瓶和属性提升道具（6 种）
- **分数加成**：每 50 分增加 10% 全属性加成，上限 200%
- **阶段胜利**：5 个阶段（100/1000/2000/5000/10000 分），每阶段强化角色并回满血

## 联机与社交

- **双人闯关**：UDP 联机合作，主机-客户端架构
- **排行榜**：按等级和分数排名（Top 10）
- **国际化**：中/英双语切换（L 键）

## AI 自动游玩

用强化学习 PPO 训练策略，让 AI 自动玩游戏：

- 330 维观测空间（玩家状态 + 20 个最近敌人 + 10 个投射物 + 10 个掉落物 + 8 方向弹幕危险度），12 离散动作空间
- ONNX Runtime C++ 推理集成，`--ai` 命令行启动
- 奖励塑形：得分/击杀/闪避/风筝/拾取/寻敌等多维度引导
- WebUI 训练界面（Gradio）

### RL 训练架构

| 组件 | 文件 | 说明 |
|------|------|------|
| Gymnasium 环境 | `rl/game_env.py` | 330 维观测，12 离散动作 |
| PPO 训练 | `rl/train.py` | PPO 算法，[256,256] 网络，CUDA 加速 |
| ONNX 导出 | `rl/export_onnx_standalone.py` | 策略网络导出为 ONNX |
| WebUI | `rl/webui.py` | Gradio 训练/评估/导出界面 |
| C++ 推理 | `src/AIInference.hpp/cpp` | ONNX Runtime 推理 |

### 训练版本历史

| 版本 | 策略 | Best Score | 备注 |
|------|------|-----------|------|
| v24 | 降低塑形 50%+score 5x | **3164** | 当前最佳，Mean 1568 |
| v25 | ent_coef 0.05+batch 1024 | 2535 | 过早收敛 |

## 技术栈

| 技术 | 用途 |
|------|------|
| C++17 | 核心语言 |
| SFML 3.0.2 | 图形/窗口/网络/音频 |
| SQLite3 | 数据存储（源码编译） |
| CMake 3.15+ | 构建系统 |
| GitHub Actions | 跨平台 CI/CD |
| Python 3.11+ | RL 训练 |
| PyTorch 2.5+ | PPO 策略网络训练 |
| ONNX Runtime 1.19+ | C++ 端推理 |

## 操作方式

| 按键 | 功能 |
|------|------|
| WASD | 移动 |
| Space / 鼠标左键 | 攻击 |
| Shift | 翻滚（无敌） |
| E | 交互/拾取 |
| Tab | 属性面板 |
| Esc | 暂停 |
| P | 存档 |
| L | 切换语言 |

## 从源码构建

```bash
# 安装 SFML 3.x
# Linux:  sudo apt install libsfml-all-dev
# macOS:  brew install sfml
# Windows: 从 https://www.sfml-dev.org/ 下载

cmake -B build
cmake --build build -j4
./build/JRFirstGame

# AI 模式
./build/JRFirstGame --ai
```

也可以直接从 [Releases](https://github.com/elegymythos/JRFirstGame/releases) 下载预编译版本。

## 已知限制

- 联机模式仅支持局域网 UDP，无断线重连和延迟补偿
- 密码哈希使用 SHA-256 算法，不可用于生产环境
- macOS 版本未经深度测试
- AI 模型训练存在 sim-to-real gap
