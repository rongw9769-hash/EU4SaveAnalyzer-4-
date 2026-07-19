# EU4 存档分析器 / Europa Universalis IV Save Parser

这是一个面向 **Europa Universalis IV（EU4，不是 UE4）** 的 Python 3.11+ 命令行 MVP。它读取 Clausewitz/Paradox 文本存档或普通 ZIP 压缩 `.eu4` 存档，识别玩家国家，并生成 JSON 与 HTML 国家概况报告。

当前 v0.18.0 同时提供 Windows 图形界面便携版。用户无需安装 Python，双击程序、选择存档即可生成报告。

v0.18.0 在国内省份上方加入附庸概览，显示当前有效附庸的类型、建立日期、忠诚度与独立倾向，并提供与大区国家概览一致的财政、发展度、军队、科技、附属国和外交快照。已结束的历史附庸不会混入当前列表。

v0.17.0 在每场战争卡片中加入战役二级菜单。战役按日期排序，展示陆战/海战、交战国家、指挥官、地点、初始兵力、损失和玩家胜负；默认显示前 10 场，更多记录可一键展开。仅展示存档实际保留的数据，不推测缺失字段。

v0.16.1 修复部分压缩存档把启用 MOD 列表保存在 `meta`、而不是 `gamestate` 时被误判为无 MOD 的问题。现在会合并读取两个区段并去重，确保汉化 MOD 能按存档记录正常生效。

v0.16.0 取消报告中的中文/English 手动切换。报告只读取游戏本体及该存档记录为已启用的 MOD 本地化，不再扫描未启用的翻译 MOD；界面语言根据实际加载的本地化内容自动确定，MOD 人名、地名和混合语言文本保持游戏原样。报告会注明本次使用的本地化来源。

v0.15.0 修复 EU4 汉化控制字节被误判为换行的问题，统一提高国家、区域、省份、叛军与大区名称的中文覆盖率；实际游戏或 MOD 本地化始终优先，内置词典只作缺失兜底，不按玩家国家 TAG 选择解析路径。表格、清单、战争、条约、特权、效果和编年史超过 10 项时默认显示前 10 项，可点击查看全部并再次收起。

v0.11.0 为 EU4 官方列强榜和世界大区国家概览加入存档截止时的月收入、月支出与月结余，并改进中文双字节文本解析。汉化映射按本地化键及编码组匹配，不绑定特定国家、地区或时间；普通 UTF-8、本体英文及启用模组仍按各自实际本地化读取。

## MVP 范围

