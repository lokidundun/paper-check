# 论文查重程序

学号：3124004179 入口：`main.py`。运行环境：Python 3.9 及以上。

程序只依赖 Python 标准库，运行时无需安装第三方包，也不联网。

## 先运行一次

在终端进入本文件所在的 `3124004179` 文件夹，执行：

```bash
mkdir -p answers
python3 main.py "$(pwd)/samples/orig.txt" "$(pwd)/samples/orig_add.txt" "$(pwd)/answers/demo.txt"
cat answers/demo.txt
```

标准调用方式：

```text
python3 main.py <原文绝对路径> <待比较文本绝对路径> <答案文件绝对路径>
```

Windows 下可以使用作业指定的 `python main.py ...`。路径有空格时加双引号。实际验证环境为 Windows 11 + Python 3.12，所有测试通过（符号链接相关测试在无权限时自动跳过，不影响评测）。

结果直接写入指定答案文件，正常运行时终端不打印分数。答案范围为 0～1，保留两位小数；例如 `0.85` 代表本算法计算的 85% 相似度。输入必须为 UTF-8，可带 BOM。答案的父文件夹应预先存在。

## 算法与异常约定

- 统一 Unicode 兼容字符、大小写，忽略标点和空白。
- 对单字符、相邻双字符的频数分别计算余弦相似度，按 0.3 和 0.7 加权。
- 任一清洗文本为空时输出 `0.00`；任一文本只有一个有效字符时使用单字符相似度。
- 输出路径不能覆盖任一输入文件，包括符号链接和硬链接指向同一文件的情况。
- 参数数量错误返回退出码 2；路径、编码、读写失败返回 1，并在标准错误中说明；正常或查看帮助返回 0。

## 运行测试

基础测试无需额外依赖：

```bash
python3 -m unittest discover -s tests -v
```

目前有 52 项测试，另有种子固定的随机性质检查。完整结果见 [测试报告](docs/TEST_REPORT.md)。

如需重新生成代码检查、覆盖率或性能报告，在本文件所在目录创建独立开发环境：

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-dev.txt
python -m ruff check .
python -m ruff format --check .
python -m coverage run -m unittest discover -s tests -v
python -m coverage run --append main.py --help
python -m coverage report
python -m coverage html
python -m coverage json -o reports/coverage.json
```

Windows PowerShell 激活环境的命令为 `.venv\Scripts\Activate.ps1`。Windows 若没有创建符号链接的权限，相关测试可能需要系统的开发者模式或相应权限；不影响普通文件比较。

`requirements.txt` 说明程序没有第三方运行依赖；开发工具版本记录在 `requirements-dev.txt`。


## 复现性能分析

```bash
python -m tools.benchmark --label optimized --output reports/performance-rerun
python -m snakeviz reports/performance-rerun/optimized.prof
```

## 参考资料

- [课程作业要求](https://edu.cnblogs.com/campus/gdgy/Class56-Grade2024-CS/homework/15693)
- [Python cProfile 文档](https://docs.python.org/3/library/profile.html)
- [Coverage.py 文档](https://coverage.readthedocs.io/en/7.10.7/)
- [Ruff 文档](https://docs.astral.sh/ruff/)
- [SnakeViz 项目文档](https://jiffyclub.github.io/snakeviz/)
