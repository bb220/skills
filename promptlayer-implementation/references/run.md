
## Using the `run` Method (Recommended)

The easiest way to use PromptLayer is with the `run()` method. It fetches a prompt template from the Prompt Registry, executes it against your configured LLM provider, and logs the result — all in one call.

```python
from promptlayer import PromptLayer
promptlayer_client = PromptLayer()

response = promptlayer_client.run(
    prompt_name="my-prompt",
    input_variables={"topic": "poetry"},
    tags=["getting-started"],
    metadata={"user_id": "123"}
)

print(response["prompt_blueprint"]["prompt_template"]["messages"][-1]["content"])
```

> Your LLM API keys (OpenAI, Anthropic, etc.) are **never** sent to our servers. All LLM requests are made locally from your machine — PromptLayer just logs the request.

The `run()` method works with any provider configured in your prompt template — OpenAI, Anthropic, Google, and more. After making your first few requests, you should be able to see them in the PromptLayer dashboard!