- 支持 `EU4txt` 明文存档。
- 支持包含 `meta` 与 `gamestate` 的普通 ZIP 存档。
- 流式读取和分词，不创建整个存档的多份内存副本。
- 解析嵌套 `{}`、`key=value`、字符串、数字、日期、布尔值、列表及重复键。
- 提取玩家 tag、日期、统治者、经济、军事、科技、理念、制度、宗教、文化、政府、省份与主要外交关系。
- 未识别字段保留在通用解析树中，不因未知字段失败。
- 输出结构化 JSON 和单文件 HTML 报告。
- 从国家、逐省和战争历史中生成按日期排序的国家编年史。
- 追踪国家标签前身，整理君主、继承人、国号、文化、领土得失与参战记录。
- HTML 时间线支持按事件类型筛选。
- 同一天、同一所有者变化产生的多个省份事件会自动合并，保留完整省份清单。
- 区分 1444 开局前的预设历史，并显示每个分类的事件数量。
- 陆军统计按实际军团与当前有效兵力计算，不再把军团编队数误作兵力。
- 军事实力模块展示人力与海员储备、陆海军编制、有效兵力、部队位置、当前兵种、陆海军传统、职业度和将领能力；不伪造存档未直接提供的最终军队上限或纪律。
- 国内省份模块以 EU4 `area` 为一级单位按玩家实际拥有发展度排名，二级菜单展示 area 内全部地图地块；部分控制区域会灰色显示外国地块及其所有者。
- 战争记录包含进攻方、防守方、玩家阵营、战争目标、持续时间、战斗胜负、伤亡和参与度；每场战争的二级菜单可查看存档保留的详细陆战和海战记录。
- 和平条约缺失时，将同日领土变化作为明确标注的推断结果，不冒充原始条约。
- 经济概况包含上月收入、支出、结余、分类账，以及逐笔贷款的本金、利率、到期日、剩余天数、类型和年利息。
- 自动查找 Steam 中的 EU4 与已启用模组本地化；使用汉化模组时优先显示中文国家、大区和省份名。
- 君主、继承人与王朝若未被模组汉化，会尝试内置中文音译并保留拉丁原名；无法可靠音译的名字保持原文。
- 外交报告只展示存档截止日期仍可确认的当前关系；单份存档无法完整恢复的历史联盟不予展示。附庸概览显示类型、开始日期、忠诚度、独立倾向及当前国力。
- 从国家外交历史中读取已接受的和平协议，包括割地、归还核心、金币和战争赔款。
- HTML 提供战争卡片、和平条约、财政图表、贷款到期表、债务警告和重要外交关系表。
- 从 1444 开局日期到存档日期重建省份数、累计领土得失、同时战争数和君主能力曲线。
- 读取 EU4 安装目录的 `area.txt`、`region.txt` 与 `superregion.txt`，统计玩家所属大区的国家、省份、发展度、军队、国库与外交关系。
- 大区领土采用区域优先显示：完整拥有一个 `area` 时显示区域名，未完整拥有时显示具体省份名，且不重复列出已归入完整区域的省份。
- 分析全部 `superregion`，并分为首都大区、国家相关大区和其他大区；默认聚焦玩家、附庸或当前交战方拥有领土的大区，也可切换和快速跳转查看全部。
- 读取存档 `great_powers`，展示 EU4 官方八大列强排名、列强分数和当前国力，不生成自定义排名。
- 大区国家概览同时展示区内省份/发展度和全国省份/总发展度，以及陆海军、人力、国库、三项科技、附庸发展度、当前战争、盟友、宿敌和与玩家关系。
- 国家治理页会从游戏本体及启用模组中读取政府改革和当前国家修正的直接 Buff，区分百分比与固定数值，并标注无法静态计算的条件性动态效果。
- 国内阶层页展示各阶层当前忠诚、重建后的忠诚均衡点与影响力、阶层土地、态度 Buff，以及所有已授予特权的直接和条件 Buff。
- 条件 Buff 使用生效、不生效、无法确认三态判定：不适用于当前国家的效果会隐藏，无法静态确定的效果折叠为潜在效果。
- 国家治理页直接读取存档中的当前灾难，显示灾难中文名、国家修正及关联阶层。
- Windows 图形界面可以自动调用 Rakaly 处理二进制存档，转换过程使用临时副本，不修改原始存档。

二进制、加密或无法直接解码的 Ironman 存档不在 MVP 承诺范围内。不同 EU4 版本和模组的字段会变化；可使用 `--tag` 覆盖自动识别结果。

随 Windows 便携版附带的 Rakaly 可以处理常见 EU4 二进制存档，但加密、损坏或新版本尚未支持的存档仍可能失败。

## Windows 便携版

从 GitHub Releases 下载 `EU4SaveAnalyzer-v0.18.0-windows-x64.zip`，解压后双击 `EU4SaveAnalyzer.exe`。程序会自动寻找 Steam 中的 EU4 安装目录，并在需要时调用同目录的 `rakaly.exe`。生成结果统一保存在程序目录下的 `reports\存档名` 文件夹，不会写入 EU4 存档目录。

本地构建便携版：

```powershell
python -m pip install -r requirements-dev.txt
.\build_release.ps1 -RakalyPath "C:\tools\rakaly.exe"
```

