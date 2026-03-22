I benchmarked multiple local LLMs and observed that quantized variants reduced latency significantly, especially for Phi where latency dropped by ~60%. However, I also observed that throughput decreased due to shorter outputs. This highlighted an important tradeoff between latency and token generation efficiency. I also realized the need to normalize output length to make fair comparisons.
Fix(Control output):
"options": {
    "temperature": 0.0,
    "num_predict": 100   # fix output length
}
