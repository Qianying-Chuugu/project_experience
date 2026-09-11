# Streamlit

**是什么**：Python 包。只写 Python 就能把数据处理 / 机器学习 / AI 程序做成可交互网页，几乎不需要前端知识。

**替代了什么**：HTML 建页面、CSS 控样式、JavaScript 管交互、后端提供接口——在 Streamlit 里都是 Python 函数。

**在哪用**：快速做原型、验证产品想法、调试模型输出、向老师或同学演示、逐步加功能。

**能展示什么**：表格、图片、Markdown、JSON、折线图 / 柱状图 / 散点图、模型预测结果、聚类结果、实验指标。

**特点**：改完保存 Python 文件即重新加载，不用重启。

**怎么跑**：`streamlit run app.py`，默认开在 `http://localhost:8501`。**不要用 `python app.py` 跑**，那样只会执行脚本、不会起服务。

**常用 API**（这些凑起来够写一个完整应用）：

| 用途 | API |
| --- | --- |
| 输出 | `st.write` / `st.markdown` / `st.title` / `st.dataframe` / `st.metric` / `st.json` / `st.image` / `st.pyplot` |
| 输入 | `st.text_input` / `st.text_area` / `st.selectbox` / `st.slider` / `st.file_uploader` / `st.button` / `st.checkbox` |
| 布局 | `st.sidebar` / `st.columns` / `st.tabs` / `st.expander` / `st.form` |
| 状态 | `st.session_state` |
| 缓存 | `@st.cache_data` / `@st.cache_resource` |

## 注意事项

1. **核心机制是 rerun**：用户改控件或点按钮后，脚本从上到下整个重跑一遍，再根据当前状态重新生成页面。不是局部更新。
2. **普通变量每次重跑都会重置**。要跨交互保住的东西（比如用户选的文件、累积的结果）必须放 `st.session_state`。这是最常见的坑。
3. **重跑 = 重复计算**。加载模型、连数据库、算 TF-IDF 这类耗时操作一定要缓存：数据类用 `@st.cache_data`，连接 / 模型对象用 `@st.cache_resource`。不加缓存，每点一次按钮就重来一遍。
4. **表单能减少重跑次数**：多个输入框用 `st.form` + `st.form_submit_button` 包起来，只在提交时重跑一次，而不是每敲一个字跑一次。
5. 页面是整段重绘的，脚本太长太重就会每次交互都卡——重逻辑要挪进缓存或外部。
6. 定位是原型和内部工具，**不适合高并发、复杂权限管理的生产系统**。
7. 密钥别硬编码，放 `.streamlit/secrets.toml`，用 `st.secrets` 读；这个文件不要提交进 git。
