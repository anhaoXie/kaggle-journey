# Kaggriculture 竞赛冲刺日志 — Day 1

> **日期**: 2026年8月4日  
> **执行人**: 谢安豪  
> **导师**: AI竞赛导师 (Kimi)  
> **阶段**: 筑基期 Day 1 / 50天All-In冲刺  
> **今日目标**: 搭建本地开发环境，跑通开源Agent模拟器，完成首次对战评估

---

## 今日任务清单

- [x] 创建项目文件夹结构
- [x] 下载并解压开源参考项目 (deepeshumrao/kaggriculture-agent)
- [x] 安装Python依赖 (requirements.txt)
- [x] 运行66个单元测试，全部通过
- [x] 运行单机模拟游戏，观察Agent行为
- [x] 运行对战评估 (Smart Agent vs Naive Baseline)
- [x] 记录并分析对战结果

---

## 环境搭建过程

### 1. 项目结构创建

在桌面创建主项目文件夹：

```
Kaggriculture-Agent/
├── src/              # 后续存放自己编写的Agent代码
└── reference/        # 存放开源参考代码
```

### 2. 开源项目获取

- **来源**: GitHub - deepeshumrao/kaggriculture-agent
- **下载方式**: GitHub页面 → Code → Download ZIP
- **解压位置**: Kaggriculture-Agent/reference/
- **注意**: 解压后出现双层嵌套文件夹 kaggriculture-agent-main/kaggriculture-agent-main/，实际代码在最内层

### 3. 开发环境

| 项目 | 版本/路径 |
|---|---|
| 操作系统 | Windows 11 |
| Python | 3.12 |
| IDE | PyCharm |
| 虚拟环境 | PyCharm自动创建的 .venv |
| 项目路径 | C:\Users\谢安豪\Desktop\Kaggriculture-Agent |

### 4. 依赖安装

在PyCharm Terminal中执行：

```powershell
cd "C:\Users\谢安豪\Desktop\Kaggriculture-Agent\reference\kaggriculture-agent-main\kaggriculture-agent-main"
pip install -r requirements.txt
```

**安装结果**: Successfully installed 所有依赖包，包括：
- aiohttp, aiosignal, annotated-types, attrs
- certifi, charset-normalizer, click, colorama
- distro, fastapi, frozenlist, google-auth
- google-genai, graphviz, h11, httpcore
- httpx, idna, iniconfig, joserfc
- jsonschema-specifications, multidict, openai
- opentelemetry-api, opentelemetry-sdk
- pluggy, propcache, pyasn1, pyasn1-modules
- pydantic, pydantic-core, pygments, pyparsing
- pytest, python-dotenv, python-multipart
- referencing, requests, rpds-py, sniffio
- starlette, tenacity, typing-extensions
- typing-inspection, tzdata, tzlocal
- urllib3, uvicorn, watchdog, websockets, yarl

---

## 测试验证

### 单元测试

```powershell
python -m pytest tests/ -v
```

**结果**: 66 passed in 0.39s

测试覆盖范围：
- test_real_agent.py - Agent核心逻辑测试
- test_real_env.py - 游戏环境机制测试
- test_security.py - 安全性测试（注入攻击防护、权限验证等）
- test_sim.py - 模拟器一致性测试
- test_submission_parity.py - 提交格式兼容性测试

所有测试通过意味着：本地模拟器与真实竞赛环境的行为一致，后续在此开发的Agent可以直接提交到Kaggle。

---

## 单机模拟运行

### 命令

```powershell
python scripts/run_local.py
```

**注意**: Windows PowerShell不支持 python -m scripts.run_local，必须使用 python scripts/run_local.py 直接运行。

### 运行日志分析

```
=== Kaggriculture - seed 0, 20 days ===

day  0 | plant plot=0 crop=tomato       | cash    65.00 | planted tomato on plot 0
day  1 | water plot=0                   | cash    65.00 | watered plot 0
day  2 | water plot=0                   | cash    65.00 | watered plot 0
day  3 | water plot=0                   | cash    65.00 | watered plot 0
day  4 | water plot=0                   | cash    65.00 | watered plot 0
day  5 | harvest plot=0                 | cash    65.00 | harvested 12.0 tomato
day  6 | sell crop=tomato               | cash  1631.12 | sold 12.0 tomato for 1566.12
day  7 | plant plot=0 crop=tomato       | cash  1596.12 | planted tomato on plot 0
day  8 | water plot=0                   | cash  1596.12 | watered plot 0
day  9 | water plot=0                   | cash  1596.12 | watered plot 0
day 10 | water plot=0                   | cash  1596.12 | watered plot 0
day 11 | plant plot=1 crop=tomato       | cash  1561.12 | planted tomato on plot 1
day 12 | harvest plot=0                 | cash  1561.12 | harvested 0.0 tomato
day 13 | water plot=1                   | cash  1561.12 | watered plot 1
day 14 | water plot=1                   | cash  1561.12 | watered plot 1
day 15 | water plot=1                   | cash  1561.12 | watered plot 1
day 16 | harvest plot=1                 | cash  1561.12 | harvested 40.0 tomato
day 17 | sell crop=tomato               | cash  6829.52 | sold 40.0 tomato for 5268.4
day 18 | wait                           | cash  6829.52 | waited
day 19 | wait                           | cash  6829.52 | waited | game complete

=== FINAL SCORE: 6829.52 (over 20 days) ===
```

### 行为分析

