# charset-normalizer

**是什么**：帮 Python **猜文本文件编码**的库。

**为什么需要它**：计算机存的其实是字节，文字得按某种编码规则去解释。常见的有 UTF-8、GBK、GB18030、UTF-16、Big5。同一个 TXT 文件里写着「数据结构课程资料」，如果它原本是 GBK 存的，程序却强行按 UTF-8 读，结果就是报 `UnicodeDecodeError`，或者更糟——不报错但读出乱码。

**它做什么**：读取文件的原始字节 → 给出最可能的编码 → 转成 Python 字符串。**本质是推测**。

## 典型用法

```python
from charset_normalizer import from_path, from_bytes

best = from_path("data/course.txt").best()
if best is None:                       # 探测失败，必须兜底
    text = open("data/course.txt", encoding="utf-8", errors="replace").read()
else:
    text = str(best)                   # 也可以 best.output()
    print(best.encoding)               # 看它猜的是哪种编码
```

`from_bytes(data)` 用法相同，适合你已经在内存里拿到字节的场景。

## 注意事项

1. **判断不一定对**。这几种情况特别容易猜错：文件内容很短、只有英文和数字、字节同时符合多种编码、文件本身已经损坏。这些都是编码探测的固有局限，不是库的问题。
2. **探测失败要兜底**。`.best()` 可能返回 `None`，代码里必须处理，否则直接 `AttributeError`。别把探测结果当成绝对可信的事实。
3. **建议先试 UTF-8，失败再探测**——而不是无脑先探测。UTF-8 是现在的主流，直接试又快又准。
4. **中文兜底链用 `gb18030` 而不是 `gbk`**。GB18030 是 GBK 的超集，能多覆盖一部分生僻字，容错更好。
5. **`errors="replace"` / `errors="ignore"` 是止血带，不是常规方案**。它们能保证不崩，但会**悄悄丢字或塞进替换符**，让错误往下游传，最后在别的地方以更难查的形式爆出来。只在探测也失败的最后一步用。
6. **小心 UTF-8 BOM**。带 BOM 的文件用 `encoding="utf-8"` 读，开头会多出一个看不见的 `﻿`，导致第一行字符串比较莫名失败——这种要用 `utf-8-sig`。
7. **大文件别整个读进来反复试探**。探测有计算成本，可以只取开头若干 KB 去猜。
8. **别静默接受低置信度的结果**。不确定时把猜到的编码记进日志或让用户确认，比后面满屏乱码好排查得多。

**边界**：它只解决"字节 → 字符串"这一步。如果文字本身是上一步（比如 PyMuPDF 提取）就提坏了的，换什么编码都救不回来。
