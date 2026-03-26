=== BASE MODELS ===
             response_time_s  words_per_second  memory_mb  word_count
model                                                                
llama3.2:3b           41.306             3.968      0.009      155.62
phi3.5                92.109             2.787      0.002      231.37

=== QUANTIZED MODELS ===
                                  response_time_s  words_per_second  memory_mb  word_count
model                                                                                     
llama3.2:3b-instruct-q4_K_M                38.253             4.445      0.010      155.62
phi3.5:3.8b-mini-instruct-q5_K_M           37.083             1.924     -0.006      105.63

=== IMPROVEMENT SUMMARY ===

  llama3.2:3b  →  llama3.2:3b-instruct-q4_K_M
    Response time : 41.306s  →  38.253s  (-7.4%  faster)
    Words/second  : 4.0   →  4.4   (+12.0%)
    Memory delta  : 0.01MB →  0.01MB  (+11.1%)

  phi3.5  →  phi3.5:3.8b-mini-instruct-q5_K_M
    Response time : 92.109s  →  37.083s  (-59.7%  faster)
    Words/second  : 2.8   →  1.9   (-31.0%)
    Memory delta  : 0.00MB →  -0.01MB  (-400.0%)
  Skipping mistral — data missing




I benchmarked multiple local LLMs and observed that quantized variants reduced latency significantly, especially for Phi where latency dropped by ~60%. However, I also observed that throughput decreased due to shorter outputs. This highlighted an important tradeoff between latency and token generation efficiency. I also realized the need to normalize output length to make fair comparisons.
Fix(Control output):
"options": {
    "temperature": 0.0,
    "num_predict": 100   # fix output length
}

***********After Fixing output length and benchmarking via FastAPI***********

=== BASE MODELS ===
             response_time_s  words_per_second  memory_mb  word_count
model                                                                
llama3.2:3b           12.590             5.898        0.0        75.8
phi3.5                18.912             4.542        0.0        84.0

=== QUANTIZED MODELS ===
                                  response_time_s  words_per_second  memory_mb  word_count
model                                                                                     
llama3.2:3b-instruct-q4_K_M                12.418             5.992        0.0        75.8
phi3.5:3.8b-mini-instruct-q5_K_M           23.587             3.692        0.0        86.0

=== IMPROVEMENT SUMMARY ===

  llama3.2:3b  →  llama3.2:3b-instruct-q4_K_M
    Response time : 12.590s  →  12.418s  (-1.4%  faster)
    Words/second  : 5.9   →  6.0   (+1.6%)
    Memory delta  : 0.00MB →  0.00MB  (+0.0%)

  phi3.5  →  phi3.5:3.8b-mini-instruct-q5_K_M
    Response time : 18.912s  →  23.587s  (+24.7%  slower)
    Words/second  : 4.5   →  3.7   (-18.7%)
    Memory delta  : 0.00MB →  0.00MB  (+0.0%)
  Skipping mistral — data missing

- Quantization does not always improve performance
- Llama 3.2 showed minimal improvement (~1%)
- Phi 3.5 quantized model was slower on CPU (~25%)
- Performance depends on hardware and model architecture
