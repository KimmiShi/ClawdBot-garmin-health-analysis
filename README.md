# Garmin Health Analysis - CodeBuddy Skill

> **用自然语言查询你的 Garmin 数据** — "我昨天跑得最快多少？"、"昨晚睡得怎么样？"、"下午3点心率多少？"

Fork 自 [eversonl/ClawdBot-garmin-health-analysis](https://github.com/eversonl/ClawdBot-garmin-health-analysis)，增加了**中国区支持**和**细粒度活动数据分析**（逐秒时序、逐圈分段）。

---

## 环境准备

### 1. 安装 Python 依赖

```bash
pip3 install garminconnect fitparse gpxpy
```

### 2. 登录 Garmin Connect

中国区用户使用 `--cn` 参数登录：

```bash
python3 scripts/garmin_auth.py login --email YOUR_EMAIL --password YOUR_PASSWORD --cn
```

国际区用户不加 `--cn`：

```bash
python3 scripts/garmin_auth.py login --email YOUR_EMAIL --password YOUR_PASSWORD
```

登录成功后 token 保存在本地（中国区：`~/.clawdbot/garmin_cn/`，国际区：`~/.clawdbot/garmin/`），后续无需重复登录。

验证登录状态：

```bash
python3 scripts/garmin_auth.py status --cn     # 中国区
python3 scripts/garmin_auth.py status          # 国际区
```

---

## 使用方法

在 CodeBuddy 中直接用自然语言提问即可，以下是一些示例 prompt：

### 基础健康数据

- "我昨晚睡得怎么样？"
- "最近一周的心率变化趋势"
- "我的 Body Battery 今天恢复了吗？"
- "最近7天的压力水平"
- "我昨天有哪些运动？"

### 细粒度活动分析（v1.3.0 新增）

这是本 fork 的核心增强——支持**逐圈分段**和**逐秒时序**的深度数据分析：

- "我上周那次跑步，每圈的心率和配速是多少？"
- "5月29号那次4公里跑，速度随时间怎么变化的？"
- "我上次骑车，功率输出曲线什么样？"
- "那次跑步中间心率飙到多少了？在第几公里？"
- "帮我对比一下那次跑和骑行的平均心率"
- "那次长跑的后半程，配速掉了吗？"

支持的逐秒指标包括：实时心率、速度、累计距离、海拔、功率、跑步步频、骑行踏频、GPS速度、纠坡速度、垂直振幅、触地时间、步幅、气温等 20+ 项。

### 时间点查询

- "今天下午3点我的心率是多少？"
- "早上7点的压力水平"

### 扩展指标

- "我的训练准备度怎么样？"
- "最近的血氧数据"
- "体脂率变化趋势"

### 图表与报告

- "帮我画个最近一个月的睡眠趋势图"
- "生成一份健康概览仪表盘"

---

## 与上游的区别

| 功能 | 上游 | 本 fork |
|------|------|---------|
| 中国区登录 (`--cn`) | ❌ | ✅ |
| 逐圈分段查询 (`activity_splits`) | ❌ | ✅ |
| 逐秒时序查询 (`activity_detail`) | ❌ | ✅ |
| 指标过滤 / 降采样 | ❌ | ✅ |
| 活动列表含 `activity_id` | ❌ | ✅ |

---

## 常见问题

**Q: 中国区登录报错怎么办？**
确保加 `--cn` 参数。中国区使用 `connect.garmin.cn`，不加该参数默认连接国际区。

**Q: Token 过期了？**
重新执行 `login` 命令即可，token 会自动刷新。

**Q: 怎么找到 activity_id？**
问 "我最近有哪些运动？"，每条活动会返回 `activity_id`，后续可基于此查询详情。

---

## Credits

- 原作者: [EversonL](https://github.com/eversonl)
- Fork 维护: KimmiShi
- License: MIT
