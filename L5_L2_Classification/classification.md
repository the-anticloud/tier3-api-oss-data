# L5 Narrow / L2 General Classification — api-oss-data
**Platform:** Anticloud | **PAX:** 27B | **IP:** USPTO pending 2026, Anticloud FZ LLE
**Domain:** Sovereign data pipeline: ingestion, transformation, and storage for Anticloud

## L5 Narrow
api-oss-data manages data ingestion from local sources (files, sensors, databases), applies transformations, and stores results in sovereign local storage. No data is sent to external services. Transformation pipelines are AIOSS-chained for full lineage.

## L2 General
L2 General: same data pipeline framework handles biosignal EDF files (TIER_7), RF packet captures (TIER_8), ROS2 sensor feeds (TIER_9), and clinical HL7/FHIR records — all through the same api-oss-data API.

## PAX Integration
PAX 27B is used for intelligent schema inference and data quality assessment: given a new data source, PAX identifies the schema, flags anomalies, and suggests transformation rules.

## AIOSS Audit Relevance
Every data transformation event (input hash + transformation spec hash + output hash + quality metrics) is AIOSS-chained: H_n = SHA3-256(H_{n-1} || entry_hash_n || timestamp_n).
Tamper-evident, offline-verifiable, zero cloud dependency.

## Regulatory / Compliance
GDPR Art. 5 (purpose limitation), HIPAA Safe Harbor (for clinical data), NIST SP 800-188
