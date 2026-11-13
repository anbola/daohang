# 冬阳汇 · 项目说明书

> 教师用的单文件资源导航站 + 工作台。本文件用于**追溯**：后面再改之前，先读第 2、3、5、7 节。
> 最近更新：2026-11-13（V1.12.0 / v19 免费度校准 + 纯净替代）。

## 1. 这是什么、怎么打开

- **主文件**：`冬阳汇.html`，一个 HTML 单文件，双击 `file://` 就能用，断网也能用；也可以静态托管。
- **数据源**：`sites.json`。离线打开时主文件里的 `const SEED=[...]` 兜底；在线托管时 `applyFeed()` 会用 `sites.json` 合并刷新。
- **数据存哪**：用户收藏 / 固定 / 配色 / 排序都存浏览器 `localStorage`，不上传。首次进教师工作台才弹引导，导航页不弹。
- **部署包**：`deploy/` 里**只放** `index.html` + `sites.json` 两个文件。用户是手动上传的，所以**不要放点开头文件**（`.nojekyll` 传不上去；包里本来也没有 `_` 开头文件，Jekyll 不会漏复制）。
- **独立外链**：成绩核算、智能分班必须保持独立外链（`anbola.github.io/chengji/`、`.../fenban/`），**不内置、不本地化**。

## 2. 最高原则（改任何东西前先读）

**收录总原则（用户 2026-11-13 定稿）：无广告、纯净、免费、好用。**

> 「坚持无广告，纯净，免费，好用这个总原则……原则就是我们要找绿色免费特别版好用的。
> 不管它停更没停更，好用是第一原则。」

配套的用户原话，按优先级：

| 优先级 | 原则 | 出处 |
|---|---|---|
| 最高 | **好用才是硬道理，停更 ≠ 该删**。不能拿「官方已停更 / 版本老」当下架理由 | 2026-11-12 |
| 最高 | **站点增删必须先经用户确认**。体检只出候选清单，「不好的可以删」不等于授权直接删 | 2026-11-12 |
| 高 | **千挑万选万里挑一，同类比较找最好**（v14 大清洗的收录标准） | 2026-11-07 |
| 高 | 找「**去广告无会员直装版**」，官方给的就不用找破解包 | 2026-11-03（最早出现） |
| 高 | 不收破解分发站，不主动搜罗破解版网盘直链；用户自己给的直链可以收，但必须如实写「第三方改版 / 非官方分发 / 装前查毒 / 学校批量用有合规风险」 | 长期约定 |
| 中 | 文案极简：「两个字能说明白的，两个字就把它搞明白」 | 2026-11-05 |
| 中 | 禁止写未核实的隐私断言（「不上传 / 本地处理」）；在线 PDF/OCR 要上传文件，涉密材料提示改用桌面版 | 长期约定 |

## 3. 硬约束清单（不可破）

| 约束 | 说明 |
|---|---|
| 单文件 | 主文件自包含，零外链 CDN、零 base64 位图；图片底纹一律纯 CSS/SVG |
| deploy 两文件 | `deploy/` 只有 `index.html` + `sites.json`，且分别与根目录两份**字节一致** |
| 五套配色 | `dawn / ridge / paper / aurora / night`。结构统一「浅色顶栏 + 深色侧栏 + 高对比正文」，只有 `night` 全站深色 |
| 菜单两层 | v17 定稿：左侧「一级大类 → 一层子项」+ 悬浮展开；右侧 chips **横排一行流**，不许插「装机必备 / 常用软件」档名小标题。`SOFT_SECTIONS.grp` 数据保留（jsdom 仍断言 10 组 4 档）但**不再渲染** |
| 常用默认全空 | v15 起，只有点过 / 固定过的站点才出现，别塞内置默认 |
| 卡片双链接 | 主卡点官方链接，右下角「直链」点直接下载；后台「站点编辑 → 直链」可自己填 |
| 三端一致 | 桌面 / 平板 / 手机都要无横向溢出；标签型卡片按内容自适应但字号恒定 |
| 搜索台 | 固定单行 40px 横条（桌面宽 620px），引擎按钮无圆点、只写 2 字。别改回独立大面板或上下两排 |
| 网盘直链优先级 | 蓝奏云（免登录不限速）> 123 网盘 > 百度网盘（最后，需登录且限速） |
| 在线工具链接 | 优先给打开即可操作的 deep link，不要首页/介绍页；免登录的别经首页；有语言选择就选中文（路径优先于参数，参数须实测） |

