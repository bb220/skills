## Error Handling

PromptLayer provides robust error handling with specialized exception classes and configurable error behavior.

### Exception Classes

The library includes specific exception types following industry best practices:

```python
from promptlayer import (
    PromptLayerAPIError,              # General API errors
    PromptLayerBadRequestError,       # 400 errors
    PromptLayerAuthenticationError,   # 401 errors
    PromptLayerNotFoundError,         # 404 errors
    PromptLayerValidationError,       # Input validation errors
    PromptLayerAPIConnectionError,    # Connection failures
    PromptLayerAPITimeoutError,       # Timeout errors
    PromptLayerRateLimitError,        # 429 rate limit errors
)
```

### Using `throw_on_error`

By default, PromptLayer throws exceptions when errors occur. You can control this behavior using the `throw_on_error` parameter:

```python
from promptlayer import PromptLayer

# Default behavior: throws exceptions on errors
promptlayer_client = PromptLayer(api_key="pl_****", throw_on_error=True)

# Alternative: logs warnings instead of throwing exceptions
promptlayer_client = PromptLayer(api_key="pl_****", throw_on_error=False)
```

**Example with exception handling:**

```python
from promptlayer import PromptLayer, PromptLayerNotFoundError, PromptLayerValidationError

promptlayer_client = PromptLayer()

try:
    # Attempt to get a template that might not exist
    template = promptlayer_client.templates.get("NonExistentTemplate")
except PromptLayerNotFoundError as e:
    print(f"Template not found: {e}")
except PromptLayerValidationError as e:
    print(f"Invalid input: {e}")
```

**Example with warnings (`throw_on_error=False`):**

```python
from promptlayer import PromptLayer

# Initialize with throw_on_error=False to get warnings instead of exceptions
promptlayer_client = PromptLayer(throw_on_error=False)

# This will log a warning instead of throwing an exception if the template doesn't exist
template = promptlayer_client.templates.get("NonExistentTemplate")
# Returns None if not found, with a warning logged
```

### Automatic Retry Mechanism

PromptLayer includes a built-in retry mechanism to handle transient failures gracefully. This ensures your application remains resilient when temporary issues occur.

**Retry Behavior:**
- **Total Attempts**: 4 attempts (1 initial + 3 retries)
- **Exponential Backoff**: Retries wait progressively longer between attempts (2s, 4s, 8s)
- **Max Wait Time**: 15 seconds maximum wait between retries

**What Triggers Retries:**
- **5xx Server Errors**: Internal server errors, service unavailable, etc.
- **429 Rate Limit Errors**: When rate limits are exceeded

**What Fails Immediately (No Retries):**
- **Connection Errors**: Network connectivity issues
- **Timeout Errors**: Request timeouts
- **4xx Client Errors** (except 429): Bad requests, authentication errors, not found, etc.

The retry mechanism operates transparently in the background — you don't need to implement retry logic yourself.

### Logging

PromptLayer uses Python's built-in `logging` module for all log output:

```python
import logging
from promptlayer import PromptLayer

# Configure logging to see PromptLayer logs
logging.basicConfig(level=logging.INFO)

promptlayer_client = PromptLayer()
```

**Setting log levels:**

```python
import logging

# Get the PromptLayer logger
logger = logging.getLogger("promptlayer")

# Set to WARNING to only see warnings and errors
logger.setLevel(logging.WARNING)

# Set to DEBUG to see detailed information
logger.setLevel(logging.DEBUG)
```

**Viewing Retry Logs:**

When retries occur, PromptLayer logs warnings before each retry attempt:

```python
import logging
from promptlayer import PromptLayer

logging.basicConfig(
    level=logging.WARNING,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

promptlayer_client = PromptLayer()

# If a retry occurs, you'll see log messages like:
# "Retrying in 2 seconds..."
# "Retrying in 4 seconds..."
```