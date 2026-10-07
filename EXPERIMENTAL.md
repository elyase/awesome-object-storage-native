# Experimental Implementations

Open-source implementations that fit the [list's](README.md) scope but are not yet recommended for general use, because of their documented support status or validation gaps. Information was checked on October 7, 2026.

- [Inkless](https://github.com/aiven/inkless) - Apache Kafka fork implementing diskless topics with batches in object storage and a PostgreSQL batch coordinator.
- [WombatKV](https://github.com/Venkat2811/wombatkv) - LLM inference cache that stores reusable attention key-value blocks in object storage, with a SlateDB index.

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Inkless | S3 and S3-compatible, GCS, Azure Blob. | AGPL-3.0 for `storage/inkless`, network copyleft; original Apache Kafka files remain Apache-2.0. | Batches of diskless-topic records. | Requires PostgreSQL for batch coordination (in-memory for tests). The maintainers state it is not meant to be a long-term fork, they do not accept patches, and the code is open for information while the changes are proposed upstream through KIP-1150. | Experimental fork. | [README](https://github.com/aiven/inkless#readme), [Deployment](https://github.com/aiven/inkless/blob/main/docs/inkless/README.md), [License](https://github.com/aiven/inkless/blob/main/LICENSE.md) |
| WombatKV | AWS S3, MinIO, Cloudflare R2, GCS. Tigris, Azure Blob, and S3 Express One Zone are listed as not yet implemented. | Apache-2.0, permissive. | Content-addressed inference KV blocks; SlateDB metadata index. | The 0.1.0 alpha integrates with one inference engine (ds4). End-to-end validation on Linux is still pending. "KV" refers to transformer attention tensors, not a key-value database API. | Alpha. | [README](https://github.com/Venkat2811/wombatkv#readme) |