## 4. 文件职责

| 文件 | 作用 |
|---|---|
| `冬阳汇.html` | 唯一交付主文件（含 `CATS`、`SEED`、`SEED_TOP`、`SEED_DROP`、`NAV_GROUPS`、`SOFT_SECTIONS`、`DESK_SRC`） |
| `sites.json` | **站点数据唯一源**；`cats / sites / top / picks / pinned / drop / ver / notice` |
| `deploy/index.html`、`deploy/sites.json` | 部署副本，必须与根目录两份字节一致 |
| `_desksrc_aivideo.html`、`_desksrc_teacher.html` | 内嵌工作台的**可读源码**；`DESK_SRC.*` 是转义后的超长字符串 |
| `_sync_seed.py` | 从 `sites.json` 重新生成主文件里的 `SEED / SEED_PICKS / SEED_TOP`，自检条数与重复 |
| `_vXX_data.py` / `_vXX_html.py` | 各版本的数据 / 呈现层迁移脚本，**幂等**（检测版本标记跳过），是本目录的「改动记录」 |
| `_v14_final_jsdom.js` | 主回归（数据一致性、老存档迁移、配色、级联、纯净判定），当前 **135 PASS** |
| `_vXX_final_cdp.js` | 真实 Edge CDP 全场景截图 + 溢出 / 对比度 / 几何测量 |
| `_v16_rect_check.js` | 直链卡片相邻元素矩形间距测量（判断叠压必须用它，不能只看 `overflow:false`） |
| `_v17_linkcheck.py` / `_v17_linkcheck.json` | 全站链接体检脚本与结果 |
| `_chk.js` | 主文件内联 JS 的抽取副本，供 `node --check` 快速验语法（改完主文件要重新抽取，见第 9 节） |
| `*.bak-pre-vXX-日期` | 每轮改动前的主文件 / 数据备份，**不要删** |
| `预览-vXX-*.png` | 各版本交付截图 |
| `.workbuddy/memory/` | 工作日志（按日追加）与 `MEMORY.md`（长期约定），本 README 与它们互为补充 |
| `README.md` | 本文件 |

`_` 开头的一律是工具 / 临时文件，**不上传、不进 deploy**。

## 5. 数据流与「改数据的正确姿势」（最容易踩坑）

```
sites.json（唯一源）
   └─ python _sync_seed.py ──> 主文件 SEED / SEED_PICKS / SEED_TOP（离线兜底）
                                  └─ cp ──> deploy/index.html + deploy/sites.json
```

1. **字段白名单**：`_sync_seed.py` 只序列化 `n,s,u,c,t,f,d,w,g,v,dl,dlk,dlp,dls,must`。
   **新增任何站点字段必须同步加进白名单**，否则离线 `file://` 打开会静默丢字段（v15 直链踩过）。
