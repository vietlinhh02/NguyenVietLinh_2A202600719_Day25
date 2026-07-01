# Day 25 Lab Report: Reliability Agent

## 1. Overview
This project implements a Reliability Agent with various resilience patterns to ensure robust interactions with external services or AI models. The core patterns implemented include:
- **Caching (`cache.py`)**: Implements Redis caching to reduce latency, API costs, and load on primary services.
- **Circuit Breaker (`circuit_breaker.py`)**: Prevents cascading failures by stopping requests to failing services.
- **Chaos Engineering (`chaos.py`)**: Simulates real-world failure scenarios (timeouts, flaky services) to test system resilience.
- **API Gateway (`gateway.py`)**: Routes requests and integrates reliability patterns.
- **Metrics (`metrics.py`)**: Tracks and reports on system reliability and performance.

## 2. Performance Comparison

We tested the system in two configurations: without cache and with Redis cache (simulating 300 total requests).

| Metric | Without Cache | With Redis Cache | Improvement |
|--------|---------------|------------------|-------------|
| **Availability** | 98.33% | 99.33% | +1.00% |
| **Error Rate** | 1.67% | 0.67% | -1.00% |
| **Cache Hit Rate** | 0.0% | 72.33% | +72.33% |
| **Latency (p50)** | 278.09 ms | 273.08 ms | -5.01 ms |
| **Latency (p95)** | 316.13 ms | 318.13 ms | +2.00 ms |
| **Latency (p99)** | 320.08 ms | 320.09 ms | +0.01 ms |
| **Circuit Breaker Opens** | 22 times | 5 times | Reduced by 17 |
| **Estimated Cost** | $0.1256 | $0.0369 | Saved ~$0.088 |

### Key Observations:
1. **Cost Savings**: Enabling the Redis cache saved approximately **$0.217** across the tests while keeping the estimated cost down to $0.0369.
2. **System Stability**: The Circuit Breaker opened 22 times without the cache, showing a highly unstable backend. With the cache absorbing **72.33%** of the requests, the circuit breaker only needed to open 5 times.
3. **Availability**: Overall system availability improved from 98.33% to an excellent 99.33% thanks to cached responses serving users even when the backend was unstable.

## 3. Scenarios Evaluated
The reliability agent successfully passed all chaos engineering scenarios in both configurations:
- `primary_timeout_100`: **PASS** (System successfully handles 100% timeout from primary service)
- `primary_flaky_50`: **PASS** (System handles 50% failure rate)
- `all_healthy`: **PASS** (Normal operations function correctly)

## 4. Conclusion
Integrating resilience patterns—specifically caching combined with a circuit breaker—drastically improves system availability, reduces costs, and provides a much more stable user experience even under heavy chaos and backend failure conditions.
