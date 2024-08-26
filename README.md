# OpenBB LLM 智能体代理
正在进行中。

这是一个利用LLM（大型语言模型）和[OpenBB平台](https://github.com/OpenBB-finance/OpenBBTerminal/tree/develop/openbb_platform) 来创建金融分析师智能体代理的项目，这些智能体代理能够自主执行金融研究，并使用最新数据回答问题。这是可能的，因为智能体代理使用函数调用与OpenBB平台进行交互。

## 安装
目前，我们支持Python 3.10+。我们将在相对不久的将来增加对更多Python版本的支持。

`openbb-agents`可作为PyPI包获取：

``` sh
pip install openbb-agents --upgrade
```

## 设置
### OpenAI API密钥

要使用OpenBB LLM 智能体代理，您需要一个OpenAI API密钥。请按照以下步骤操作：

1. **获取API密钥**：在[OpenAI](https://www.openai.com/) 注册并获取您的API密钥。
2. **设置环境变量**：将其添加到您的shell配置文件中（`.bashrc`，`.zshrc`等）：
    ```sh
    export OPENAI_API_KEY="your_openai_api_key"
    ```

### OpenBB平台数据提供商凭证
要使用OpenBB平台功能，您需要配置必要的[data provider API凭证](https://docs.openbb.co/platform/usage/api_keys)。 这可以通过以下两种方式之一完成：

1. **本地配置**：在`~/.openbb_platform/user_settings.json`文件中指定您的凭证。按照[本地环境设置指南](https://docs.openbb.co/platform/getting_started/api_keys#local-environment) 了解详细说明。
2. **OpenBB Hub**：通过您的[OpenBB Hub](https://my.openbb.co/)账户创建个人访问令牌（PAT）。然后可以将此PAT作为参数传递给智能体代理。

## 开始使用
强烈建议查看[入门笔记本](https://github.com/awesome-generative-ai/openbb-agents/blob/main/getting_started.ipynb)，它将带您从高层次了解`openbb-agents`的功能。

## 使用方法

``` python
>>> from openbb_agents.agent import openbb_agent
>>> result = openbb_agent("TSLA当前的市值是多少？")  # 将打印一些日志以显示您的进度
>>> print(result)
- TSLA（特斯拉公司）当前的市值大约为695,833,798,800.00美元。
- 这个数字基于2024年1月15日最近可用的数据。
- 市值是通过将当前股价（218.89美元）乘以流通股数量（3,178,920,000股）来计算的。
```

要使用存储在OpenBB Hub中的您的数据提供商凭证，您可以直接将OpenBB Hub PAT传递给智能体代理：

``` python
>>> openbb_agent("AAPL的股价是多少？", openbb_pat="<openbb-hub-pat>")
```

**注意：**智能体代理根据可用的数据提供商凭证动态配置自己。因此，没有适当的API密钥，某些数据源和功能可能无法访问。默认情况下，`yfinance`作为数据提供商被包含在内，不需要API密钥。有关支持的数据提供商的函数的全面列表，请参阅[OpenBB平台文档](https://docs.openbb.co/platform/reference)。

查询可以相对复杂：

```python
>>> openbb_agent("使用最近可用的数据对AMZN进行基本面财务分析。您发现了什么有趣的内容？")
```

查询也可以具有时间依赖性（即，需要之前子问题的答案来回答后面的子问题）：

``` python
>>> openbb_agent("TSLA的同行是谁？它们各自的市值是多少？以市值_降序_返回结果。")
```

智能体代理也提供了`async`版本：

``` python
>>> from openbb_agents.agent import aopenbb_agent
>>> await aopenbb_agent("TSLA当前的市值是多少？")
```

## 开发
- 使用`poetry`创建一个新的虚拟环境
- `poetry install`

### 代码检查和格式化
我们目前正在尝试使用`ruff`作为`black`、`isort`和`pylint`的替代品。

您可以按以下方式运行代码检查：

``` sh
ruff check
```

或修复代码检查错误：

``` sh
ruff check --fix
```

或格式化代码：

``` sh
ruff format
```

我们还在`pre-commit`中包含了这些，如果您希望在提交代码之前自动运行这些检查。您可以按以下方式安装`pre-commit`钩子：

``` sh
pre-commit install
```

### 测试
我们使用`pytest`作为我们的测试运行器：
``` sh
pytest -n 8 tests/
```