2. **两者必须完全一致**：改完 `sites.json` 必跑 `_sync_seed.py`，再 `cp` 到 `deploy/`。
3. **换 URL 必须同时下架旧地址**：`applyFeed` 用 `u|c` 合并，`load()` 用 `SEED_VER` 补差；只加新链会让老用户留下僵尸卡。把每个旧 URL 写进 `sites.json.drop` 和主文件 `SEED_DROP`，递增 `SEED_VER` 与 `ver`。
4. **两份 drop 不能互相合并**（v14 定稿）：`sites.json.drop` 是 `applyFeed` 在线合并的黑名单；`SEED_DROP` 是 `mergeSeed` 老存档迁移用的「历史被替换地址」，其中 `wjx.cn / wj.qq.com / obsproject.com` 至今仍是现役卡。并进去会误删现役站点。两份名单只在「本轮新删」这一批保持一致，之后各自独立递增。
5. **递归替换只匹配「值完全等于旧 URL」的字符串**，别做全文文本替换（新 URL 常是旧 URL 的前缀，会串味）。`SEED_DROP` 增删后必查语法：漏逗号会让整个脚本块崩。
6. **修改已有卡片字段（`f`/`d`/`w`）时 `mergeSeed` 只补新卡、不刷字段**，离线老存档看不到修正。要在 `load()` 的 `raw.seedVer!==SEED_VER` 分支加按 URL 精确的 `FIXxx` 对齐（只动指定几条，别全量覆盖，否则抹掉用户后台自定义），并递增 `SEED_VER`。在线托管靠 `applyFeed` 的 `Object.assign` 自动刷新。
7. **改 `top` 首选清单时老存档不会自动刷新**：`FIX16` 用 `for(const k in SEED_TOP) if(!Array.isArray(m.top[k]))` 补新 key，再按 URL `unshift` 到第一（不覆盖用户自定义顺序）。新增任何首选 key 必须靠这一步。
8. **`picks / top / pinned / DEFAULT_PINNED` 都按 URL 引用**，换链后要查悬空引用。
9. **改分类归属**要同时改 `sites.json.cats` 和主文件硬编码的 `const CATS=[...]`，并在 `load()` 迁移分支与 `applyFeed` 开头**两处**都加迁移，否则 `u|c` 合并会造双卡。
10. **改内嵌工作台**：先编辑 `_desksrc_*.html`，再用 `json.dumps(...,ensure_ascii=False)` + `</ → <\/` 写回 `DESK_SRC.*`，回读 `json.loads` 校验，最后同步 `deploy/index.html`。
11. **原子写 + 立刻回读**（2026-11-12 踩过）：`json.dump` 曾没落盘，导致 `_sync_seed.py` 条数对不上。用 `tmp → os.replace`，写完立刻 `json.load` 断言条数与增删项。
12. **纯呈现层改动优先写 `_vXX_html.py` 幂等脚本**（如 v17、v18），不动数据、不动 SEED、不动老存档，所有用户打开即生效，回滚只要 `cp` 备份。

## 6. 版本演进（决策追溯）

