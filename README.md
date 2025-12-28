# LLM Pipeline Observability

A comprehensive framework for monitoring, observing, and optimizing Large Language Model (LLM) pipelines in production environments. This boilerplate provides enterprise-grade observability solutions for agentic systems and complex LLM workflows.

## Table of Contents

1. [Problem Statement](#problem-statement)
2. [What It Does](#what-it-does)
3. [What It Doesn't Do](#what-it-doesnt-do)
4. [Quick Start Guide](#quick-start-guide)
5. [Repository Structure](#repository-structure)
6. [Key Concepts](#key-concepts)
7. [Development Workflow](#development-workflow)
8. [Integration Points](#integration-points)
9. [Examples](#examples)
10. [Contributing](#contributing)

## Problem Statement

### The Challenge

Large Language Model pipelines present unique observability challenges that traditional monitoring tools struggle to address:

- **Black Box Nature**: LLM outputs are non-deterministic and difficult to validate automatically
- **Complex Workflows**: Agentic systems involve multiple LLM calls, tool interactions, and decision branches
- **Cost Control**: Every token costs money; understanding where resources are consumed is critical
- **Quality Assurance**: How do you measure the quality of LLM outputs in production?
- **Debugging Complexity**: Tracing issues across multiple LLM calls with context windows and token limits
- **Latency Analysis**: Identifying bottlenecks in multi-step LLM orchestrations
- **Compliance & Audit**: Maintaining audit trails for regulated industries
- **Performance Optimization**: Understanding which prompts, models, and approaches work best

### Why This Matters

Without proper observability, LLM systems operate in the dark, making it impossible to:
- Understand user experience degradation
- Identify cost optimization opportunities
- Debug failures in production
- Make data-driven improvements to prompts and workflows
- Maintain compliance requirements

## What It Does

This framework provides:

### Core Observability Features
- **End-to-End Tracing**: Track every LLM call from request to completion with full context
- **Token Accounting**: Monitor token consumption (input, output, cached) per request and aggregated
- **Structured Logging**: JSON-based logging for all pipeline stages with correlation IDs
- **Span-Based Instrumentation**: OpenTelemetry-compatible tracing for distributed workflows
- **Metrics Collection**: Real-time metrics for latency, throughput, error rates, and cost
- **Custom Attributes**: Attach business context, user IDs, feature flags, and custom metadata

### Quality Monitoring
- **Output Validation**: Built-in validators for format, content, and relevance checks
- **Confidence Scoring**: Quantify LLM output confidence and reliability
- **Error Categorization**: Automatic classification of failures (rate limit, invalid format, timeout, etc.)
- **Performance Baselines**: Track performance trends over time

### Integration Capabilities
- **Multiple LLM Providers**: Native support for OpenAI, Anthropic, Cohere, and other providers
- **Tool Integration**: Track tool calls and their outcomes
- **Custom Handlers**: Extensible architecture for adding observability to any LLM interaction
- **Export Options**: Send data to Datadog, New Relic, Grafana, or any OpenTelemetry collector

## What It Doesn't Do

This framework explicitly does NOT:

- **Store Model Weights**: We only observe behavior, not train or fine-tune models
- **Replace API Providers**: Not an LLM service; works alongside existing providers
- **Guarantee Output Quality**: Cannot fix poor prompts or incorrect workflows (you must)
- **Provide Cost Optimization Automatically**: Shows you the data; you make the decisions
- **Handle Authentication**: Assumes you have valid API keys and credentials
- **Provide Real-Time Alerting Engine**: Focuses on data collection; use external tools for alerting
- **Modify LLM Outputs**: Never changes, filters, or manipulates model responses
- **Provide Prompt Engineering**: Not a prompt optimization tool; observes existing prompts

## Quick Start Guide

### Prerequisites
- Python 3.9+
- pip or Poetry
- API credentials for your LLM provider (OpenAI, Anthropic, etc.)

### Installation

```bash
# Clone the repository
git clone https://github.com/jmeiracorbal/agentic-boilerplate.git
cd agentic-boilerplate

# Install dependencies
pip install -e .

# Or with Poetry
poetry install
```

### Basic Usage

```python
from llm_observability import ObservableLLMPipeline, Tracer

# Initialize the tracer
tracer = Tracer(
    provider="openai",
    api_key="sk-...",
    enable_logging=True,
    log_level="INFO"
)

# Create an observable pipeline
pipeline = ObservableLLMPipeline(
    tracer=tracer,
    model="gpt-4",
    temperature=0.7
)

# Use the pipeline with automatic observability
response = pipeline.complete(
    prompt="What is the capital of France?",
    context={"user_id": "user_123", "session": "sess_456"}
)

print(f"Response: {response.text}")
print(f"Tokens used: {response.metrics.total_tokens}")
print(f"Cost: ${response.metrics.estimated_cost}")
```

### Configuration

Create a `.env` file:

```env
# LLM Provider Configuration
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4

# Observability Settings
ENABLE_TRACING=true
ENABLE_LOGGING=true
LOG_LEVEL=INFO

# Export Configuration
EXPORT_TRACER=datadog
DATADOG_API_KEY=...
DATADOG_APP_NAME=llm-pipeline

# Optional: Custom validators and handlers
CUSTOM_VALIDATORS=validators.py
```

### Running Examples

```bash
# Simple completion
python examples/simple_completion.py

# Agent workflow
python examples/agent_workflow.py

# Tool integration
python examples/tool_integration.py

# Advanced: Multi-step pipeline
python examples/multi_step_pipeline.py
```

## Repository Structure

```
agentic-boilerplate/
├── llm_observability/
│   ├── __init__.py
│   ├── core/
│   │   ├── tracer.py              # Core tracer implementation
│   │   ├── pipeline.py            # LLM pipeline orchestration
│   │   ├── span.py                # Span and trace data structures
│   │   └── context.py             # Request context management
│   ├── integrations/
│   │   ├── providers/
│   │   │   ├── openai.py         # OpenAI integration
│   │   │   ├── anthropic.py      # Anthropic (Claude) integration
│   │   │   ├── cohere.py         # Cohere integration
│   │   │   └── base.py           # Base provider interface
│   │   ├── tools/
│   │   │   ├── decorators.py     # Tool instrumentation decorators
│   │   │   └── handlers.py       # Tool execution handlers
│   │   └── exporters/
│   │       ├── datadog.py        # Datadog exporter
│   │       ├── newrelic.py       # New Relic exporter
│   │       ├── otel.py           # OpenTelemetry exporter
│   │       └── console.py        # Console exporter (debugging)
│   ├── validators/
│   │   ├── base.py               # Base validator class
│   │   ├── format.py             # Format validators (JSON, XML, etc.)
│   │   ├── content.py            # Content validators (profanity, relevance)
│   │   └── custom.py             # Custom validation framework
│   ├── metrics/
│   │   ├── collector.py          # Metrics collection engine
│   │   ├── aggregators.py        # Aggregation strategies
│   │   └── reporters.py          # Metrics reporting
│   ├── logging/
│   │   ├── formatters.py         # Custom log formatters
│   │   ├── handlers.py           # Log handlers
│   │   └── filters.py            # Context-aware log filtering
│   └── utils/
│       ├── config.py             # Configuration management
│       ├── timing.py             # Timing utilities
│       └── helpers.py            # Utility functions
├── examples/
│   ├── simple_completion.py      # Basic usage example
│   ├── agent_workflow.py         # Multi-step agent example
│   ├── tool_integration.py       # Tool/function calling
│   └── multi_step_pipeline.py    # Complex orchestration
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── docs/
│   ├── guides/
│   ├── api_reference.md
│   └── troubleshooting.md
├── pyproject.toml
├── requirements.txt
├── .env.example
└── README.md
```

## Key Concepts

### Spans and Traces

A **trace** represents a complete user request through your system. A **span** is a unit of work within that trace.

```python
# Automatic span creation for each LLM call
with tracer.start_span("get_user_profile") as span:
    response = llm.complete("Get user profile")
    span.set_attribute("model", "gpt-4")
    span.set_attribute("user_id", "user_123")
```

### Context Propagation

Maintain context across multiple LLM calls:

```python
context = {
    "user_id": "user_123",
    "session_id": "sess_456",
    "feature_flags": ["new_prompt_v2"],
    "request_id": "req_789"
}

response = pipeline.complete(prompt, context=context)
# Context is automatically included in all traces and logs
```

### Metrics

Track key performance indicators:

```python
# Automatic metrics
- latency_ms: Time to first token and total completion time
- tokens_used: Input, output, and cached token counts
- cost_usd: Estimated cost based on token counts
- error_rate: Percentage of failed requests
- model_usage: Distribution across different models
```

### Validators

Ensure output quality:

```python
pipeline = ObservableLLMPipeline(
    tracer=tracer,
    validators=[
        JSONValidator(),           # Output must be valid JSON
        ProfanityValidator(),       # Filter inappropriate content
        RelevanceValidator(score_threshold=0.7),  # Relevance check
        CustomValidator(my_check_func)
    ]
)
```

## Development Workflow

### Setting Up Development Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install development dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install
```

### Testing

```bash
# Run all tests
pytest

# Run specific test suite
pytest tests/unit/

# Run with coverage
pytest --cov=llm_observability

# Run integration tests (requires API keys)
pytest tests/integration/ --include-integration
```

### Code Style

```bash
# Format code
black llm_observability/

# Lint
flake8 llm_observability/

# Type checking
mypy llm_observability/
```

### Making Changes

1. Create a feature branch: `git checkout -b feature/my-feature`
2. Make your changes with tests
3. Run linting and tests: `pytest && black . && flake8 .`
4. Commit with clear messages: `git commit -m "feat: add new feature"`
5. Push and create a pull request

## Integration Points

### With LLM Providers

```python
# OpenAI
from llm_observability.integrations.providers import OpenAIProvider

provider = OpenAIProvider(api_key="sk-...")
response = provider.complete(prompt="Hello", model="gpt-4")
```

### With Tools/Functions

```python
from llm_observability.integrations.tools import observe_tool

@observe_tool(tracer=tracer, name="get_weather")
def get_weather(location: str) -> str:
    # Your tool implementation
    return f"Weather in {location}"

# Automatically traced in observability system
```

### With External Services

```python
# Export to Datadog
from llm_observability.integrations.exporters import DatadogExporter

exporter = DatadogExporter(api_key="...", site="us3.datadoghq.com")
tracer.add_exporter(exporter)

# All traces automatically sent to Datadog
```

### Custom Integration

```python
from llm_observability.core import BaseExporter

class CustomExporter(BaseExporter):
    def export_trace(self, trace):
        # Send trace to your system
        pass
```

## Examples

### Example 1: Simple Completion

```python
from llm_observability import Tracer, ObservableLLMPipeline

tracer = Tracer(provider="openai", api_key="sk-...")
pipeline = ObservableLLMPipeline(tracer=tracer, model="gpt-4")

response = pipeline.complete(
    prompt="Explain machine learning in 2 sentences"
)

print(f"Response: {response.text}")
print(f"Tokens: {response.metrics.total_tokens}")
print(f"Latency: {response.metrics.latency_ms}ms")
```

### Example 2: Agent with Tools

```python
from llm_observability import Tracer, Agent
from llm_observability.integrations.tools import observe_tool

tracer = Tracer(provider="openai", api_key="sk-...")

@observe_tool(tracer=tracer)
def search_weather(location: str) -> str:
    # Mock weather search
    return f"Sunny in {location}, 72°F"

agent = Agent(
    tracer=tracer,
    model="gpt-4",
    tools=[search_weather]
)

result = agent.run(
    "What's the weather in San Francisco?",
    context={"user_id": "user_123"}
)

print(f"Agent result: {result}")
# Automatically traces all LLM calls and tool executions
```

### Example 3: Multi-Step Pipeline with Validation

```python
from llm_observability import Tracer, ObservableLLMPipeline
from llm_observability.validators import JSONValidator, CustomValidator

def validate_response_length(response):
    """Custom validator: ensure response isn't too long"""
    return len(response) < 1000, "Response too long"

tracer = Tracer(provider="openai", api_key="sk-...")
pipeline = ObservableLLMPipeline(
    tracer=tracer,
    model="gpt-4",
    validators=[
        JSONValidator(),
        CustomValidator(validate_response_length)
    ]
)

# Step 1: Extract user intent
intent_response = pipeline.complete(
    prompt="Extract intent from: 'I need a hotel in New York'"
)

# Step 2: Generate recommendations
recs_response = pipeline.complete(
    prompt=f"Based on intent {intent_response.text}, recommend 3 hotels",
    context={"previous_step": "intent_extraction"}
)

# All steps traced with correlation ID
print(f"Recommendations: {recs_response.text}")
print(f"Quality score: {recs_response.validation.score}")
```

### Example 4: Cost Monitoring

```python
from llm_observability import Tracer, CostMonitor

tracer = Tracer(provider="openai", api_key="sk-...")
monitor = CostMonitor(tracer=tracer, budget_usd=100)

# Run multiple operations
for i in range(50):
    response = tracer.complete(
        prompt=f"Generate creative name {i}",
        model="gpt-4"
    )
    
    # Monitor budget
    if monitor.is_over_budget():
        print(f"Budget exceeded! Used ${monitor.total_cost_usd}")
        break

print(f"Total cost: ${monitor.total_cost_usd}")
print(f"Cost breakdown: {monitor.cost_by_model}")
```

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Areas for Contribution

- Additional LLM provider integrations
- New validators and quality checks
- Performance optimizations
- Documentation and examples
- Bug fixes and testing

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Support

- 📖 [Documentation](docs/)
- 🐛 [Issues](https://github.com/jmeiracorbal/agentic-boilerplate/issues)
- 💬 [Discussions](https://github.com/jmeiracorbal/agentic-boilerplate/discussions)

## Acknowledgments

Built with inspiration from OpenTelemetry, LangChain, and the observability community.

---

**Last Updated**: December 28, 2025
