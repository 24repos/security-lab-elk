# Detection Rules

All rules are built in Kibana → Stack Management → Rules → Create rule,
type "Elasticsearch query", against the `security-lab-*` index pattern.
Time field: `@timestamp`. Schedule: every 30 seconds. Alert delay: 1.

## 1. SSH Brute Force Detection

```json
{
  "query": {
    "bool": {
      "must": [
        { "match_phrase": { "message": "Failed password" } },
        { "wildcard": { "log.file.path": "*auth.log*" } }
      ]
    }
  }
}
```
Threshold: count() overall documents is above 4, for the last 1 minute.

## 2. Windows Failed Login Detection

```json
{
  "query": {
    "match": {
      "winlog.event_id": "4625"
    }
  }
}
```
Threshold: count() overall documents is above 4, for the last 1 minute.

## 3. Port Scan Detection

```json
{
  "query": {
    "bool": {
      "must": [
        { "match_phrase": { "message": "UFW BLOCK" } }
      ]
    }
  }
}
```
Threshold: count() overall documents is above 9, for the last 1 minute.

## Notes

- Thresholds were tuned empirically against simulated attack traffic in this
  lab; a 5-attempt SSH/Windows threshold and 10-attempt scan threshold gave
  clean detections with no false positives during testing.
