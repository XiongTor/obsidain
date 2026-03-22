# 📚 Own Library - 个人知识管理系统

> 基于 Obsidian 构建的数字化知识库，整合学习笔记、项目管理和文献阅读。

---

## 🗂️ 仓库结构

```
own_library/
├── 📁 CS/                    # 计算机科学相关笔记
├── 📁 Diary/                 # 日记与日常记录
├── 📁 ML/                    # 机器学习笔记
├── 📁 MOC/                   # Map of Content - 内容地图/索引
├── 📁 project/               # 项目文档与管理
├── 📁 promt/                 # AI Prompt 模板库
├── 📁 references/            # 参考资料与文献
├── 📁 report/                # 报告与总结文档
├── 📁 zotero_note/           # Zotero 文献阅读笔记
├── 📁 imag/                  # 图片资源
├── 📁 homebackground/        # 主页背景图片
├── 📄 Home.md                # 可视化主页（带小组件）
├── 📄 KEY.md                 # 密钥与配置信息
└── 📄 README.md              # 本文件
```

---

## 🚀 快速开始

### 1. 环境要求
- [Obsidian](https://obsidian.md/) 最新版
- 推荐插件：
  - **Dataview** - 动态查询笔记
  - **Contribution Graph** - 可视化小组件
  - **Custom Attachment Location** - 附件管理
  - **Git** - 版本控制

### 2. 打开仓库
```bash
# 克隆仓库
git clone https://github.com/XiongTor/obsidian.git

# 在 Obsidian 中打开文件夹
# 文件 → 打开文件夹 → 选择 obsidian 目录
```

### 3. 主页导航
打开 `Home.md` 进入可视化主页，包含：
- 📊 **MOC 导航** - 快速访问 Reference、Project 等核心模块
- ⏱️ **运行时间** - 主页运行时长统计
- 📅 **周进度条** - 本周时间进度可视化
- ✅ **今日任务** - 自动抓取当天日记中的待办事项

---

## 📝 使用规范

### 笔记命名
- 日记：`YYYY-MM-DD.md`
- 项目：`Project_名称.md`
- 文献：`作者_年份_标题关键词.md`

### 标签体系
- `#todo` - 待办事项
- `#doing` - 进行中
- `#done` - 已完成
- `#reference` - 参考资料
- `#idea` - 灵感想法

### 文件夹用途
| 文件夹 | 用途 |
|--------|------|
| `MOC/` | 内容地图，建立笔记间的索引关系 |
| `Diary/` | 每日记录，支持任务追踪 |
| `zotero_note/` | 与 Zotero 联动的文献笔记 |
| `promt/` | 常用的 AI Prompt 模板 |

---

## 🔗 关联工具

| 工具 | 用途 |
|------|------|
| [Zotero](https://www.zotero.org/) | 文献管理 |
| [OpenClaw](https://openclaw.ai/) | AI 助手集成 |
| Git | 版本控制与备份 |

---

## ⚙️ 配置说明

### Git 同步
```bash
# 提交更改
git add .
git commit -m "update notes"
git push origin main
```

### 密钥管理
敏感信息（API Key、Token 等）统一存放在 `KEY.md`，**请勿提交到公开仓库**。

---

## 📌 注意事项

1. **定期备份** - 虽然使用 Git，仍建议定期导出备份
2. **图片路径** - 使用 `[[图片名]]` 格式，确保在 Obsidian 中正常显示
3. **插件依赖** - 部分页面依赖 Dataview 等插件，首次打开可能需等待索引

---

*Last Updated: 2026-03-22*
