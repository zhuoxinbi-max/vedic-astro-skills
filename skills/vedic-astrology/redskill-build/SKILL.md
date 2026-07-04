---
name: vedic-astrology
description: 吠陀占星(Vedic/Jyotish)完整分析系统。从星盘PDF/截图读取数据、执行P1-P12行星审计、分盘交叉分析、宫位诊断、十大板块人生总结，支持出生时间校准、职业分析、恋爱分析、双人合盘和Q&A追问。当用户提到"印度占星""吠陀占星""Jyotish""星盘""vedic""排盘""看盘""占星分析""读盘""时间校准""职业分析""恋爱分析""合盘""两个人合不合"等关键词时触发。
---

# 吠陀占星完整分析系统 (Vedic Astrology)

本 Skill 包含 7 个子模块，覆盖吠陀占星的完整工作流：

| 模块 | 功能 | 触发场景 |
|:-----|:-----|:---------|
| **Reader** | 从星盘PDF/截图提取行星数据 | 用户提供星盘文件时 |
| **Calculator** | 基于 swisseph 的精确计算引擎 | 需要从零计算星盘时 |
| **Core** | P1-P12行星审计 + 格局扫描 + 十大板块人生总结 + Q&A | 数据提取完成后 |
| **Rectifier** | 出生时间校准（±5分钟精度） | 用户出生时间不确定时 |
| **Career** | 职业方向深度分析 | 用户问职业/事业时 |
| **Love** | 恋爱时机与配偶分析 | 用户问感情/婚姻时 |
| **Synastry** | 双人合盘（六维矩阵分析） | 用户问两人关系时 |

---

## 工作流

### 标准流程（有星盘PDF/截图）

```
1. Reader读盘 → 输出 structured_data.md + user_context.md
2. Core分析 → 输出 p1-p5 系列报告
3. Q&A追问 → 输出 qa_主题.md
```

### 无星盘流程

```
1. Calculator计算 → 输出 structured_data.md
2. Core分析 → 同上
```

### 专项分析

```
- 时间校准：Rectifier（需要5个人生事件）
- 职业分析：Career（需要 structured_data.md）
- 恋爱分析：Love（需要 structured_data.md）
- 合盘分析：Synastry（需要双方各一份 structured_data.md）
```

---

## 模块路由

### 当用户提供星盘PDF、截图或文本数据时

→ 读取 `resources/reader.md` 执行读盘流程

Reader 子模块资源文件：
- `resources/chart_reading_rules.md` — 图表识别规则
- `resources/data_contract.md` — 数据输出契约
- `resources/validation_rules.md` — 数据校验规则

### 当需要从零计算星盘时

→ 读取 `resources/calculator.md` 执行计算

Calculator 脚本：
- `scripts/engine.py` — swisseph 计算核心
- `scripts/formatter.py` — 输出格式化
- `scripts/setup_env.py` — 环境配置
- `scripts/transit.py` — 行运计算
- `scripts/ashtakavarga_pyjhora.py` — SAV/BAV 计算封装
- `scripts/shadbala_pyjhora.py` — 六力值计算封装
- `scripts/dasha_pyjhora.py` — 大运系统封装
- `scripts/divisional_pyjhora.py` — 分盘计算封装
- `scripts/extras_pyjhora.py` — 附加数据封装
- `scripts/ephe/` — Swiss Ephemeris 星历数据（seas_18.se1, semo_18.se1, sepl_18.se1）

依赖：`requirements.txt`

### 当 structured_data.md 就绪，需要分析时

→ 读取 `resources/core.md` 执行核心分析

Core 子模块资源文件：
- `resources/p1_p12.md` — P1-P12 参数审计规则（含完整6条NBRY落陷补救）
- `resources/house_framework.md` — 宫位框架
- `resources/yogas.md` — 格局判定规则（含Guru Chandala/Shakata等10种格局）
- `resources/qa_rules.md` — Q&A 规则（含伦理约束）
- `resources/report_rules.md` — 报告生成规则

Core 脚本：
- `scripts/report_builder.py` — HTML 报告打包

### 当用户要求时间校准时

→ 读取 `resources/rectifier.md` 执行校准

Rectifier 资源：
- `resources/event_house_map.md` — 事件-宫位映射表

Rectifier 脚本：
- `scripts/time_scan.py` — 时间扫描（swisseph 精确计算）

### 当用户问职业/事业方向时

→ 读取 `resources/career.md` 执行职业分析

### 当用户问感情/恋爱/婚姻时

→ 读取 `resources/love.md` 执行恋爱分析

### 当用户问两人关系/合盘时

→ 读取 `resources/synastry.md` 执行合盘分析

Synastry 资源：
- `resources/synastry_aspect-policy.md` — 跨盘相位规则
- `resources/synastry_koota-policy.md` — 月宿匹配规则
- `resources/synastry_signal-triage.md` — 信号分诊
- `resources/synastry_interpretation-rubric.md` — 解读规范
- `resources/synastry_romantic-framework.md` — 恋爱框架
- `resources/synastry_business-framework.md` — 合作框架
- `resources/synastry_friendship-framework.md` — 友情框架
- `resources/synastry_family-framework.md` — 家庭框架
- `resources/synastry_general-framework.md` — 通用框架

Synastry 脚本：
- `scripts/build_synastry_data.py` — 跨盘计算引擎
- `scripts/validate_synastry_data.py` — 双盘自检

---

> **⚠️ 文件解码（首次使用前必须执行）**
>
> 由于平台上传限制，部分文件使用了 `.txt` 后缀。**首次运行任何 Calculator/Rectifier/Synastry 脚本前**，必须先在 `scripts/` 目录下执行以下重命名操作：
>
> ```bash
> # 将所有 .py.txt 恢复为 .py
> cd scripts/
> for f in *.py.txt; do mv "$f" "${f%.txt}"; done
>
> # 将星历数据 .se1.txt 恢复为 .se1
> cd ephe/
> for f in *.se1.txt; do mv "$f" "${f%.txt}"; done
> ```
>
> Windows PowerShell 等效命令：
> ```powershell
> Get-ChildItem scripts/*.py.txt | Rename-Item -NewName { $_.Name -replace '\.txt$','' }
> Get-ChildItem scripts/ephe/*.se1.txt | Rename-Item -NewName { $_.Name -replace '\.txt$','' }
> ```
>
> 重命名完成后才能正常运行 `setup_env.py`、`engine.py` 等脚本。

---

## 关键原则

1. **三层 Context 梯度**：Step 1-3 纯盲审 → Step 4 可佐证但禁反推 → QA 全开（必读 user_context.md）
2. **人文关怀（Q&A）**：分析结论不改，但表达考虑用户感受。伦理红线不可触碰
3. **数据驱动**：所有结论基于 structured_data.md，禁止幻觉
4. **逻辑链**：P1→P12 逐层推导，格局预扫描（读取yogas.md）先于行星审计
5. **人话优先**：70%解读 + 20%数据 + 10%注释
6. **反确认偏误**：不从用户经历反推盘面含义

## License

AGPL-3.0 — 个人使用无限制，商用需开源全部服务端代码。详见 GitHub 仓库。
