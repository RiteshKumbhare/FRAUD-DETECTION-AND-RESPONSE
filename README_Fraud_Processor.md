# Fraud Stream Processor

A full-featured **real-time fraud monitoring and alert system** with enrichment, sliding-window features, network analysis, anomaly scoring, dynamic blacklisting, and RAG + LLM-assisted SAR drafting. This pipeline is designed for asynchronous streaming, and can run locally or in cloud environments.

---

## Features

1. **Async Streaming Ingestion**
   - Simulated transaction stream with configurable events per second.
   - Supports multiple transaction types (`card_payment`, `transfer`, `crypto_withdrawal`).

2. **Transaction Enrichment**
   - Enriches transactions with user profiles, geo-location, and risk scores.
   - Checks against a dynamic in-memory blacklist for IP, card, or device.

3. **Sliding-Window Feature Computation**
   - Per-event incremental updates.
   - Features include `avg_amount_1m`, `velocity_1m`, and `distinct_ips_1m`.

4. **Network Graph Analysis**
   - Built using NetworkX.
   - Computes small ring/graph degree to detect suspicious clusters.

5. **Anomaly Scoring**
   - Z-score based anomaly detection on transaction amounts.
   - Scores normalized between 0 and 1.

6. **Alerting**
   - Async HTTP webhook delivery.
   - Console fallback if no webhook is configured.

7. **Dynamic Blacklist**
   - Automatically updates local blacklist on high-risk transactions.
   - Supports IP, device, and card blocking.

8. **RAG (Retrieval-Augmented Generation)**
   - Uses FAISS for vector-based storage of past transactions.
   - HuggingFace embeddings to encode documents.

9. **LLM-Assisted SAR Drafting**
   - LangChain integration for drafting Suspicious Activity Reports.
   - Leverages HuggingFace LLM pipelines (e.g., Vicuna, GPT-style models).

---

## Requirements

- Python ≥ 3.9
- Packages:

```bash
pip install aiohttp pandas networkx transformers langchain faiss-cpu nest_asyncio torch sentence-transformers
```

> Adjust `faiss-cpu` to `faiss-gpu` if using GPU acceleration.

---

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `STREAM_RATE_PER_SEC` | Number of simulated transactions per second | 5 |
| `WINDOW_SECONDS` | Sliding window length for features | 60 |
| `BLOCK_CONFIDENCE_THRESHOLD` | Threshold for auto-blocking transactions | 0.8 |
| `ALERT_WEBHOOK` | Webhook URL for async alerts | None (prints to console) |
| `BLACKLIST_REFRESH_URL` | Optional URL to refresh blacklist dynamically | None |
| `BLACKLIST_REFRESH_INTERVAL` | Interval (seconds) for refreshing blacklist | 60 |
| `LLM_MODEL` | HuggingFace LLM for SAR generation | `TheBloke/vicuna-7B-1.1-HF` |

---

## Usage

```bash
python fraud_processor.py
```

The processor runs a **simulated async transaction stream** and continuously:

1. Enriches incoming transactions.
2. Computes features and anomaly scores.
3. Checks for blacklist hits and rule violations.
4. Generates alerts via webhook or console.
5. Updates the local blacklist dynamically.
6. Adds events to RAG vector store.
7. Generates SAR reports using an LLM.

---

### Jupyter / Colab Notes

For running in Jupyter notebooks or Colab:

```python
import nest_asyncio
nest_asyncio.apply()
```

Then run:

```python
await main()
```

---

## Architecture

```
+--------------------+
| Transaction Stream |
+---------+----------+
          |
          v
+--------------------+
| Fraud Processor    |
| - Enrichment       |
| - Sliding Window   |
| - Feature Compute  |
| - Graph Analysis   |
| - Anomaly Scoring  |
+---------+----------+
          |
          v
+--------------------+       +---------------------+
| Alert System       |<----->| Blacklist           |
| - Async Webhook    |       | - Dynamic Updates   |
| - Console fallback |       +---------------------+
+--------------------+
          |
          v
+--------------------+
| RAG + LLM SAR      |
| - FAISS            |
| - HuggingFace LLM  |
| - LangChain        |
+--------------------+
```

---

## Extending the System

- **Add new transaction types** by modifying the `transaction_stream`.
- **Plug in real-time data sources** (Kafka, AWS Kinesis) instead of simulated stream.
- **Integrate additional LLM models** for SAR generation.
- **Use GPU FAISS + LLM** for better performance.
- **Enhance anomaly detection** with ML models or online learning algorithms.

---

## License

MIT License — free for academic and commercial use.  

---

## Authors

- Ritesh Kumbhare
