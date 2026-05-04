---
title: "JRFirstGame - 基于C++17与SFML的RPG联机闯关游戏"
date: 2026-05-04T12:00:00+08:00
draft: false
tags: ["C++", "SFML", "Game", "RPG"]
categories: ["Projects"]
---

这是我高级语言程序设计课程设计的作品——一款基于 C++17 + SFML 3.x + SQLite3 的俯视角 RPG 闯关游戏，灵感来源于《塞尔达传说》系列，支持单机闯关与双人 UDP 联机合作。

项目地址：[elegymythos/JRFirstGame](https://github.com/elegymythos/JRFirstGame)

## 为什么选择 SFML 而不是 EasyX

最初课设推荐使用 EasyX，但由于我日常在 Linux 环境下开发，而 EasyX 严重依赖 Windows API，跨平台支持几乎为零。SFML 则具备以下优势：

- 内置 `sf::Network`，原生支持 TCP/UDP，可直接实现联机逻辑
- 面向对象设计，与 STL 无缝配合，易于构建复杂游戏架构
- 跨平台支持 Windows / Linux / macOS

## 核心功能

### 用户与角色系统

- 注册/登录，密码使用 SHA-256 加盐哈希安全存储
- 每个用户最多 3 个角色槽位，支持创建/选择/删除
- 三种职业：**战士**（力量加成/铁剑）、**法师**（魔法加成/火焰法器）、**刺客**（敏捷加成/暗影刺刀）
- 六大属性（力量/敏捷/魔法/智力/生命/幸运）按正态分布随机生成，总和上限 80

### 单机闯关

- 800×800 区域网格构成的无限地图，越远敌人越强
- 4 种敌人 AI：史莱姆、骷髅刀斧手、骷髅弓箭手、巨人
- 扇形近战攻击 + 远程投射物，暴击机制，刀光动画
- Shift 翻滚无敌、击杀掉落血瓶与属性道具、分数加成（每 50 分 +10%，上限 200%）

### 联机合作

- UDP 网络通信，主机-客户端架构
- 玩家状态同步、暂停同步、数据包验证与容错
- 断开连接时正确发送 Disconnect 消息

### 其他

- **音频系统**：BGM 自动切换 + 32 通道轮询播放 15 种音效，AudioManager 单例管理，Python 脚本程序化生成全部 WAV 文件
- **武器系统**：C 语言单链表 + C++ 桥接层（RAII 封装）
- **国际化**：中/英双语实时切换（L 键）
- **数据持久化**：SQLite3 存储用户、角色、排行榜、存档
- **排行榜**：按等级和分数排名

## 技术栈

| 技术 | 用途 |
|------|------|
| C++17 | 核心语言 |
| SFML 3.x | 图形/窗口/网络/音频 |
| SQLite3 | 数据存储（源码编译） |
| CMake 3.15+ | 构建系统 |
| GitHub Actions | 跨平台 CI/CD |

## 操作方式

| 按键 | 功能 |
|------|------|
| WASD | 移动 |
| Space / 鼠标左键 | 攻击 |
| Shift | 翻滚（无敌） |
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
```

也可以直接从 [Releases 页面](https://github.com/elegymythos/JRFirstGame/releases) 下载预编译版本。

## 已知限制

- 联机模式仅支持局域网 UDP，无断线重连和延迟补偿
- 密码哈希使用 SHA-256 算法，不可用于生产环境
- macOS 版本未经深度测试