| 日期 | 版本 | 做了什么 / 为什么 |
|---|---|---|
| 11-03 | 起始 | 融合「班务通」的数据安全思路（本地存储 + 自动备份快照 + 首启引导只在教师台弹）；成绩核算 / 智能分班定为独立外链；SEED 加 ShareX / Flameshot，理由「去广告无会员直装版，官方即给，不必找破解包」 |
| 11-04 | v4 | 精选改**引用式**（`picks` 只存 URL），621 → 555 条 / 21 分类 / 0 重复；4 套配色 + Unsplash base64 底纹。用户要求「多轮筛选、不要重复累赘」「审美第一」 |
| 11-04 | 事故 | 重建脚本误删整个数据层（`S/DB/defaults/mergeSeed/SEED_VER` 全没），导航站打开即 `S is not defined`，从会话备份恢复。**教训：改主文件先备份、逐块验语法、jsdom boot 验渲染** |
| 11-05 | v5 / v6 | 文案极简；卡片多层背景「原色保底」，图丢了也不塌；`HUBFS` 本地文件存储 |
| 11-06 | v7 | 删 21 张 base64 位图改纯 CSS 渐变。实测：图片不是「运行卡顿」主因（本地首屏仅 65ms），但是「打开慢」主因（下载+解码）；文件 1,190,850 → 850,173 B，gzip 收益约 45% |
| 11-06 | v8 | 比例重构：卡片变矮、字号变大、三端一致。UI `V1.1.0` |
| 11-07 | v9 | 拖拽根因：防复制保护在**捕获阶段**对所有 `dragstart` 调 `preventDefault()`，侧栏不在放行名单 → 加 `draggableOK()`；新增 `SEED_TOP` 分组首选与「首选」角标 |
| 11-07 | v10 | 两项技术结论（见第 10 节）：百度语音接不进单文件、AI 视频统一登录/查额度做不到 |
| 11-07 | v11 | 搜索栏回退成单行 + 链接直达化 |
| 11-07 | v12 | `install`（装机必备）与 `lab`（机房运维）并入 `soft`，一级分类 18 个；`must` 改为靠站点 `must:1` 的跨组精选，不复制数据；问卷 / 投屏 / 信创深挖 |
| 11-07 | v13 | 左侧级联菜单：`NAV_GROUPS` 纯呈现分组，**不动 CATS / hash / 后台排序**；信创 18 → 8「只取精的」 |
| 11-07 | v14 | 五套高对比配色（旧 `paper` 病根是三层历史 CSS 打架，不是色板）；工作台级联同构（38 处 `#nav2` → `:is(#nav,#nav2)`）；**网址大清洗**：删 47 条（收费墙 / 同质 / 空壳）补 26 条顶级开源，687 → 666；`f` 修正如实标注 iLovePDF 广告与额度 |
| 11-08 | v15 | 常用默认全空；**卡片双链接**（补齐 `_sync_seed.py` 字段白名单）；菜单收敛；搜索台单行 40px |
| 11-08 | v16 / v16.1 | 精品软件 10 组 4 档；新机必装跨组精选；问卷筛到 12 条；直链行压紧。**热修**：配色层 `html[data-pal] .item{padding:…!important}` 特异性压过 v16 的 `padding-bottom`，导致来源行叠压说明 8–10px → 让 `.dmeta` 回文档流、选择器加 `html ` 打平；名称行徽章被 v8 `nowrap+overflow:hidden` 切半字 → 只对 3 张直链卡定点修，免费度标签移到信息行 |
| 11-12 | v17 | 菜单去分层：左侧一层悬浮、右侧 chips 横排一行流，删 4 个档名小标题；chips 压紧到桌面 11 个一行。全站体检 668 条 → 0 重复 / 0 空说明 / 0 失效描述；**删莲山课件 + 加 3 条课堂工具**（670 条）；确立「好用第一，停更不等于该删」 |
| 11-13 | v18 / `V1.11.0` | **纯净优先**：`isPure()` / `pureRank()` 让绿色 / 纯净 / 特别版在同组靠前，卡片左侧绿条 + 标签描边，分类页「只看纯净」开关。全部纯呈现层，站点数据一条没动。新增本 README |
| 11-13 | v19 / `V1.12.0` | **免费度校准 + 纯净替代**：4 条带广告卡「免费」→「部分」并补「要无广告用」指引；按用户确认新增 uBlock Origin Lite（Edge 官方商店直达）/ 立创开源硬件平台 / AutoHotkey 三条（670 → **673**）。`SEED_VER 1.0.30`，`FIX19TAGS` 只改 `f`、`ADD19` 追加 `w` 不覆盖后台自定义说明 |

## 7. 用户点名 · 必须靠前保留（清洗前先对照）

| 条目 | 位置 / 要求 |
|---|---|
| **WinRAR 烈火汉化版** `rarlab.com/download.htm` | `soft|file` 第一，也是「新机必装」第一 |
| **WPS 2023 雨糖科技版** `raincandy.tech/wpsoffice_2023_x64/` | `soft|office` 第一；`wps.cn` 官方卡留着更新、右下角挂雨糖直链 |
| **搜狗输入法 去广告精简版** `mefcl.com/sougouime.html` | `soft|tool` 第一 |
| **PDF-XChange PDF 编辑器** `pdf-xchange.com/product/pdf-xchange-editor` | `office|pdf` 第一，且在 `sites` 数组里**物理排第一**（防老存档 `DB.top` 不刷新）；蓝奏直链 `shine.lanzouu.com/iNLR33e4vzjc` |
| **123D Design 2.2.14** `onlinedown.net/soft/979556.htm` | 2017 停更但老机房照常用，**永久保留** |
| **光影魔术手** `neoimaging.cn` | 批量修图经典，**永久保留** |
| **脚本猫** | `plug|mgr` 第一 |
| **极域电子教室** `mythware.com`、**剪映专业版** `capcut.cn` | 官方卡必须留，极域在「机房运维」组 |
| 新机必装其余 | MAS 激活脚本 `massgrave.dev`、7-Zip、火绒、PotPlayer、SDIO、微信电脑版、VC++ 运行库、Slimjet；顺序写在 `top['soft|must']`，靠 `mustRank()` 排 |
| 通用 | 同类的纯净 / 去广告 / 特别版 / 直装版排第一；`must:1` 的 11 条别在清洗时删掉 |