输出位于 `release\EU4SaveAnalyzer`，可上传 GitHub Release 的压缩包位于 `release\EU4SaveAnalyzer-v0.18.0-windows-x64.zip`。

## 安装与运行

Windows PowerShell 下最简单的方式是不安装，直接使用启动脚本：

```powershell
.\run.ps1 "C:\path\campaign.eu4"
.\run.ps1 "C:\path\campaign.eu4" -Tag FRA -Output report
.\run.ps1 "C:\path\campaign.eu4" -GameDir "E:\SteamLibrary\steamapps\common\Europa Universalis IV"
.\run.ps1 "C:\path\campaign.eu4" -Superregion persia_superregion,india_superregion
```

运行测试：

```powershell
.\run.ps1 -Test
```

脚本会优先寻找 Codex 自带的 Python，然后尝试 `py` 和 `python`。

也可以创建虚拟环境并安装命令行入口：

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -e .
eu4-save-parser "C:\path\campaign.eu4" --output report
```

也可以不安装入口脚本：

```powershell
python -m eu4_save_parser.main "C:\path\campaign.eu4" --tag FRA --output report
```

运行完成后会产生 `FRA.json` 和 `FRA.html`（tag 随玩家国家变化）。启用详细日志可加 `--verbose`。

## 测试

```powershell
python -m unittest discover -v
```

测试夹具使用接近真实 EU4 Clausewitz 存档的字段与结构，并覆盖明文、压缩、嵌套解析和端到端报告。

## 项目结构

```text
eu4_save_parser/
  save_loader.py          # 明文/ZIP 检测，meta 与 gamestate 流
  tokenizer.py            # 增量 Clausewitz 分词器
  parser.py               # 通用嵌套解析器
  effect_definitions.py   # 政府改革与国家修正 Buff 定义
  models.py               # dataclass 报告模型
  extractors/
    country.py            # 玩家识别和国家快照
    estate.py             # 阶层、特权、忠诚与影响力重建
    disaster.py           # 当前国家灾难及灾难修正
    history.py            # 编年史、战争和和平条约
    province.py           # 省份归属后备提取
    trends.py             # 可从单份存档重建的历史曲线
    region.py             # EU4 大区定义和区域实力汇总
    war.py                # 战争数据入口
  report/
    json_report.py
    html_report.py
  main.py                 # CLI
  gui.py                  # Windows 图形启动器
  converter.py            # Rakaly 自动转换与临时文件管理
tests/
  fixtures/france_plain.eu4
```

## 历史报告

第二阶段的首个版本已经接入。程序会把存档中可确认的历史统一转换为事件模型，每条事件包含日期、类型、标题、说明、相关国家、省份和数据来源。对于 Rakaly 无法还原的模组本地化名称，会回退到稳定的省份编号或战争编号，不展示损坏文本。

单份存档通常不包含每个月的经济和军队快照，因此国库、收支、军队、人力和总发展度的长期趋势仍需要后续的多存档目录分析功能；程序不会用插值伪造这些指标。

## 实现方案与后续阶段

载入层负责格式判断并提供文本流；分词和解析层只关心 Clausewitz 语法；提取层把版本相关字段映射为稳定的数据模型；报告层不依赖原始解析细节。这种分层允许后续分别增加版本适配器和编年史提取器。

下一步适合增加“多存档目录分析”：按日期批量读取同一战役的自动存档，生成真实的国库、收支、债务、军队、人力和发展度曲线。区域层还可继续增加区内战争阵营、宗教版图和省份级地图。PDF 可由稳定的 HTML 模板再渲染生成。

## GitHub 发布注意事项

仓库已忽略 `.eu4`、`outputs/`、`release/` 和临时构建目录，不应上传玩家存档或 EU4 游戏文件。项目代码采用 MIT License；便携版中的 Rakaly 版权说明见 `THIRD_PARTY_NOTICES.md`。本项目是非官方社区工具，与 Paradox Interactive 无关联。
