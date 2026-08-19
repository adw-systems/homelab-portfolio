### Performance Profiling: CPU-Bound LLM Token Generation Metrics

This benchmarking document logs the operational execution speeds, architectural constraints, and hardware bottlenecks observed during local CPU-only large language model inference. 

---

### 1. Hardware Bottleneck Analysis (Compute vs. Memory Bandwidth)

While the host Intel i5-11500 processor provides 12 execution threads capable of handling advanced AVX2 matrix math routines, steady-state token generation speeds are strictly bound by consumer DDR4 memory channel bandwidth limitations . 

### Operational Mechanics

- **Token Generation (Eval Rate):** Requires streaming multi-gigabyte neural network tensor layers sequentially out of volatile RAM into processor caches for every individual token generated. 
    
- **Throughput Delta:** Dedicated graphics hardware (VRAM) passes data at bandwidths exceeding 400 GB/s, whereas a dual-channel desktop memory framework maxes out near 40–50 GB/s. Consequently, execution speeds scale directly with model file parameter sizing. 
    
- **Cache vs. Bus Saturation:** For highly condensed architectures (e.g., 1.5B parameters), model layers fit deeply within direct CPU cache structures, bypassing system RAM bottlenecks. Larger weights (e.g., 8B parameters) saturate memory buses completely, shifting the processing curve to raw RAM transit limits. 
    

---

### 2. Consolidated Empirical Benchmark Matrix

The following baseline metrics were captured across isolated container parameters utilizing strict physical core segmentation (`OLLAMA_NUM_THREADS=6`) and memory stream constraints (`OLLAMA_NUM_PARALLEL=1`): 

|Model Profile & Architecture|File Scale|Quantization Tier|Output Generation Speed (Eval Rate)|Targeted Real-World UX Use Case|
|---|---|---|---|---|
|**`deepseek-r1:1.5b`**|1.5 Billion|Q4_K_M (4-bit)|**36.68 tokens/sec**|**Real-Time Interactive Utility:** Lightning-fast coding shortcuts, quick logical queries, and zero-latency chat responses.|
|**`llama3.2:latest`**|3.2 Billion|Q4_K_M (4-bit)|**17.35 tokens/sec**|**Standard Personal Assistant:** Balanced everyday text writing, summarizing documents, and light development tasks.|
|**`llama3.1:8b`**|8.0 Billion|Q4_K_M (4-bit)|**8.05 tokens/sec**|**Deep Text Context Processing:** Complex coding logic blocks and deeply descriptive text generation tracks.|
|**`deepseek-r1:8b`**|8.0 Billion|Q4_K_M (4-bit)|**5.48 tokens/sec**|**Advanced Batch Reasoning:** Deep multi-minute Chain-of-Thought math, logic, and architectural analysis runs.|

---

### 3. Subsystem Metrics Verification Protocol

To capture execution duration details natively for portfolio indexing, trigger the backend API verbose monitoring parameters through the terminal console shell interface: 

bash

```
# Execute native engine logging directly across a target model layer
ollama run llama3.2 --verbose
```

Use code with caution.

### Telemetry Logs Signature

Upon response finalization, the tracking layer outputs clear system timing summaries: 

- `prompt eval rate`: Speed at which the processing core reads and hashes the incoming prompt context block. 
    
- `eval rate`: Steady-state output token production velocity per second. 
    

---

### 4. Empirical Performance Log Verification Traces

### Trace Array A: High-Velocity Reasoning (`deepseek-r1:1.5b`)

text

```
total duration:       8.259360347s
load duration:        133.220386ms
prompt eval count:    34 token(s)
prompt eval duration: 134.89ms
prompt eval rate:     252.06 tokens/s
eval count:           293 token(s)
eval duration:        7.988361s
eval rate:            36.68 tokens/s
```

Use code with caution.

### Trace Array B: Core Standard Baseline (`llama3.2:3b`)

text

```
total duration:       47.516590122s
load duration:        169.514976ms
prompt eval count:    47 token(s)
prompt eval duration: 424.435ms
prompt eval rate:     110.74 tokens/s
eval count:           814 token(s)
eval duration:        46.912637s
eval rate:            17.35 tokens/s
```

Use code with caution.

### Trace Array C: Memory-Saturated Standard Profile (`llama3.1:8b`)

text

```
total duration:       1m1.905696623s
load duration:        168.02964ms
prompt eval count:    25 token(s)
prompt eval duration: 609.831ms
prompt eval rate:     40.99 tokens/s
eval count:           492 token(s)
eval duration:        1m1.12609s
eval rate:            8.05 tokens/s
```

Use code with caution.

### Trace Array D: Continuous Multi-Minute Reasoning Loop (`deepseek-r1:8b`)

text

```
total duration:       5m23.931535192s
load duration:        134.812621ms
prompt eval count:    36 token(s)
prompt eval duration: 706.295ms
prompt eval rate:     50.97 tokens/s
eval count:           1771 token(s)
eval duration:        5m23.076179s
eval rate:            5.48 tokens/s
```

Use code with caution.