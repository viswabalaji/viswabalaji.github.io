+++
date = '2026-02-07T12:40:23-08:00'
draft = false
title = 'Welcome to My Technical Blog'
tags = ['meta', 'introduction']
+++

## Hello World

Welcome to my technical blog where I explore ideas at the intersection of philosophy, computer science, AI, and biology.

## What to Expect

I'll be writing about:

- **Philosophy**: Exploring fundamental questions and their practical implications
- **Computer Science**: Deep dives into algorithms, systems, and software engineering
- **Artificial Intelligence**: Machine learning, neural networks, and AI safety
- **Biology**: Computational biology, bioinformatics, and the nature of life

## Example Code

Here's a simple example of syntax highlighting:

```python
def fibonacci(n):
    """Calculate the nth Fibonacci number."""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# With memoization for efficiency
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci_fast(n):
    if n <= 1:
        return n
    return fibonacci_fast(n-1) + fibonacci_fast(n-2)
```

Stay tuned for more posts!
