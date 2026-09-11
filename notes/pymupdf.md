# PyMuPDF

**是什么**：读写、分析、处理 PDF 的 Python 库。在项目里的角色是——**把 PDF 转成程序能处理的文字、页码、图片和版面信息**。官方定位：用于 PDF 等文档的数据提取、分析、转换和处理的**高性能** Python 库。

**能干嘛**：打开 PDF、取总页数、按页提取文字、取文字所在位置、搜索关键词、提取图片、把页面渲染成图片、读目录和链接、合并 / 拆分 / 旋转、添加标注或水印、配合 OCR 处理扫描版 PDF。

## 典型用法

```python
import pymupdf                       # 老代码里写作 import fitz，同一个库

doc = pymupdf.open("lecture.pdf")
print(doc.page_count)
for page in doc:
    text = page.get_text()           # 纯文本
    blocks = page.get_text("blocks")  # 带坐标，做版面分析用
doc.close()
```

## 常用 API

| 用途 | API |
| --- | --- |
| 打开 / 页数 | `pymupdf.open()`、`doc.page_count`、`doc[i]` |
| 取文字 | `page.get_text()`（纯文本）、`page.get_text("blocks" / "words" / "dict")`（带位置） |
| 搜索 | `page.search_for("关键词")` |
| 图片 | `page.get_images()`、`page.get_pixmap(dpi=200).tobytes("png")`（页面渲染成图） |
| 目录 / 链接 | `doc.get_toc()`、`page.get_links()` |
| 加工 | `doc.insert_pdf()` 合并、`doc.select()` / `delete_page()` 拆分、`page.set_rotation()`、`page.add_highlight_annot()`、`page.insert_text()` |
| 保存 | `doc.save("out.pdf")` |

## 注意事项

1. **扫描版 PDF 提不出文字**。有些 PDF 实际是一页页图片，内部没有文字层，`page.get_text()` 可能返回空字符串。判断方法：文字为空但 `page.get_images()` 有内容。这时必须走 OCR——PyMuPDF 可以配合 OCR，但通常还需要额外安装 OCR 引擎（如 Tesseract，中文还要装 `chi_sim` 语言包）。
2. **阅读顺序可能混乱**。双栏论文、复杂课件、带侧边栏的 PDF，提取结果容易出现：左右栏交叉、标题跑到正文后面、页眉页脚混进正文、单词之间缺空格、不正常的换行。原因是 **PDF 记录的重点是"文字画在哪里"，未必保存了自然的阅读顺序**。官方文档也提醒了异常换行和阅读顺序的问题。要更可靠，可以用 `get_text("blocks")` 拿到坐标后自己按栏排序。
3. **表格不一定能直接还原**。视觉上是表格，不代表 PDF 内部存着标准的行列结构。第一版可以把表格当普通文本处理，后续再针对表格加专门解析。
4. **页码从 0 开始**，`doc[0]` 才是第一页，做"第 N 页"的映射时最容易错。
5. **加密 PDF 要先解锁**：`doc.needs_pass` 为真时得先 `doc.authenticate(密码)`，否则取不到内容。
6. 记得 `doc.close()`。处理大批 PDF 时逐文件、逐页处理，别一次性全开在内存里。
7. 如果最终目的是喂给大模型，`pymupdf4llm` 可以直接把 PDF 转成 Markdown，比裸 `get_text()` 更适合 LLM 阅读。