| 阶段 | 回合 | 动作 | 现金 | 分析 |
|---|---|---|---|---|
| 开局 | day 0 | 种番茄 | $65 | 初始资金65，全部投入买种子 |
| 生长期 | day 1-4 | 浇水x4 | $65 | 番茄需要4天浇水才能成熟 |
| 第一次收获 | day 5 | 收获 | $65 | 收了12个番茄，还在库存 |
| 第一次销售 | day 6 | 卖出 | $1631 | 12个番茄卖$1566，收益率2400% |
| 第二轮 | day 7-11 | 再种+浇水 | $1596->$1561 | 买种子，同时开第二块地 |
| 第二次收获 | day 12,16 | 收获 | $1561 | plot 0收0个（可能bug），plot 1收40个 |
| 第二次销售 | day 17 | 卖出 | $6829 | 40个番茄卖$5268 |
| 终局 | day 18-19 | 等待 | $6829 | 时间不够再种一轮，干等 |

### 关键发现

1. **番茄是暴利作物**: 投入约$35种子成本，产出$1566+，ROI超过4000%
2. **单线程种植**: 虽然开了plot 1，但两块地没有并行管理，存在效率损失
3. **终局策略缺失**: 最后2天空等，没有利用剩余资金进行市场操作
4. **plot 0收获异常**: day 12 harvested 0.0 tomato，可能是浇水次数不足或成熟判断问题

---

## 对战评估

### 命令

```powershell
python scripts/evaluate.py --episodes 5 --days 20
```

### 评估结果

```
=== Evaluation over 5 games (20 days each) ===
Smart agent  avg score :  6583.39
Naive baseline avg     :  1114.11
Improvement over baseline:  490.9%
Win rate vs baseline    : 5/5 (100%)
Best known crop (learned): tomato
```

### 数据解读

| 指标 | 数值 | 意义 |
|---|---|---|
| Smart Agent 平均分 | $6,583.39 | 开源Agent的平均表现 |
| Naive Baseline 平均分 | $1,114.11 | 简单规则对手的基准线 |
| 提升幅度 | 490.9% | Smart Agent是Baseline的约6倍 |
| 胜率 | 5/5 (100%) | 5局全胜 |
| 最佳作物 | 番茄 | Agent自动学习到的最优选择 |

### 对比分析

**为什么Smart Agent能赢490%？**

1. **作物选择**: Baseline可能随机选择作物，而Smart Agent锁定番茄（最高ROI）
2. **种植效率**: Smart Agent有更优的种植-浇水-收获时序安排
3. **市场时机**: Smart Agent懂得在合适时机卖出，可能利用了价格波动
4. **资源管理**: 更有效地分配资金和地块资源

**Baseline的弱点**:
- 不会选择最优作物
- 种植节奏混乱（可能错过浇水时机）
- 不会把握卖出时机
- 地块利用率低

---

## 今日学习心得

### 技术层面

1. **Windows开发注意事项**:
   - PowerShell不支持 ls 命令，需用 dir
   - python -m scripts.xxx 在Windows上可能因模块路径问题失败，改用 python scripts/xxx.py 更稳定
   - 路径中包含中文用户名（谢安豪）不影响运行，但建议后续项目避免中文路径以防万一

2. **项目结构理解**:
   - scripts/run_local.py - 单机模拟运行
   - scripts/evaluate.py - Agent vs Baseline评估
   - scripts/run_kaggle.py - Kaggle提交相关
   - scripts/run_real.py - 真实环境运行
   - tests/ - 66个自动化测试
   - src/ - Agent核心源码（待明天精读）

3. **依赖管理**:
   - requirements.txt 是Python项目的标准依赖清单
   - pip install -r requirements.txt 一键安装所有依赖
   - 安装过程中自动解决了包之间的版本兼容问题

### 策略层面

1. **规则策略的强大**: 这个开源Agent使用的是纯规则策略（非深度学习），但凭借清晰的逻辑和高效的资源管理，实现了490%的提升。这验证了"规则写对就能参赛"的判断。

2. **番茄经济学**: 番茄是游戏中最高效的作物，4天成熟周期、极高的售价/成本比。理解作物的经济属性比写复杂代码更重要。

3. **评估驱动开发**: evaluate.py 提供了量化指标，后续迭代策略时可以精确衡量改进效果。这是科学调优的基础。

### 工程层面

1. **测试的重要性**: 66个测试覆盖了Agent逻辑、游戏机制、安全性、提交格式，确保代码在本地和Kaggle环境行为一致。

2. **版本控制准备**: 明天需要初始化Git仓库，建立规范的commit习惯。

---

## 今日进度追踪

| 里程碑 | 计划时间 | 实际完成 | 状态 |
|---|---|---|---|
| 创建项目结构 | Day 1 | 2026/8/4 | 完成 |
| 下载开源代码 | Day 1 | 2026/8/4 | 完成 |
| 安装依赖 | Day 1 | 2026/8/4 | 完成 |
| 66个测试通过 | Day 1 | 2026/8/4 | 完成 |
| 单机运行成功 | Day 1 | 2026/8/4 | 完成 |
| 对战评估完成 | Day 1 | 2026/8/4 | 完成 |

**今日完成度: 100%**

---

## 明日计划 (Day 2: 2026年8月5日)

### 核心目标
精读开源Agent源码，理解其决策逻辑，画出流程图，手写规则摘要。

### 具体任务

1. **阅读 src/ 目录下的核心代码文件**
   - Agent主逻辑文件
   - 决策模块（种植/浇水/收获/卖出）
   - 路径规划模块
   - 市场分析模块

2. **画出Agent决策流程图**
   - 每回合的决策顺序
   - 各模块之间的调用关系
   - 关键参数和阈值

3. **手写中文规则摘要**
   - 游戏核心机制（observation/action格式）
   - Agent策略要点
   - 关键参数说明
   - 贴在桌前，随时查阅

4. **初始化Git仓库**
  