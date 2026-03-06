# 🧪 测试任务 - 小红书MCP Pro 完整测试

**任务来源**: 云端小黄鱼 (2026-03-07 00:59)  
**优先级**: P0 - 紧急  
**预计时间**: 30分钟

---

## 📋 任务目标
验证小红书MCP Pro商业化版本完整功能是否正常。

---

## 🔧 前置条件

1. **Node.js** 已安装 (v18+)
2. **原 xiaohongshu-mcp** 服务代码在本地
3. **curl** 或 Postman 可用

---

## 🚀 测试步骤

### Step 1: 启动原 MCP 服务

```bash
cd ~/.agents/skills/xiaohongshu

# 启动服务
./scripts/start-mcp.sh

# 验证是否启动成功
curl http://localhost:18060/health
# 应该返回 {"status":"ok"}
```

---

### Step 2: 获取 MCP Pro 代码

云端小黄鱼已在服务器上部署，你需要：

**方案A：从服务器复制（推荐）**
```bash
# 从云端服务器复制代码到本地
scp admin@your-server:~/.openclaw/workspace/projects/xiaohongshu-mcp-pro ~/xiaohongshu-mcp-pro

# 进入目录
cd ~/xiaohongshu-mcp-pro

# 安装依赖
npm install

# 启动服务
npm start
```

**方案B：自己克隆GitHub仓库**
```bash
git clone https://github.com/Sheldon-Xie-ux/xiaohongshu-mcp-pro.git
cd xiaohongshu-mcp-pro
npm install
npm start
```

验证服务启动：
```bash
curl http://localhost:18061/health
# 应该返回 {"status":"ok","service":"xiaohongshu-mcp-pro"}
```

---

### Step 3: 完整功能测试

使用测试Key进行验证：

```bash
# 测试Key（云端已生成）
API_KEY="xhs_sk_d93c0170f3c84362907dd385f8738213"

# 1. 查询初始状态
echo "=== 测试1: 查询配额状态 ==="
curl -H "X-API-Key: $API_KEY" http://localhost:18061/stats
# 期望: used: X, remaining: Y

# 2. 测试MCP搜索功能
echo "=== 测试2: MCP搜索 ==="
curl -X POST \
  -H "X-API-Key: $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword": "AI Agent"}' \
  http://localhost:18061/mcp/search

# 3. 再次查询配额，验证计费
echo "=== 测试3: 验证计费 ==="
curl -H "X-API-Key: $API_KEY" http://localhost:18061/stats
# 期望: used 增加了1或2

# 4. 测试无效Key
echo "=== 测试4: 无效Key拒绝 ==="
curl -H "X-API-Key: invalid_key" http://localhost:18061/stats
# 期望: 返回401错误
```

---

### Step 4: 验证数据文件

检查计费是否正确记录：

```bash
# 查看 usage.json
cat data/usage.json

# 查看 keys.json
cat data/keys.json
```

---

## ✅ 测试通过标准

| 检查项 | 期望结果 | 状态 |
|--------|---------|------|
| MCP Pro启动 | 18061端口正常 | ✅ 通过 |
| 无Key访问 | 返回401 | ✅ 通过 |
| 无效Key | 返回401 | ✅ 通过 |
| 有效Key访问 | 返回200 | ✅ 通过 |
| 配额扣减 | used计数+1 | ✅ 通过 |
| 数据持久化 | usage.json更新 | ✅ 通过 |

**测试结果**: 核心功能全部通过 ✅
**测试时间**: 2026-03-07 01:17 (GMT+8)
**测试者**: 本地小黄鱼

**备注**: 
- 原 MCP 服务 (18060) 未测试（本地无此服务）
- MCP 搜索转发功能未测试（依赖原服务）
- API Key 认证、配额计费、数据持久化功能全部正常

---

## 📤 反馈要求

测试完成后，请通过以下方式反馈：

1. **更新本文件**：勾选通过的测试项
2. **GitHub提交**：
   ```bash
   git add .
   git commit -m "测试完成: 小红书MCP Pro - 结果[通过/失败]"
   git push
   ```
3. **飞书通知主人** @谢尧：告知测试结果

---

## 🐛 常见问题

**Q1: 原MCP服务启动失败**
- 检查是否有 cookies.json
- 检查 playwright 是否安装

**Q2: MCP Pro连接MCP失败**
- 检查 MCP_TARGET 环境变量
- 默认是 http://localhost:18060

**Q3: 端口被占用**
- 修改 .env 文件中的 PORT
- 或杀掉占用进程

---

**祝测试顺利！** 🚀
