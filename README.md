# Ghost Data Audit Agent - Northstar Shop

An AI-powered auditing system that detects ghost data, active sync risks, and unverified data deletions across multi-system environments, generating strict remediation and verification steps.

## System Architecture

The solution uses a 4-stage pipeline:
1. **Data Models & Configuration (`code/models.py`, `code/config.py`)**: Defines Pydantic schemas for strict JSON serialization and standard status categories (`COMPLETE`, `INCOMPLETE`, `NOT_APPLICABLE`).
2. **Identifier Resolution (`code/core/identifier_resolver.py`)**: Recursively resolves primary and secondary user identifiers across hashed/unhashed email, phone, and user IDs.
3. **Ghost Detection & Rule Engine (`code/core/ghost_detector.py`, `code/core/rule_engine.py`)**: Scans databases, log stores, backup archives, and search indexes for active sync risks or lingering tombstones. Enforces deletion rules to flag missing steps.
4. **Execution & Evaluation (`code/main.py`, `code/evaluation/evaluate.py`)**: Processes all 518 cases, outputs formatted `output.csv` and `log.txt`, and evaluates accuracy metrics.

## Setup & Running Instructions

1. **Install dependencies:**
   ```bash
   pip install -r code/requirements.txt

```

2. **Run the full audit pipeline:**
```bash
PYTHONPATH=. python -m code.main

```


3. **Run evaluation suite:**
```bash
PYTHONPATH=. python -m code.evaluation.evaluate

```



## Custom Evaluation Edge Cases Validated

* **Custom Case 1 (Account Tombstone with Unredacted PII):** Validated that `FINDING (REPORT_EXPOSED_PII)` -> `REMEDIATION (REDACT_RECORD)` -> `VERIFICATION (VERIFY_RECORD_REDACTED)` sequence is generated.
* **Custom Case 2 (Background Sync CRON Job):** Validated that `FINDING (REPORT_ACTIVE_DATA_FLOW)` -> `REMEDIATION (DISABLE_DATA_FLOW)` -> `VERIFICATION (VERIFY_DATA_FLOW_DISABLED)` sequence is generated.
* **Custom Case 3 (Legally Retained Audit Log):** Validated that retained compliance audit log records produce `VERIFY_RECORD_PRESENT` verification checks.

## Key Output Files

* `output.csv`: Complete results for all 518 deletion request cases.
* `log.txt`: Comprehensive system trace logs.
* `code/evaluation/evaluation_report.md`: Detailed evaluation breakdown and accuracy metrics.
* `code/evaluation/usage_report.md`: Token usage and cost accounting report.

