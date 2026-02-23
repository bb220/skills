# Python

[install PromptLayer using](https://pypi.org/project/promptlayer/) `pip`:

```bash
pip install promptlayer
```

PromptLayer's Python library has support for OpenAI, Anthropic, and other LLM providers! Set up a PromptLayer client in your Python file:

```python
from promptlayer import PromptLayer
promptlayer_client = PromptLayer()
```

Optionally, you can specify the API key and base URL in the client:

```python
promptlayer_client = PromptLayer(api_key="pl_****", base_url="https://api.promptlayer.com")
```