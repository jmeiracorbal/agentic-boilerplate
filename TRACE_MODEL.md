# Model Tracing and Debugging Guide

## Overview

This document provides comprehensive guidance for tracing and debugging model execution in the agentic boilerplate framework. It includes tools, methodologies, and best practices for understanding model behavior, identifying bottlenecks, and troubleshooting issues.

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Tracing Architecture](#tracing-architecture)
3. [Debugging Tools](#debugging-tools)
4. [Instrumentation](#instrumentation)
5. [Log Analysis](#log-analysis)
6. [Performance Profiling](#performance-profiling)
7. [Common Issues and Solutions](#common-issues-and-solutions)
8. [Best Practices](#best-practices)
9. [Advanced Debugging](#advanced-debugging)

---

## Quick Start

### Enable Tracing

```python
import logging
from agentic_boilerplate.tracing import enable_tracing

# Enable all tracing
enable_tracing(level=logging.DEBUG)

# Run your model
result = model.run(input_data)
```

### Basic Logging Setup

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('model_trace.log'),
        logging.StreamHandler()
    ]
)
```

---

## Tracing Architecture

### Components

#### 1. **Trace Collector**
Centralized component for collecting execution traces.

```python
from agentic_boilerplate.tracing import TraceCollector

collector = TraceCollector()
collector.start()

# Your code here

trace_data = collector.get_trace()
collector.save_trace('trace.json')
```

#### 2. **Span System**
Hierarchical tracking of execution blocks.

```python
from agentic_boilerplate.tracing import create_span

with create_span("model_inference") as span:
    span.set_attribute("model_name", "gpt-4")
    span.set_attribute("input_size", len(input_data))
    result = model.predict(input_data)
    span.set_attribute("output_size", len(result))
```

#### 3. **Event Logging**
Fine-grained event tracking.

```python
from agentic_boilerplate.tracing import log_event

log_event("model_loaded", {
    "model_type": "transformer",
    "parameters": 7e9,
    "device": "cuda:0"
})
```

---

## Debugging Tools

### 1. Interactive Debugger

```python
from agentic_boilerplate.debugging import ModelDebugger

debugger = ModelDebugger(model)
debugger.breakpoint_on_error()

# Run with debugging
result = debugger.run(input_data, step_through=True)
```

### 2. Trace Visualizer

```python
from agentic_boilerplate.tracing import visualize_trace

trace_data = collector.get_trace()
visualize_trace(trace_data, output_file='trace_visualization.html')
```

### 3. Profiler Integration

```python
from agentic_boilerplate.profiling import ModelProfiler

profiler = ModelProfiler(model)
profiler.start()

result = model.predict(input_data)

profiler.stop()
profiler.generate_report('profile_report.html')
```

---

## Instrumentation

### Adding Custom Spans

```python
from agentic_boilerplate.tracing import create_span

def custom_processing(data):
    with create_span("data_preprocessing") as span:
        span.set_attribute("data_type", type(data).__name__)
        span.set_attribute("data_size", len(data))
        
        # Processing code
        processed_data = preprocess(data)
        
        span.set_attribute("processed_size", len(processed_data))
        return processed_data
```

### Adding Metrics

```python
from agentic_boilerplate.metrics import record_metric

record_metric("model_latency", latency_ms, tags={"model": "gpt-4"})
record_metric("token_count", token_count, tags={"model": "gpt-4"})
record_metric("error_rate", error_rate, tags={"model": "gpt-4"})
```

### Custom Instrumentation Points

```python
from agentic_boilerplate.tracing import instrument

@instrument(name="prediction_pipeline")
def predict_with_tracing(model, input_data):
    """Automatically traced prediction function."""
    return model.predict(input_data)
```

---

## Log Analysis

### Structured Logging

```python
import structlog

logger = structlog.get_logger()

logger.info(
    "model_execution",
    model_name="gpt-4",
    input_tokens=150,
    output_tokens=200,
    latency_ms=1250,
    status="success"
)
```

### Log Filtering and Searching

```python
from agentic_boilerplate.logging import LogAnalyzer

analyzer = LogAnalyzer('model_trace.log')

# Find all errors
errors = analyzer.filter_by_level('ERROR')

# Find slow operations
slow_ops = analyzer.filter_by_duration(min_ms=1000)

# Search by pattern
api_calls = analyzer.search_pattern(r'api_.*_call')
```

### Log Aggregation

```python
from agentic_boilerplate.logging import LogAggregator

aggregator = LogAggregator()
aggregator.add_log_file('model_trace_1.log')
aggregator.add_log_file('model_trace_2.log')
aggregator.add_log_file('model_trace_3.log')

summary = aggregator.generate_summary()
print(summary)
```

---

## Performance Profiling

### CPU Profiling

```python
from agentic_boilerplate.profiling import cpu_profile

@cpu_profile
def slow_function(data):
    # Function will be CPU profiled
    return process(data)

result = slow_function(input_data)
```

### Memory Profiling

```python
from agentic_boilerplate.profiling import memory_profile

@memory_profile
def memory_intensive_function(data):
    # Function will be memory profiled
    return process_large_dataset(data)

result = memory_intensive_function(input_data)
```

### GPU Profiling

```python
from agentic_boilerplate.profiling import gpu_profile

@gpu_profile
def gpu_intensive_function(data):
    # Function will be GPU profiled
    return model.forward(data)

result = gpu_intensive_function(input_data)
```

### Custom Profiling Context

```python
from agentic_boilerplate.profiling import ProfileContext

with ProfileContext("inference_pipeline") as ctx:
    # Code to be profiled
    result = model.predict(input_data)
    
# Get profiling results
print(ctx.get_statistics())
```

---

## Common Issues and Solutions

### Issue 1: Model Runs Too Slowly

**Diagnosis Steps:**
1. Enable performance profiling
2. Check GPU/CPU utilization
3. Analyze token generation rate

```python
from agentic_boilerplate.profiling import ModelProfiler

profiler = ModelProfiler(model)
profiler.start()

result = model.predict(input_data)

profiler.stop()
stats = profiler.get_statistics()

print(f"Total latency: {stats['total_latency_ms']}ms")
print(f"GPU utilization: {stats['gpu_utilization']}%")
print(f"Token generation rate: {stats['tokens_per_second']}")
```

**Solutions:**
- Reduce batch size
- Enable model quantization
- Use smaller model variant
- Optimize data preprocessing

### Issue 2: Out of Memory Errors

**Diagnosis Steps:**
1. Profile memory usage
2. Check for memory leaks
3. Monitor batch size impact

```python
from agentic_boilerplate.debugging import MemoryDebugger

debugger = MemoryDebugger()
debugger.start_monitoring()

# Your code here
result = model.predict(input_data)

debugger.stop_monitoring()
debugger.print_memory_report()
```

**Solutions:**
- Reduce batch size
- Enable gradient checkpointing
- Use mixed precision training
- Implement memory-efficient attention

### Issue 3: Incorrect Model Outputs

**Diagnosis Steps:**
1. Enable input/output logging
2. Verify tokenization
3. Check model state

```python
from agentic_boilerplate.debugging import IODebugger

io_debug = IODebugger(model)
io_debug.log_inputs = True
io_debug.log_outputs = True

result = io_debug.predict(input_data)
io_debug.print_io_trace()
```

**Solutions:**
- Verify input preprocessing
- Check model weights
- Validate tokenizer
- Review prompt format

### Issue 4: High API Costs

**Diagnosis Steps:**
1. Track token usage
2. Monitor API calls
3. Analyze cost per operation

```python
from agentic_boilerplate.cost_tracking import CostTracker

tracker = CostTracker(model_pricing={
    "gpt-4": {"input": 0.03, "output": 0.06}
})

tracker.start()

result = model.predict(input_data)

tracker.stop()
print(tracker.get_cost_report())
```

**Solutions:**
- Implement caching
- Reduce token usage
- Use cheaper models
- Batch requests

---

## Best Practices

### 1. **Comprehensive Logging**

```python
import logging
from functools import wraps

def logged_function(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        logger = logging.getLogger(__name__)
        logger.debug(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        
        try:
            result = func(*args, **kwargs)
            logger.debug(f"{func.__name__} returned successfully")
            return result
        except Exception as e:
            logger.error(f"{func.__name__} failed with error: {e}", exc_info=True)
            raise
    
    return wrapper

@logged_function
def your_function(data):
    return process(data)
```

### 2. **Structured Context Management**

```python
from contextlib import contextmanager

@contextmanager
def execution_context(operation_name, **metadata):
    """Manage execution context with proper cleanup."""
    logger.info(f"Starting {operation_name}", **metadata)
    start_time = time.time()
    
    try:
        yield
    finally:
        elapsed = time.time() - start_time
        logger.info(f"Completed {operation_name}", 
                   elapsed_seconds=elapsed, **metadata)
```

### 3. **Error Handling and Reporting**

```python
from agentic_boilerplate.error_handling import ErrorReporter

reporter = ErrorReporter()

try:
    result = model.predict(input_data)
except Exception as e:
    reporter.report(e, context={
        "input_size": len(input_data),
        "model_name": model.name,
        "operation": "prediction"
    })
```

### 4. **Performance Monitoring**

```python
from agentic_boilerplate.monitoring import PerformanceMonitor

monitor = PerformanceMonitor()

@monitor.track_performance
def critical_operation(data):
    return process_data(data)
```

---

## Advanced Debugging

### 1. Remote Debugging

```python
from agentic_boilerplate.debugging import RemoteDebugger

debugger = RemoteDebugger(
    host="localhost",
    port=5678
)
debugger.start()

# Attach external debugger at breakpoints
debugger.set_breakpoint("model.predict")
```

### 2. Time-Travel Debugging

```python
from agentic_boilerplate.debugging import TimeTravel

time_travel = TimeTravel()
time_travel.record_execution(model.predict, input_data)

# Replay execution
time_travel.replay(target_step=5)
time_travel.inspect_state()
```

### 3. Distributed Tracing

```python
from agentic_boilerplate.tracing import DistributedTracer

tracer = DistributedTracer(service_name="model_service")

with tracer.trace("distributed_operation") as span:
    # Trace will be visible across multiple services
    result = call_external_service()
```

### 4. Custom Trace Exporters

```python
from agentic_boilerplate.tracing import TraceExporter

class CustomExporter(TraceExporter):
    def export(self, trace_data):
        # Custom export logic
        send_to_monitoring_system(trace_data)

collector.add_exporter(CustomExporter())
```

---

## Configuration

### Environment Variables

```bash
# Enable trace collection
export ENABLE_TRACING=true

# Set trace level (DEBUG, INFO, WARNING, ERROR)
export TRACE_LEVEL=DEBUG

# Trace output directory
export TRACE_OUTPUT_DIR=/path/to/traces

# Enable profiling
export ENABLE_PROFILING=true

# Sample rate (0.0 to 1.0)
export TRACE_SAMPLE_RATE=1.0
```

### Configuration File

```yaml
tracing:
  enabled: true
  level: DEBUG
  output_dir: ./traces
  
profiling:
  enabled: true
  cpu_profiling: true
  memory_profiling: true
  gpu_profiling: true
  
logging:
  format: structured
  aggregation: enabled
  
sampling:
  enabled: true
  rate: 0.1
```

---

## Resources and Further Reading

- [Python Logging Documentation](https://docs.python.org/3/library/logging.html)
- [OpenTelemetry Python](https://opentelemetry.io/docs/instrumentation/python/)
- [cProfile and pstats](https://docs.python.org/3/library/profile.html)
- [NVIDIA Profiling Tools](https://developer.nvidia.com/tools-overview)

---

## Contributing

To add new debugging tools or improve tracing:

1. Create new module in `agentic_boilerplate/debugging/`
2. Follow existing instrumentation patterns
3. Add tests in `tests/debugging/`
4. Update this documentation

---

## Support

For issues or questions regarding model tracing and debugging:

- Create an issue in the repository
- Consult the troubleshooting section
- Review example scripts in `examples/debugging/`

---

**Last Updated:** 2025-12-27
**Version:** 1.0
