# Project 开发经验库

记录开发过程中用到的技术、方法和踩坑教训，供以后自己翻看、或直接交给 AI 写代码时参考。

## 记录约定

- 每篇笔记一个主题，按类别放进 `notes/<类别>/` 下（目前只有 `python/`，以后有别的方向再建同级目录）。
- 文件名用英文小写（如 `streamlit.md`），正文用中文。
- **条目式速查，不照抄原文整理**。一篇笔记回答四个问题：
  1. **是什么** —— 这东西是什么
  2. **在哪用 / 能干嘛** —— 适用场景、能解决什么问题
  3. **特点** —— 它跟同类比有什么脾气
  4. **注意事项** —— 坑、限制、容易误解的地方
- **可以适当拓展**：四块的方向不变，但不局限于口述内容——典型用法、常用 API、官方推荐做法、没说到的坑都可以补。拓展要克制，只补影响"用起来会不会踩雷"的东西，不写百科背景和长篇教程。
- 同主题优先补充进已有文件，不重复新建。
- 新笔记写完后，在下方索引加一行。

## 索引

### Python 生态

- [Streamlit](notes/python/streamlit.md) — 只写 Python 就能做可交互网页，核心机制是脚本整段重跑
- [SQLite](notes/python/sqlite.md) — 单文件关系型数据库，Python 标准库自带，为单机单写入者设计
- [PyMuPDF](notes/python/pymupdf.md) — 把 PDF 转成文字/页码/图片/版面信息，扫描版和阅读顺序是两大坑
- [scikit-learn](notes/python/scikit-learn.md) — 传统机器学习工具箱，负责特征、聚类、分类和评估
- [pytest](notes/python/pytest.md) — 自动化测试框架，防回归，让改代码和重构有底气
- [FastAPI](notes/python/fastapi.md) — 后端 API 框架，Streamlit 给人看界面、它给程序调接口
- [charset-normalizer](notes/python/charset-normalizer.md) — 猜测文本编码，解决中文 TXT 乱码和 UnicodeDecodeError
