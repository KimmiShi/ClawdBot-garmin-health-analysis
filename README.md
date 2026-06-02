# Garmin Health Analysis - CodeBuddy Skill

> **用自然语言查询你的 Garmin 数据** — "我昨天跑得最快多少？"、"昨晚睡得怎么样？"、"下午3点心率多少？"

Fork 自 [eversonl/ClawdBot-garmin-health-analysis](https://github.com/eversonl/ClawdBot-garmin-health-analysis)，在此基础上增加了**中国区支持**和**活动详情/分段查询**功能。

---

## 环境准备

### 1. 安装 Python 依赖

```bash
pip3 install garminconnect fitparse gpxpy
```

### 2. 登录 Garmin Connect

**国际区用户（connect.garmin.com）：**

```bash
python3 scripts/garmin_auth.py login --email YOUR_EMAIL --password YOUR_PASSWORD
```

**中国区用户（connect.garmin.cn）：**

```bash
python3 scripts/garmin_auth.py login --email YOUR_EMAIL --password YOUR_PASSWORD --cn
```

登录成功后 token 会保存在本地（`~/.clawdbot/garmin/` 或 `~/.clawdbot/garmin_cn/`），后续无需重复登录。

### 3. 验证登录状态

```bash
python3 scripts/garmin_auth.py status          # 国际区
python3 scripts/garmin_auth.py status --cn     # 中国区
```

---

## 使用方法

### 基础健康数据

所有数据获取脚本都支持 `--cn` 参数切换中国区：

```bash
# 睡眠数据
python3 scripts/garmin_data.py sleep --cn

# HRV（心率变异性）
python3 scripts/garmin_data.py hrv --cn

# Body Battery（身体电量）
python3 scripts/garmin_data.py body_battery --cn

# 心率
python3 scripts/garmin_data.py heart_rate --cn

# 活动列表（含 activity_id）
python3 scripts/garmin_data.py activities --cn

# 压力
python3 scripts/garmin_data.py stress --cn

# 综合概览
python3 scripts/garmin_data.py summary --cn

# 个人资料
python3 scripts/garmin_data.py profile --cn
```

时间范围可通过 `--days`、`--start`、`--end` 控制：

```bash
python3 scripts/garmin_data.py sleep --days 30 --cn
python3 scripts/garmin_data.py activities --start 2026-05-01 --end 2026-05-31 --cn
```

### 活动分段数据（v1.3.0 新增）

获取某次活动的每圈数据（心率、速度、距离、功率、踏频、海拔等）：

```bash
python3 scripts/garmin_data.py activity_splits --activity-id 600107330 --cn
```

### 活动逐秒详情（v1.3.0 新增）

获取某次活动的逐秒时序数据，支持指标过滤和降采样：

```bash
# 获取全部指标（全精度）
python3 scripts/garmin_data.py activity_detail --activity-id 600107330 --cn

# 只看心率和速度，每30秒采样一次
python3 scripts/garmin_data.py activity_detail --activity-id 600107330 \
  --metrics directHeartRate,directSpeed,sumDistance --sample-interval 30 --cn
```

**常用逐秒指标：**

| 指标 key | 含义 |
|----------|------|
| `directHeartRate` | 实时心率 |
| `directSpeed` | 实时速度 |
| `sumDistance` | 累计距离 |
| `directElevation` | 海拔 |
| `directPower` | 功率 |
| `directRunCadence` | 跑步步频 |
| `directBikeCadence` | 骑行踏频 |
| `directGpsSpeed` | GPS 速度 |
| `directGradeAdjustedSpeed` | 纠坡速度 |
| `directVerticalOscillation` | 垂直振幅 |
| `directGroundContactTime` | 触地时间 |
| `directStrideLength` | 步幅 |
| `directAirTemperature` | 气温 |

> `activity_id` 可从 `activities` 命令的输出中获取。

### 时间点查询

```bash
python3 scripts/garmin_query.py --metric heart_rate --time "3pm" --cn
python3 scripts/garmin_query.py --metric stress --time "上午10点" --cn
```

### 扩展指标

```bash
python3 scripts/garmin_data_extended.py training_readiness --cn
python3 scripts/garmin_data_extended.py body_composition --cn
python3 scripts/garmin_data_extended.py spo2 --cn
```

### 生成图表

```bash
python3 scripts/garmin_chart.py sleep --days 7 --cn
python3 scripts/garmin_chart.py dashboard --days 30 --cn
```

### 活动文件下载与分析

```bash
python3 scripts/garmin_activity_files.py download --activity-id 600107330 --cn
python3 scripts/garmin_activity_files.py analyze --activity-id 600107330 --cn
```

---

## 与上游的区别

| 功能 | 上游 | 本 fork |
|------|------|---------|
| 中国区登录 (`--cn`) | ❌ | ✅ |
| 活动分段查询 (`activity_splits`) | ❌ | ✅ |
| 逐秒详情查询 (`activity_detail`) | ❌ | ✅ |
| 指标过滤 / 降采样 | ❌ | ✅ |
| 活动列表含 `activity_id` | ❌ | ✅ |

---

## 项目结构

```
scripts/
├── garmin_auth.py            # 认证登录（支持 --cn）
├── garmin_data.py            # 基础数据 + 分段/详情查询（支持 --cn）
├── garmin_chart.py           # 生成 HTML 图表
├── garmin_data_extended.py   # 扩展指标（VO2 max、训练准备度等）
├── garmin_activity_files.py  # 下载 FIT/GPX 文件
└── garmin_query.py           # 时间点查询
```

---

## 常见问题

**Q: 中国区登录报错怎么办？**
确保加 `--cn` 参数。中国区使用 `connect.garmin.cn`，不加该参数默认连接国际区。

**Q: Token 过期了？**
重新执行 `login` 命令即可，token 会自动刷新。

**Q: 怎么找到 activity_id？**
先运行 `python3 scripts/garmin_data.py activities --cn`，每条活动的 `activity_id` 字段即为所需 ID。

---

## Credits

- 原作者: [EversonL](https://github.com/eversonl)
- Fork 维护: KimmiShi
- License: MIT