## 8. 站点增删流程与体检判读

1. 体检只出**候选清单**（建议删 / 建议加 / 待复核），不擅自改 `sites.json`。
2. 逐条向用户确认；用户点过头才动数据。
3. 备份 `sites.json` + 主文件 → 原子写 + 立刻回读 → 改 `drop / SEED_DROP / SEED_VER / ver / notice` → `python _sync_seed.py` → `cp` 到 `deploy/` → 回归 + 截图 → 写日志。

**判读体检结果的硬规矩**（2026-11-12 核实）：

- **403 / 401 多是反爬**，不是坏站：Pexels / Pixabay / Flaticon / LottieFiles / Blender / Inkscape / Musopen / Unsplash / 智慧教育平台教材等，浏览器正常打开。
- **超时多为本机网络**：108 个超时里 68 个 `github.com`、14 个 `greasyfork.org`，其余 Gamma / Suno / HeyGen 等境外站。别拿超时当坏站。
- 必须 curl / WebFetch 交叉复核才能判死；本机 DNS 异常不能替代换网络复核。
- 同域名多条（GitHub 68 / crxsoso 23 / GreasyFork 14）多为不同 deep link 或不同脚本，**不是重复卡**。
- **极域「破解版」别去搜**：流通的多是学生摆脱教师控制的工具（JiYuTrainer / OneKeyPlay），不是教师端安装包；剪映 5.9 / 6.01 是官方历史版本号。只等用户给可信直链，或引导官方试用 / 开源替代 Veyon。

### 2026-11-13 本轮筛查结论（v18，数据一条未动）

按「无广告 / 纯净 / 免费 / 好用」逐条复核 670 条：

- **数据本身干净**：0 重复 URL、0 同名、0 空说明、0 失效描述。免费度 `free 362 / open 189 / part 63 / quota 56`。
- **48 条命中「纯净 / 绿色版 / 去广告 / 特别版 / 免安装 / 无捆绑 / 无广告」**，已用绿条 + 排序提前 + 「只看纯净」开关突出，不再需要靠翻卡片找。
- **明确带广告但好用、且卡片已如实写清的**：电子发烧友（广告多）、蛙蛙工具（界面带广告）、StrawPoll / Poll Maker（问卷免费档带广告 / 带徽章）、问卷星（免费版带广告，同组已有腾讯问卷无广告替代）、iLovePDF（免费档有广告，同组已有 PDF24 / BentoPDF）、ClassTools（英文站、免费档有广告）。按「好用第一」原则**全部保留**，不为凑数乱删。
- **用户点名保留的**：123D Design 2.2.14、光影魔术手、解除网页限制、AC-baidu、装机必备合集（蓝奏盘）、OlderGeeks 一律不动。
- **误判防护**：「绿色圃中小学教育网」站名含「绿色」但属课件站，已从纯净判定中排除；「问卷星」警示里写「要无广告用腾讯问卷」，也不能因此被标成纯净。判定规则见主文件 `isPure()`，已有 jsdom 断言逐条锁定。
- **v18 当时没有增删任何站点**，因为「增删必须先经用户确认」。后续两件事用户已逐条点头，在同日 v19 按第 8 节流程执行，见下。


### 2026-11-13 v19 已确认并执行的数据改动

用户明确点头后按第 8 节流程执行，**站点与链接一条未下架**：

