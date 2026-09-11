# pytest

**是什么**：Python 里最常用的自动化测试框架。你先把"正确结果应该是什么"写下来，pytest 自动运行项目代码，检查实际结果符不符合预期。

**能干嘛**：主要是**防回归**——改了一个功能之后，立刻发现有没有意外弄坏其他已经做好的功能。

**在哪用**：项目功能基本能跑通之后就加上，之后每次改代码都能拿出来跑一遍。

**定位**：它不是项目功能，而是项目的**质量保障工具**。两个好处：一是让你敢放心修改和重构，二是有东西证明你的项目不只是"演示时能运行"，而是经过可重复验证的。

## 典型用法

```python
# test_extract.py
import pytest
from myproject.extract import clean_text

def test_clean_text_去掉多余空白():
    assert clean_text("a  b\n\nc") == "a b c"

@pytest.mark.parametrize("raw, expected", [
    ("", ""),
    ("   ", ""),
    ("正常", "正常"),
])
def test_clean_text_边界情况(raw, expected):
    assert clean_text(raw) == expected

def test_读取不存在的文件应报错():
    with pytest.raises(FileNotFoundError):
        clean_text_from("not_exist.pdf")
```

跑：`pytest`（或 `python -m pytest`）。常用参数：`-v` 看每条用例、`-x` 首个失败就停、`-k 关键词` 只跑名字匹配的、`--lf` 只跑上次失败的。

## 注意事项

1. **断言直接写 `assert`**，不需要 `assertEqual` 那一套；失败时 pytest 会把两边的值展开给你看。
2. **命名必须符合约定**，否则用例根本不会被收集：文件叫 `test_*.py` 或 `*_test.py`，函数叫 `test_*`。写完发现"一个都没跑"，八成是命名问题。
3. **测试要独立、可重复**：不能依赖执行顺序，也不能依赖上一次留下的状态。重复跑两次结果必须一样。
4. **别碰真实数据**。测试里不要写真实数据库和真实文件路径——用 `tmp_path` 这个内置临时目录 fixture，SQLite 用临时文件或内存库 `sqlite3.connect(":memory:")`。否则跑一次测试就把项目数据改了。
5. **耗时的测试单独标记**。提取 PDF、跑 OCR、训练模型这类很慢的用例，用 `@pytest.mark.slow` 标出来，平时跳过（`-m "not slow"`），否则整个套件越跑越久，最后就没人愿意跑了。
6. **fixture 复用准备数据**：`@pytest.fixture` 用来准备测试数据或临时资源，多个用例共享，比每个用例里重复搭一遍干净。
7. 浮点数比较别用 `==`，用 `pytest.approx()`。

**边界**：pytest 只负责"跑"和"报"，用例本身要你自己写。它验证的是"代码行为符不符合你写下的预期"——预期写错了，测试全绿也是假的。
