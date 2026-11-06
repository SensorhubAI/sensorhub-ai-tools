# Site stats

Data for the counter on the Sensorhub website: "Sensorhub analyzed X posts and pages and found Y relevant signals in the last 30 days".

A scheduled job updates `stats.json` once a day. The website reads it and counts up to these numbers when the row comes into view. Between updates it shows the last real count.

| Field | Meaning |
|---|---|
| `analyzed` | Social posts fetched, search results checked, AI answers run and cited pages scanned in the 30 days before `asOf`. Posts both environments fetched are counted once. |
| `signals` | Posts that passed the relevance check for a project in the same 30 days. |
| `asOf` | When the counts were taken (UTC). |
| `envs` | The same counts per environment. |

This branch only ever holds this data. It is not merged into `main`.