- **`f` 校准**：电子发烧友 / 蛙蛙工具 / Poll Maker / StrawPoll 由 `free` → `part`，只改标签；链接 / 分组 / 名称 / 说明一字未动（jsdom 拿改动前备份逐字段断言）。
- **推荐语**：4 条 `w` 末尾追加「要无广告用 XXX」，指向库里已有的纯净工具（CryptPad / Tally / ARSnova / Framadate；BentoPDF / PDF24 / Squoosh / Excalidraw；立创开源 / DF 创客社区）。用「要无广告用」句式，`PURE_BAD` 正则会排除，带广告卡不会被误标绿。
- **新增 3 条纯净替代**（均实测 200、确未收录）：`uBlock Origin Lite` Edge 官方加载项直达 → `plug|ad` 并排第一；`立创开源硬件平台` → `maker|tut`；`AutoHotkey` → `soft|tool`。
- **不收**：`ublockorigin.com`（页面自称 Unofficial independent guide）、完整版 uBO 的 Edge 链接（Manifest V3 已不兼容、将被移除）、`oshwhub.com/explore`（curl 返 418 反爬，首页 200 可用）；不补 Notepad++ / qBittorrent（官网本机超时且库里已有 Notepad3 / Motrix）、FileZilla（官方包曾带捆绑）。
- **老存档迁移**：`FIX19TAGS` 只改 `f`；`ADD19` 用「不含则追加」补 `w`，离线迁移时**不整体覆盖用户在后台改过的说明**（区别于 v14 的 `FIX14` 直接写 `w`）。在线托管仍走 `applyFeed` 的 `Object.assign` 既有刷新逻辑。

## 9. 验证流程（每轮必跑）

```bash
# 0) 改动前备份
cp 冬阳汇.html 冬阳汇.html.bak-pre-vXX-日期
cp sites.json  sites.json.bak-pre-vXX-日期

# 1) 内联 JS 语法（_chk.js 是抽取副本，改完主文件要重新抽取）
python -c "import re,io;h=io.open('冬阳汇.html',encoding='utf-8').read();io.open('_chk.js','w',encoding='utf-8').write('\n'.join(re.findall(r'<script[^>]*>(.*?)</script>',h,re.S)))"
node --check _chk.js

# 2) 改过站点数据才跑（会重写 SEED / SEED_PICKS / SEED_TOP 并自检）
python _sync_seed.py

# 3) 主回归（数据一致性 / 老存档迁移 / 配色 / 级联 / 纯净判定）
node _v14_final_jsdom.js

# 4) 真实 Edge 全场景截图与几何测量（--headless --screenshot 的窄屏是假宽度，不可信）
node _v19_final_cdp.js
node _v16_rect_check.js

# 5) 同步部署包 + 字节比对
cp 冬阳汇.html deploy/index.html && cp sites.json deploy/sites.json
cmp 冬阳汇.html deploy/index.html && cmp sites.json deploy/sites.json
```

验证「叠压」**不能只看 `overflow:false` / `scrollWidth`**，必须量 `getBoundingClientRect()` 相邻元素间距；判读截图要按真实像素裁切放大，缩略图缩放会造成「文字发灰 / 没重叠」的错觉。

## 10. 已知坑 / 不要再做的技术尝试

