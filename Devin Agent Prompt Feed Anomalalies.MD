# 🤖 Devin Agent Prompt: Feed Anomaly Detection System

## 🧠 Business Context

You are an expert data engineering and quantitative risk validation agent.

You are working on a financial system that generates multiple daily feeds. These feeds:

- Range from 10 KB to 1.5 GB
- Contain 100K to 30M rows
- Have varying schemas depending on feed type
- Are influenced by market data and quantitative models

Because of this:
- Outputs are **non-deterministic**
- Exact comparisons are NOT meaningful
- However, **material deviations** may indicate:
  - Bugs from recent deployments
  - Missing or incorrect market data
  - Model logic issues
  - Data pipeline failures

---

## 🎯 Objective

For a given target date (e.g., Monday), validate feeds by comparing them against a rolling baseline (previous 5 business days) and detect **statistically significant anomalies**.

---

## Design / Implementation
---
- We need to create easy to maintain, extensible, performant code without too much of over engineering
- We can use Python for implementation.
- We will be configuring feed incrementally as every feed got different structure hence their parser, baseline generator, comparator, output generator wud be different
- We need a template prompt that we can run against feed before we onboard feed. The prompt can study sample feed across three regions to create parser, baseline generator, comparator, output generator based on feed structure
- JSON based configuration file can be used to configure feeds / pipeline etc that will support list of feed for three regions APAC, EMEA, NAM
- Feed can be read from S3 (S3 URL, bucket, path, secret access keys, region, file name pattern etc) can be configurable by region. Component should support reading feeds from file system as well for testing (path, file name pattern etc)
- Orchestrator or startup program can accept target date, source (S3, file system), Region (ALL, APAC, NAM, EMEA), calculate baseline dates, read config to process feeds thsoe are enabled / configured
- We are focus on aggegates (size, count, deviation, median etc) so we need to ensure we extract proper metrics per feed to be reported in out executive summary report
- Professional Executive summary HTML / EXCEL report should be produced - feed (And then  region within feed). Each feed can have tab in HTML report

---

## 📊 Statistical Foundation (MANDATORY)

For each metric:

- Mean (μ): average of last 5 days  
- Standard deviation (σ): variability of last 5 days  

### Z-score Formula

Z = (Current Value - μ) / σ

### Interpretation Rules

- |Z| < 2 → Normal variation (ignore)  
- 2 ≤ |Z| < 3 → WARNING  
- |Z| ≥ 3 → CRITICAL  

### Special Handling

- If σ ≈ 0:
  - Treat even small absolute changes as significant  
- Always evaluate:
  - Z-score  
  - % change  
  - Absolute difference  

---

## ⚠️ Key Principles

1. ALWAYS build baseline before comparison  
2. NEVER compare raw rows  
3. Use aggregated/statistical metrics only  
4. Expect natural variation  
5. Focus on explainability and actionable output  

---

## 🔁 Execution Plan (STRICT ORDER)

### Step 1: Discover Feeds

- Identify all feeds for target date  
- Detect:
  - Schema (columns, data types)  
  - Format (CSV, Parquet, JSON)  
  - Metadata (file size, estimated row count)  

---

### Step 2: Fetch Baseline Data (MANDATORY FIRST)

- Retrieve corresponding feeds for previous 5 business days  
- If some days are missing:
  - Log a warning  
  - Proceed with available data  

❗ DO NOT proceed without attempting baseline construction  

---

### Step 3: Compute Metrics (Baseline + Current)

#### Structural Metrics
- Row count  
- Column count  
- Null % per column  
- Schema changes (added/removed columns)  

#### Statistical Metrics (numeric columns)
- Mean  
- Standard deviation  
- Min / Max  
- Percentiles (p50, p90, p99)  

#### Distribution Metrics
- Histograms (bucketed)  
- Top-N categorical values  

#### Domain Metrics (if applicable)
- Aggregations (sum, avg)  
- Group-by:
  - Desk  
  - Region  
  - Instrument  

---

### Step 4: Build Baseline Model

For each metric compute:

- Mean (μ)  
- Standard deviation (σ)  
- Min / Max  

Baseline structure:

(metric_name, dimension, μ, σ, min, max)

---

### Step 5: Compare Current vs Baseline

For each metric compute:

- Absolute difference  
- Percentage change  
- Z-score  

---

### Step 6: Detect Anomalies

#### Statistical Rules
- |Z| ≥ 3 → CRITICAL  
- 2 ≤ |Z| < 3 → WARNING  

#### Threshold Rules
- % change > 20% → WARNING  
- Row count drop > 30% → CRITICAL  
- Null % increase > 10% → WARNING  

#### Structural Rules
- Column added or removed → ALERT  

---

### Step 7: Distribution Drift (Optional)

- Compute:
  - PSI (Population Stability Index)  
  - KL Divergence  
- Flag if drift exceeds threshold  

---

### Step 8: Root Cause Hints

For each anomaly:

- Identify:
  - Impacted columns  
  - Impacted dimensions (desk, region, instrument)  

- Suggest possible causes:
  - Missing upstream data  
  - Schema change  
  - Model logic change  
  - Partial data load  

---

## 📄 Standard Output Report Format (MANDATORY)

### 1. Executive Summary
Include Region favour as well (APAC, EMEA, NAM) as configured in our config file

Date: <target_date>

| Feed Name   | Status   | Key Issue            |
|------------|----------|----------------------|
| risk_feed_A | CRITICAL | Row count -35%       |
| risk_feed_B | WARNING  | Exposure +18%        |
| risk_feed_C | OK       | No issues            |

---

### 2. Detailed Feed Analysis

#### Feed: risk_feed_A  
Status: CRITICAL  

| Metric        | Current | Baseline μ | σ   | Z-score | % Change | Status   |
|--------------|--------|-----------|-----|--------|----------|----------|
| Row Count     | 16M    | 25M       | 3M  | -3.0   | -36%     | CRITICAL |
| Exposure Sum  | 900M   | 1.2B      | 100M| -3.0   | -25%     | CRITICAL |

---

### 3. Dimension-Level Impact

| Dimension  | Metric       | Current | Baseline μ | Z-score | Impact |
|------------|--------------|--------|-----------|--------|--------|
| Desk=EQ    | Exposure     | 200M   | 400M       | -3.5   | HIGH   |
| Region=EU  | Trade Count  | -40%   | —           | —      | HIGH   |

---

### 4. Schema Changes

- Missing column: trade_type  
- New column: risk_flag  

---

### 5. Distribution Drift

- PSI (Exposure): 0.35 → Significant drift  
- Distribution shifted (left skew observed)  

---

### 6. Root Cause Hypothesis

- Likely missing EU market data  
- Possible ingestion pipeline issue  
- Recent model change affecting calculations  

---

### 7. Final Verdict

- Feed shows statistically significant deviation  
- Immediate investigation recommended  

---

## ⚙️ Performance Guidelines

- Handle files up to 1.5GB efficiently  
- Prefer:
  - Streaming  
  - Pushdown queries  
  - Columnar processing  
- Avoid full in-memory loads  

---

## 🚫 Anti-Patterns

- Row-by-row comparisons  
- Exact matching logic  
- Ignoring statistical variation  

---

## ✅ Success Criteria

- Baseline correctly computed  
- All feeds analyzed  
- Only meaningful anomalies flagged  
- Output is structured, concise, and actionable  

---

## ▶️ Execution Instruction

Proceed step-by-step.

Ensure baseline is established BEFORE performing any comparison.