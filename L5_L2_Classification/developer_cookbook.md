# Developer Cookbook — api-oss-data
**Stack:** Python 3.11, pandas, pyarrow, SQLite, AIOSS_FORMAT
**Domain:** Sovereign data pipeline: ingestion, transformation, and storage for Anticloud
**License:** Apache-2.0 | **IP:** USPTO pending 2026, Anticloud FZ LLE

## Core Usage

```python
from api_oss_data import DataPipeline, Transformer

pipeline = DataPipeline(storage="./anticloud_data/", aioss_chain="./data.aioss")

# Ingest local CSV
df = pipeline.ingest("./clinical_records.csv", schema="auto")

# Apply transformations
transformed = pipeline.transform(df, steps=[
    Transformer.normalize_timestamps(tz="UTC"),
    Transformer.drop_pii(fields=["name", "dob", "ssn"]),
    Transformer.validate_schema(expected_schema)
])

# Store with AIOSS provenance
record = pipeline.store(transformed, name="clinical_records_clean")
print(f"Stored {len(transformed)} rows. Chain: {record.chain_hash}")

# Query with lineage
result = pipeline.query("SELECT * FROM clinical_records_clean WHERE anomaly_flag = 1")
print(result.df, result.provenance)
```

## AIOSS Chain Append

```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()

# After every api-oss-data output:
chain_hash = aioss_append("./api_oss_data.aioss",
                           result_bytes, "api-oss-data")
```

## Performance & Integration

pyarrow columnar format: ~10x faster than CSV for large datasets. Pipeline steps are lazy (no data loaded until .execute()). AIOSS entry per pipeline run, not per row. Integration: feeds api-oss-database, api-oss-analytics. Consumed by TIER_7 biosignal projects for EDF ingestion.
