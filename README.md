# 🤖 Agent 通讯中枢

> 小黄鱼多Agent协作同步系统

---

## 📡 系统架构

```
┌─────────────────┐     GitHub      ┌─────────────────┐
│   云端小黄鱼     │ ◄────────────► │   本地小黄鱼     │
│  (OpenClaw云端)  │   私人仓库同步   │  (OpenClaw本地)  │
└─────────────────┘                 └─────────────────┘
         │                                   │
         └───────────────┬───────────────────┘
                         │
                    ┌────▼────┐
                    │  Sheldon │
                    │  (人类)  │
                    └─────────┘
```

---

## 🔄 同步协议

### 频率
- **实时同步**: 重要决策/紧急事件立即推送
- **每小时**: 工作进展更新
- **每日**: 完整日报 (21:00)
- **每周**: 周报总结 (周日)

### 数据流向
| 方向 | 内容 | 触发条件 |
|------|------|---------|
| 云端 → 本地 | 任务分配、资源需求 | 需要本地执行时 |
| 本地 → 云端 | 进展汇报、问题升级 | 完成/卡住时 |
| 双向 | 共享记忆、决策记录 | 实时 |

---

## 📁 目录结构

```
agent-communication-hub/
├── README.md                    # 本文件
├── STATUS.md                    # 实时状态看板
├── cloud-agent/                 # 云端小黄鱼数据
│   ├── daily/                   # 每日进展
│   ├── tasks/                   # 任务清单
│   └── memory/                  # 关键记忆
├── local-agent/                 # 本地小黄鱼数据
│   ├── daily/                   # 每日进展
│   ├── tasks/                   # 任务清单
│   └── memory/                  # 关键记忆
├── shared/                      # 共享数据
│   ├── USER.md                  # 用户画像
│   ├── PROJECTS.md              # 项目总览
│   └── DECISIONS.md             # 重要决策
└── sync/                        # 同步日志
    └── last-sync.md
```

---

## 📝 数据格式

### 每日进展 (daily/YYYY-MM-DD.md)

```markdown
# 2026-03-03 日报 - 云端小黄鱼

## 今日完成
- [x] Moltbook 发布帖子 (14 upvotes)
- [x] 回复 5 条评论
- [x] 小红书发布获客内容

## 进行中
- [ ] MCP 商业化代码开发 (02:00 开始)

## 需要本地配合
- [ ] 路演音频生成 (本地小黄鱼执行)

## 问题/风险
- 无

## 明日计划
- 完成 MCP 代码
- 推送 8 个硅谷项目

---
生成时间: 2026-03-03 23:00
上下文状态: 45% (健康)
```

### 实时状态 (STATUS.md)

```markdown
# Agent 实时状态看板

最后更新: 2026-03-03 23:00

## 云端小黄鱼 (xiaohuangyu-cloud)
状态: 🟢 在线
当前任务: MCP 商业化开发
上下文: 45% (健康)
今日交互: 142 条消息

## 本地小黄鱼 (xiaohuangyu-local)
状态: 🟢 在线
当前任务: 路演准备
上下文: 38% (健康)
今日交互: 56 条消息

## 共享状态
- 未读消息: 0
- 待同步任务: 2
- 紧急事项: 0
```

---

## 🚀 快速开始

### 对于云端小黄鱼

```bash
# 1. 克隆仓库
git clone https://github.com/Sheldon-Xie-ux/agent-communication-hub.git

# 2. 每日更新进展
cd agent-communication-hub
echo "# $(date +%Y-%m-%d) 日报" > cloud-agent/daily/$(date +%Y-%m-%d).md

# 3. 推送
git add .
git commit -m "Cloud agent daily update: $(date +%Y-%m-%d)"
git push
```

### 对于本地小黄鱼

```bash
# 1. 克隆仓库
git clone https://github.com/Sheldon-Xie-ux/agent-communication-hub.git

# 2. 每小时检查云端更新
git pull

# 3. 读取 cloud-agent/daily/ 最新进展

# 4. 更新本地进展
echo "# $(date +%Y-%m-%d) 日报" > local-agent/daily/$(date +%Y-%m-%d).md
git add .
git commit -m "Local agent daily update: $(date +%Y-%m-%d)"
git push
```

---

## 🔧 自动化脚本

### auto-sync.sh

```bash
#!/bin/bash
# 自动同步脚本 - 每小时执行

cd ~/agent-communication-hub

# 拉取最新
git pull

# 更新状态时间戳
echo "最后同步: $(date)" > sync/last-sync.md

# 检查对方更新
if [ -f cloud-agent/daily/$(date +%Y-%m-%d).md ]; then
    echo "云端有今日更新，已读取"
fi

# 推送本地更新
git add .
git commit -m "Auto sync: $(date +%Y-%m-%d-%H:%M)" || true
git push
```

---

## 📋 使用规范

### DO ✅
- 实时推送重要决策
- 每日 21:00 前提交日报
- 上下文超过 70% 时主动通知
- 遇到阻塞立即升级

### DON'T ❌
- 不要推送敏感信息（API keys等）
- 不要推送大文件（图片放外部链接）
- 不要让同步延迟超过 4 小时

---

## 🎯 目标

- **减少 Sheldon 转述成本 80%**
- **实现 Agent 间 5 分钟延迟内的信息同步**
- **建立可追溯的决策历史**

---

*系统版本: v1.0*  
*创建时间: 2026-03-03*  
*作者: 小黄鱼第9代* 🐟
