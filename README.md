# x402-recorder-witness

Public witness for the **Assay** archive.

Assay records, hourly, what services in the x402 catalog charged and whether they were reachable.
Each day of that archive is sealed into a hash chain: every day's manifest carries a sha256 of
every file captured that day, plus the previous day's `chain_sha256`.

A chain you hold alone proves nothing. Whoever holds the archive can rewrite any day and recompute
every later link, and the verifier would still report `ok` — it detects accident, not tampering.

So every sealed manifest is committed here, to a repository that is public and timestamped by
someone other than us. **Manifests only, never the underlying data** — a manifest is a few kB and
already carries a sha256 of every blob, so witnessing the manifests witnesses the data.

```
manifests/YYYY-MM-DD.json
```

Each file contains:

| Field | Meaning |
|---|---|
| `date` | the UTC day sealed |
| `files[]` | every captured file that day, with its `sha256` and record count |
| `prev_chain` | the previous sealed day's `chain_sha256` |
| `chain_sha256` | sha256 over the date, `prev_chain`, presence, and the file digests |
| `absent` | present and `true` if the recorder captured nothing that day |

`absent` is inside the digest deliberately: a day with no data is a *claim about that day*, and it
is sealed as firmly as a day with data. A sealed day is never edited. Data that arrives late is
filed as a supplement under a later date and marked `recovered`, so the archive stays honest about
what it knew when.

To check a day: recompute the chain from the manifests here and compare against the archive you
were given. If they disagree, the archive is wrong and this repository is the evidence.

Collector source (AGPL-3.0): https://github.com/RegardV/x402-recorder
