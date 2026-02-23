## Custom Logging with `log_request`

If you need more control — for example, using your own LLM client, a custom provider, or background processing — you can use `log_request` to manually log requests to PromptLayer.

```python
from openai import OpenAI
from promptlayer import PromptLayer
import time

pl_client = PromptLayer()
client = OpenAI()

messages = [
    {"role": "system", "content": "You are an AI."},
    {"role": "user", "content": "Compose a poem please."}
]

request_start_time = time.time()
completion = client.chat.completions.create(
    model="gpt-4o",
    messages=messages
)
request_end_time = time.time()

# Log to PromptLayer
pl_client.log_request(
    provider="openai",
    model="gpt-4o",
    input={"type": "chat", "messages": [
        {"role": m["role"], "content": [{"type": "text", "text": m["content"]}]}
        for m in messages
    ]},
    output={"type": "chat", "messages": [
        {"role": "assistant", "content": [{"type": "text", "text": completion.choices[0].message.content}]}
    ]},
    request_start_time=request_start_time,
    request_end_time=request_end_time,
    tags=["getting-started"]
)
```

This works with any LLM provider, including Anthropic:

```python
import anthropic
from promptlayer import PromptLayer
import time

pl_client = PromptLayer()
client = anthropic.Anthropic()

messages = [{"role": "user", "content": "How many toes do dogs have?"}]

request_start_time = time.time()
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=100,
    messages=messages
)
request_end_time = time.time()

# Log to PromptLayer
pl_client.log_request(
    provider="anthropic",
    model="claude-sonnet-4-20250514",
    input={"type": "chat", "messages": [
        {"role": m["role"], "content": [{"type": "text", "text": m["content"]}]}
        for m in messages
    ]},
    output={"type": "chat", "messages": [
        {"role": "assistant", "content": [{"type": "text", "text": response.content[0].text}]}
    ]},
    request_start_time=request_start_time,
    request_end_time=request_end_time,
    tags=["animal-toes"]
)
```