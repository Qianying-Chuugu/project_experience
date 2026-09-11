# FastAPI

**是什么**：用 Python 开发**后端 Web 接口（API）**的框架。基于 Python 类型注解，能**自动生成接口文档**并**自动校验请求数据**。

**和 Streamlit 的分工**：Streamlit 负责**给人看的网页界面**；FastAPI 负责**提供给网页或其他程序调用的功能接口**。一个是脸，一个是插座。

**什么时候才该引入**（不满足就先别加）：

- 希望前后端分离
- 想将来换成 React 等专业前端
- 希望手机或其他程序调用
- 多个用户可能同时使用
- 想单独部署 AI 分析服务
- 希望任务在后台运行
- 想展示接口设计和后端工程能力

## 典型用法

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class AnalyzeRequest(BaseModel):
    text: str
    top_k: int = 5          # 带默认值，可选

@app.post("/analyze")
def analyze(req: AnalyzeRequest):
    return {"keywords": extract(req.text, req.top_k)}
```

跑：`uvicorn main:app --reload`。启动后自带交互式文档：`/docs`（Swagger UI）、`/redoc`。

## 注意事项

1. **类型注解是核心，也是收益来源**。请求体用 Pydantic 模型定义，字段类型、必填可选写清楚，校验和文档就都自动有了；校验不通过会直接返回 422。
2. **`async def` 和 `def` 要选对，这是最大的坑**。`async def` 路由里调用阻塞函数（PyMuPDF 解析、sklearn 训练、`sqlite3` 查询）会**卡住整个事件循环**，所有请求一起变慢。两种正确做法：要么把这类路由写成普通 `def`（FastAPI 会自动丢进线程池），要么用 `run_in_threadpool` 包一下。
3. **后台任务 ≠ 任务队列**。`BackgroundTasks` 只适合轻量的短任务；真正的长任务（整批 PDF 解析、模型训练）要上 Celery / RQ 之类的队列，否则进程一重启任务就没了。
4. **前后端分离后要配 CORS**。前端和 API 不在同一个源时，浏览器会拦请求，需要在 `app.add_middleware(CORSMiddleware, ...)` 里放行。
5. 共用逻辑抽成 `Depends()` 依赖（数据库连接、鉴权），别在每个路由里复制一遍。
6. 响应也别直接返回裸 dict——定义响应模型能让输出稳定、文档更清楚，还能防止把内部字段漏出去。
7. **它不是"项目升级的必经之路"**。只是想给自己做个界面、单机跑，Streamlit 就够了；引入 FastAPI 意味着多一层服务、多一套部署和调试成本，上面那七条需求出现了再加。

**边界**：FastAPI 只负责"把接口暴露出去、把数据校验好"。它不管界面，也不管你后台到底怎么算——那部分还是 PyMuPDF / scikit-learn 的活。
