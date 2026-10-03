# Deploy Guide — api-oss-data
**Platform:** Anticloud sovereign infrastructure | Air-gap capable
**Stack:** Python 3.11, pandas, pyarrow, SQLite, AIOSS_FORMAT

## Prerequisites
Python 3.11+, pandas 2.2+, pyarrow 15.0+, SQLite (stdlib)

## AIOSS Integration
```bash
aioss init --module api-oss-data --output ./api_oss_data.aioss
aioss append --chain ./api_oss_data.aioss --payload ./output.bin --module api-oss-data
aioss verify --chain ./api_oss_data.aioss
```

## Air-Gap Deployment
```bash
# On networked machine:
pip download -r requirements.txt -d ./wheels/
# Transfer wheels/ to air-gap host, then:
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="api-oss-data",
    aioss_chain="./api_oss_data.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./api_oss_data.aioss --verbose
python -m api_oss_data.tests.smoke
```