- **不接百度语音**：百度 ASR REST 要 API Key + Secret Key 换 token，官方明确「浏览器无法跨域直连，必须后端代理」；单文件里放密钥等于公开泄露。现用浏览器原生 `SpeechRecognition`，UI 必须标明「不是百度语音」，按 `not-allowed / network / audio-capture / no-speech` 兜底。Chrome 语音服务国内常返 `network`。
- **不做 AI 视频平台统一登录 / 统一查额度**：账号体系独立、免费额度只存在于网页登录态、无第三方余额接口、付费 API 与网页额度是两套账、同源策略禁止 A 站读 B 站登录态。工作台只做「官网标称值 + 本地记账 + 逐站直达」，数字必须标「参考值，不是余额」。浏览器自动化刷额度踩风控与条款，不做。
- **投屏没有「原生协议通吃」的软件**：iOS AirPlay / 安卓 Miracast / Windows / 麒麟统信是四套东西；唯一跨系统路径是浏览器 WebRTC（Deskreen / VDO.Ninja / Jitsi / screego），代价是延迟与画质。国产 Linux 收 iPhone 用 UxPlay/RPiPlay，收安卓用 MiracleCast。
- **麒麟不是华为的**：银河麒麟属中国电子 CEC / 麒麟软件；华为系是 openEuler（服务器，已捐开放原子基金会）与 OpenHarmony / HarmonyOS，深开鸿 KaihongOS 基于 OpenHarmony。
- 在线表单/问卷必须区分「教师创建要登录」与「学生填写免登录」；国内收集表学生端常要微信/QQ 身份，机房没手机会卡死。
- **CSS 特异性**：配色层 `html[data-pal] .item{padding:…!important}` 是 (0,2,1)，写 `.item.has-dl{…!important}` (0,2,0) 会被压住；要写 `html .item.has-dl{…!important}` 且放在它后面。改首页 h1 字号要用 `html[data-pal] .hero h1` 同款特异性并包 `@media(min-width:1081px)`。
- **名称行徽章裁切**：v8 全局 `html[data-pal] .item .nm{flex-wrap:nowrap!important;overflow:hidden!important}`，别为 3 张卡改全局（会波及全库卡片）。往名称行加节点前先想清楚。
- **`.item` 的伪元素被全局关掉了**（约 3124 行 `.item::before,.item::after{display:none!important}`）。v18 用 `::before` 画纯净绿条时首版整条不显示，必须写 `display:block!important` 才压得住；已有 jsdom 断言防回归。判读「标识生效」不能只看标签描边，要查 `getComputedStyle(el,'::before').display` 或扫截图像素。
- `.wrap{z-index:2}` 会困住移动端抽屉层级，已有 `@media(max-width:1080px){.wrap{z-index:auto!important}}` 修复；改侧栏层级前先想清楚层叠上下文。
- 折叠状态机双击靠 **230ms `navClickTimer` + `dblclick` 抢占清 timer** 保证只切换一次，改这段别丢延时。
- `const/let` 顶层声明（SEED / DB / S / CATS / PALS / navMode）不挂 `window`，jsdom 里要用 `dom.window.eval('…')` 取；函数声明才直接是 `window.xxx`。
- 侧栏文字只能用 `--rail-ink / --rail-ink-2 / --rail-ink-3`；`html[data-pal]:not([data-pal="night"]) .top .wt * / .top-stats .ts` 必须保持深色覆盖。

## 11. 当前状态快照（2026-11-13）

| 项 | 值 |
|---|---|
| UI 版本 | `V1.12.0`（构建 11-13） |
| 站点 | **673** 条 / 18 个一级分类 |
| `sites.json` | `ver 2026.11.13-1`、`drop 142`、`notice v19 · 免费度校准 · 纯净替代 +3` |
| 内嵌兜底 | `SEED_VER 1.0.30`、`SEED_DROP 173` |
| 免费度分布 | `free 359` / `open 191` / `part 67` / `quota 56` |
| 纯净标识 | `isPure()` 命中 **50** 条（约 7.4%），只作呈现层高亮，不改数据 |
| 配色 | 5 套；真实 Edge 侧栏对比度 18.8–19.4:1 |
| 导航 | `NAV_GROUPS` 7 组（6 大类 + 我的工作台）；`SOFT_SECTIONS` 10 组 / 4 档（数据保留、不渲染） |
| 直链卡片 | 3 条（WinRAR / WPS / PDF-XChange 各有网盘直链），桌面卡高约 84px、手机约 79px，无叠压 |
| 问卷 | `soft|vote` 12 条，零账号档 6 条排最前 |
| 回归 | `node --check` 通过 · jsdom **154 PASS / 0 FAIL** · Edge **20 场景**无溢出 |

## 12. 备份与日志索引

- 备份命名：`冬阳汇.html.bak-pre-vXX-YYYYMMDD`、`sites.json.bak-pre-vXX-YYYYMMDD`（v17 数据那轮另有 `-v17data-` 后缀；v19 同日两轮数据改动另有 `-v19` / `-v19b` 后缀）。**不要删备份**。
- 工作日志：`.workbuddy/memory/YYYY-MM-DD.md`（按日追加，append-only）；长期约定：`.workbuddy/memory/MEMORY.md`。
- 各版本迁移脚本 `_vXX_html.py` / `_vXX_data.py` 的**文件头注释里写了用户原话与决策理由**，是比本表更细的一手记录；遇到「为什么当初这么改」先去看那一版脚本的 docstring。
