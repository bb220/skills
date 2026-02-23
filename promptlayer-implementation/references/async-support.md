
## Async Support

PromptLayer supports asynchronous operations, ideal for managing concurrent tasks in non-blocking environments like web servers, microservices, or Jupyter notebooks.

### Initializing the Async Client

To use asynchronous non-blocking methods, initialize `AsyncPromptLayer`:

```python
from promptlayer import AsyncPromptLayer

# Initialize an asynchronous client with your API key
async_promptlayer_client = AsyncPromptLayer(api_key="pl_****")
```

### Async Usage Examples

The asynchronous client functions similarly to the synchronous version, but allows for non-blocking execution with `asyncio`.

#### Example 1: Async Template Management

```python
import asyncio
from promptlayer import AsyncPromptLayer

async def main():
    async_promptlayer_client = AsyncPromptLayer(api_key="pl_****")

    # Fetch a template asynchronously
    template = await async_promptlayer_client.templates.get("Test1")
    print(template)

    # Fetch all templates asynchronously
    templates = await async_promptlayer_client.templates.all()
    print(templates)

asyncio.run(main())
```

#### Example 2: Async Agent Execution

```python
import asyncio
from promptlayer import AsyncPromptLayer

async def main():
    async_promptlayer_client = AsyncPromptLayer(api_key="pl_****")

    response = await async_promptlayer_client.run_workflow(
        workflow_name="example_agent",
        workflow_version=1,
        input_variables={"num1": "1", "num2": "2"},
        return_all_outputs=True,
    )
    print(response)

asyncio.run(main())
```

#### Example 3: Async Tracking and Logging

```python
import asyncio
from promptlayer import AsyncPromptLayer

async def main():
    async_promptlayer_client = AsyncPromptLayer(api_key="pl_****")

    # Track metadata asynchronously
    request_id = "pl_request_id_example"
    await async_promptlayer_client.track.metadata(request_id, {"key": "value"})

    # Log request asynchronously
    await async_promptlayer_client.log_request(
        provider="openai",
        model="gpt-3.5-turbo",
        input=prompt_template,
        output=output_template,
        request_start_time=1630945600,
        request_end_time=1630945605,
    )

asyncio.run(main())
```

#### Example 4: Asynchronous Prompt Execution with `run` Method

```python
import asyncio
from promptlayer import AsyncPromptLayer

async def main():
    async_promptlayer_client = AsyncPromptLayer(api_key="pl_****")

    # Execute a prompt template asynchronously
    response = await async_promptlayer_client.run(
        prompt_name="TestPrompt",
        input_variables={"variable1": "value1", "variable2": "value2"}
    )
    print(response)

asyncio.run(main())
```

#### Example 5: Asynchronous Streaming Prompt Execution with `run` Method

```python
import asyncio
import os
from promptlayer import AsyncPromptLayer

async def main():
    async_promptlayer_client = AsyncPromptLayer(
        api_key=os.environ.get("PROMPTLAYER_API_KEY")
    )

    response_generator = await async_promptlayer_client.run(
        prompt_name="TestPrompt",
        input_variables={"variable1": "value1", "variable2": "value2"},
        stream=True
    )

    final_response = ""
    async for response in response_generator:
        # Access raw streaming response
        print("Raw streaming response:", response["raw_response"])
        
        # Access progressively built prompt blueprint
        if response["prompt_blueprint"]:
            current_response = response["prompt_blueprint"]["prompt_template"]["messages"][-1]
            if current_response.get("content"):
                print(f"Current response: {current_response['content']}")

asyncio.run(main())
```

When streaming is enabled, each chunk includes both the raw streaming response and the progressively built `prompt_blueprint`, allowing you to track how the response is constructed in real-time.