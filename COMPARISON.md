# Comparison

Technical details for the projects in the [list](README.md). Information was checked against each project's repository and documentation on October 7, 2026. Re-check the linked sources before relying on a specific claim.

How to read the tables:

- **Backends** are the object stores a project documents or implements. Support is not inherited from a dependency: a project built on a multi-cloud storage library is listed only with the backends it exposes. Compatibility with a provider's API is not a certification that a full deployment on that cloud is supported or tested.
- **License** is the SPDX identifier of the named implementation, followed by the license type. Commercial editions and managed services can have different licenses and features.
- **Object-storage role** is what the project keeps in the bucket.
- **Dependencies and caveats** names other services required for a typical deployment, and where writes are acknowledged when the documentation says so. "Not established" means the sources checked did not say.

## Contents

- [Stateful Applications and Durable Actors](#stateful-applications-and-durable-actors)
- [Streaming and Messaging](#streaming-and-messaging)
- [Databases and Data Processing](#databases-and-data-processing)
- [Search and Retrieval](#search-and-retrieval)
- [Transactional Tables and Array Datasets](#transactional-tables-and-array-datasets)
- [Observability](#observability)
- [Files, Volumes, and Repositories](#files-volumes-and-repositories)
- [Building Storage Systems](#building-storage-systems)
- [Related Tools](#related-tools)

## Stateful Applications and Durable Actors

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Celld | S3 and S3-compatible, GCS, Azure Blob. | Apache-2.0, permissive. | Deployments, ownership records, and long-term cell state. | A single node acknowledges a write after uploading it to the bucket. A fleet of two or more nodes acknowledges after replicating to peers and uploads afterwards. Ownership uses conditional bucket writes; no separate coordination service. | Pre-1.0 releases. | [README](https://github.com/denoland/celld#readme) |
| Rivet Actors | S3-compatible (cold tier). | Apache-2.0, permissive. | Idle SQLite data moved out of the hot tier. | Released storage commits to a replicated hot tier (FoundationDB, PostgreSQL, or RocksDB); S3 is not on the write path. Data idle for 7 days (configurable) moves to S3 and is restored on access. Rivet 3.0 is announced as forthcoming: latency-tolerant broadcasts move to per-node S3 logs and request-reply traffic goes directly between nodes. The announcement does not describe a change to how actor state is committed. | Tiered storage released; 3.0 forthcoming. | [Tiered storage](https://rivet.dev/blog/2026-07-31-how-we-built-the-first-zero-disk-s3-tiered-storage-engine-for-sqlite/), [3.0 messaging](https://rivet.dev/blog/2026-10-04-replacing-our-message-broker-with-s3/) |
| Terse Durable Actors | GCS. S3 and Azure Blob not established. | MIT, permissive. | Ownership records, immutable code, and state snapshots. | Documented self-hosting is on Google Cloud: GKE, PostgreSQL, and a Standard GCS bucket. Optional GCS Rapid buckets in two zones hold the hot write log; acknowledged writes are flushed to both Rapid copies, and history stays in the Standard bucket. | Pre-1.0 releases. | [Self-hosting](https://github.com/TerseAI/durable-actors/blob/main/docs/self-hosting.md) |

## Streaming and Messaging

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| AutoMQ | S3 and S3-compatible. | Apache-2.0, permissive. | Kafka partition logs through its S3Stream storage layer. | The README says the open-source edition runs directly on S3 by default, with latency in the hundreds of milliseconds, and attributes lower latency to the enterprise edition. Local disk storage remains available. | Released. | [README](https://github.com/AutoMQ/automq#readme) |
| Nisshi | S3 and S3-compatible. Other engines: PostgreSQL, libSQL, memory. | Apache-2.0, permissive. | Topic data when the S3 storage engine is selected. | The default storage engine is memory; S3 must be selected explicitly. Acknowledgment behavior of the S3 engine is not established. | Pre-release versions. | [README](https://github.com/nisshi-io/nisshi#readme) |
| S2 Lite | S3 and S3-compatible. Local directory and in-memory modes. | MIT, permissive. Covers the open-source server, not the managed S2 service. | Stream records: SlateDB write-ahead log and LSM data. | By default the write-ahead log shares the main bucket, and the README states records are durable in object storage before acknowledgment. The write-ahead log can instead be placed on local disk or in a separate bucket; do not assume the same guarantee in those modes. Single-node binary with no other dependencies. | Released. | [README](https://github.com/s2-streamstore/s2#s2-lite) |
| Ursa for Apache Kafka | S3, GCS, Azure Blob (through Ursa). | Apache-2.0, permissive. | Write-ahead log and compacted objects for diskless topics; optional Iceberg table. | Diskless storage is enabled per topic. Classic topics and internal topics such as consumer offsets stay on broker disks. Requires Oxia and a separate compaction service; without compaction, retention does not reclaim storage. Diskless topics do not support transactional producers or key-based log compaction. | No GitHub releases; Ursa 1.0 is on Maven Central. | [README](https://github.com/openlakestream/kafka#readme) |

## Databases and Data Processing

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Apache Druid | S3 and S3-compatible, GCS, Azure Blob. HDFS and local also available. | Apache-2.0, permissive. | Published segments in deep storage. | Requires a separate metadata store and coordination. Historical processes serve segments from local cache. Deep storage is not on the ingestion acknowledgment path. | Released. | [Deep storage](https://druid.apache.org/docs/latest/design/deep-storage/) |
| ClickHouse | S3 and S3-compatible (including GCS through its S3-compatible API), Azure Blob. | Apache-2.0, permissive. | MergeTree data parts on an object-storage disk. | Applies only when tables use an object-storage disk. The default `local` metadata type keeps the file-to-object mapping on local disk; `plain_rewritable` keeps no local metadata. Zero-copy replication is disabled by default and not recommended for production. | Released. | [External disks](https://clickhouse.com/docs/operations/storing-data) |
| Embucket | Amazon S3 Tables. Other S3-compatible endpoints not established. | Apache-2.0, permissive. | Iceberg tables in S3 Tables buckets. | Deploys as an AWS Lambda function; query execution runs on a single node per request. Snowflake compatibility is partial. | Pre-1.0 releases; no commits since April 2026. | [README](https://github.com/Embucket/embucket#readme), [Architecture](https://docs.embucket.com/reference/architecture/) |
| Neon | S3 and S3-compatible, GCS, Azure Blob (in its remote storage source). | Apache-2.0, permissive. | Long-term data and history uploaded by the pageserver. | Commits are acknowledged by a quorum of safekeepers, which store the write-ahead log until it is processed and uploaded. Object storage is not on the synchronous commit path. | Released. | [Architecture](https://neon.com/docs/introduction/architecture-overview), [README](https://github.com/neondatabase/neon#readme) |
| RisingWave | S3 and S3-compatible, GCS, Azure Blob, and others. | Apache-2.0, permissive. | Internal state, tables, and materialized views in its Hummock state store. | A meta service with its own metadata store is required. Optional local disk cache for hot data. | Released. | [README](https://github.com/risingwavelabs/risingwave#readme), [State stores](https://github.com/risingwavelabs/risingwave-operator/blob/main/docs/general/state-stores.md) |
| XTDB | S3 and S3-compatible, GCS, Azure Blob. | MPL-2.0, weak (file-level) copyleft. | Columnar data in its remote storage module. | The transaction log is configured separately: in-memory (default), local disk for a single node, or Kafka for multiple nodes. | Released. | [Storage](https://docs.xtdb.com/ops/config/storage.html), [Log](https://docs.xtdb.com/ops/config/log.html) |

## Search and Retrieval

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Chroma | S3 and S3-compatible; local storage. | Apache-2.0, permissive. | Indexes, data, and the wal3 log in the distributed deployment. | Only the distributed deployment uses object storage this way; a single-node Chroma server persists to local disk. | Released. | [Storage source](https://github.com/chroma-core/chroma/tree/main/rust/storage), [wal3](https://github.com/chroma-core/chroma/blob/main/rust/wal3/README.md) |
| HelixDB | S3 and S3-compatible (custom endpoint). Local disk and in-memory modes. | Apache-2.0, permissive. | SlateDB write-ahead log and sorted tables. | Object storage is used only when `S3_BUCKET` is set; with no storage settings the server runs in memory. S3 mode always adds a local memory-and-disk cache. Other SlateDB backends are not exposed by the server. | Released. | [Server storage config](https://github.com/HelixDB/helix-db/blob/main/crates/server/src/config.rs) |
| LanceDB | S3 and S3-compatible, GCS, Azure Blob. | Apache-2.0, permissive. | Lance dataset files and versions. | Embedded library; LanceDB Cloud and Enterprise are separate commercial products. Safety of concurrent writers on object storage depends on store and configuration and is not covered here. | Pre-1.0 releases. | [Storage configuration](https://docs.lancedb.com/storage/configuration) |
| Milvus | S3-compatible (MinIO by default; AWS, Google Cloud, and Alibaba Cloud through the S3 API), native GCS, Azure Blob. | Apache-2.0, permissive. | Collection data, binlogs, and indexes. | Requires etcd (or TiKV) for metadata and a separately configured write-ahead log or message queue. Milvus Lite uses a local file instead. | Released. | [Configuration](https://github.com/milvus-io/milvus/blob/master/configs/milvus.yaml), [S3 deployment](https://milvus.io/docs/deploy_s3.md) |
| OpenData Vector | AWS S3; local filesystem and in-memory modes. | MIT, permissive. | SlateDB-backed vector data and posting lists. | The default configuration uses a local directory. Only AWS, local, and in-memory object stores are exposed; other SlateDB backends are not. | Early. | [Vector README](https://github.com/opendata-oss/opendata/blob/main/vector/README.md), [Storage config](https://github.com/opendata-oss/opendata/blob/main/common/src/storage/config.rs) |

## Transactional Tables and Array Datasets

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Apache Hudi | S3, GCS, Azure, Alibaba Cloud OSS, Tencent COS, IBM Cloud Object Storage, Baidu BOS, Oracle Cloud Infrastructure. | Apache-2.0, permissive. | Data files, timeline, and metadata table. | Used through a compute engine such as Spark or Flink. Multiple writers need concurrency-control configuration, such as a lock provider. | Released. | [Cloud storage](https://hudi.apache.org/docs/cloud) |
| Apache Iceberg | S3, GCS, Azure, and Hadoop-compatible filesystems through FileIO implementations. | Apache-2.0, permissive. | Data files, manifests, and table metadata. | A catalog atomically publishes the current metadata pointer. Backend support depends on the engine, catalog, and FileIO in use. | Released. | [FileIO](https://iceberg.apache.org/docs/latest/fileio/) |
| Apache Paimon | S3, GCS, Azure, Alibaba Cloud OSS, Tencent COS, Huawei OBS; HDFS and local. | Apache-2.0, permissive. | Data files, snapshots, manifests, and changelogs. | Used through engines such as Flink and Spark. Most object stores need a plugin JAR. | Released. | [Filesystems](https://paimon.apache.org/docs/master/maintenance/filesystems/) |
| Delta Lake, delta-rs | S3, GCS, Azure, depending on implementation and connector. | Apache-2.0, permissive. | Data files and the transaction log. | Safety of concurrent writers on S3 depends on the implementation: coordination such as a DynamoDB-backed log store, or conditional writes where delta-rs supports them. | Released. | [Delta Lake](https://github.com/delta-io/delta#readme), [delta-rs S3](https://docs.rs/deltalake-aws/latest/deltalake_aws/) |
| DuckLake | S3 and S3-compatible, GCS, Azure Blob; local and network filesystems. | MIT, permissive. | Immutable Parquet data and delete files. | The catalog is a SQL database, and commits are catalog transactions. | Distributed as a DuckDB extension. | [Choosing storage](https://ducklake.select/docs/stable/duckdb/usage/choosing_storage) |
| Icechunk | Native S3 and S3-compatible; GCS, Azure Blob, local, and in-memory through Arrow `object_store`. | Apache-2.0, permissive. | Chunks, manifests, snapshots, and branch and tag references. | No external database or catalog. Conflicting commits to a branch are rejected, so only one succeeds. Provider-specific notes are in the storage guide. | Released. | [README](https://github.com/earth-mover/icechunk#readme), [Storage guide](https://icechunk.io/en/latest/guides/storage/) |

## Observability

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Grafana Loki | S3 and S3-compatible, GCS, Azure Blob. | AGPL-3.0, network copyleft. | Log chunks and index. | The distributor acknowledges after a quorum of ingesters accept a write; chunks are uploaded later. | Released. | [Architecture](https://grafana.com/docs/loki/latest/get-started/architecture/) |
| Grafana Mimir | S3 and S3-compatible, GCS, Azure Blob, OpenStack Swift. | AGPL-3.0, network copyleft. | TSDB blocks, plus ruler and Alertmanager state. | Recent samples live in the ingestion tier before blocks are uploaded. | Released. | [Object storage](https://grafana.com/docs/mimir/latest/configure/configure-object-storage-backend/) |
| Grafana Tempo | S3 and S3-compatible, GCS, Azure Blob. | AGPL-3.0, network copyleft. | Trace blocks. | Recent traces live in the ingestion tier before blocks are written. | Released. | [Storage](https://grafana.com/docs/tempo/latest/configuration/hosted-storage/) |
| GreptimeDB | S3 and S3-compatible, GCS, Azure Blob. | Apache-2.0, permissive. | Columnar data files written by datanodes. | Datanodes keep a write-ahead log before flushing to object storage. Object storage and cluster deployment are in the Apache-2.0 build; some operations, such as region migration, are manual in that build. | Released. | [README](https://github.com/GreptimeTeam/greptimedb#readme) |
| InfluxDB 3 Core | S3 and S3-compatible, GCS, Azure Blob; local and in-memory modes. | MIT OR Apache-2.0, permissive. | Write-ahead log files and Parquet data. | With the default `no_sync=false`, writes are acknowledged after the write-ahead log is persisted to the object store, which is flushed every second by default. `no_sync=true` acknowledges before persistence. | Released. | [Durability](https://docs.influxdata.com/influxdb3/core/reference/internals/durability/) |
| OpenObserve | S3 and S3-compatible, GCS, Azure Blob. | AGPL-3.0, network copyleft. | Parquet data files. | Ingesters write a local write-ahead log and Parquet files before upload. High availability requires PostgreSQL for metadata and NATS. Some features are enterprise only. | Released. | [Architecture](https://openobserve.ai/docs/architecture/) |
| Parseable | S3 and S3-compatible, GCS, Azure Blob; local filesystem. | AGPL-3.0, network copyleft. | Ingested data. | Data is staged on local disk before it is pushed to object storage, so ingest acknowledgment does not imply the data is already in the bucket. Distributed and enterprise features differ by edition. | Released. | [Configuration](https://www.parseable.com/docs/self-hosted/configuration) |
| Quickwit | S3 and S3-compatible, Azure Blob, GCS; local filesystem. | Apache-2.0, permissive. | Index splits. | Requires a metastore. Ingested documents are buffered by indexers before splits are uploaded. | Released. | [Storage configuration](https://quickwit.io/docs/configuration/storage-config) |
| Thanos | GCS, S3 and S3-compatible, Azure (stable); OpenStack Swift, Tencent COS, Alibaba Cloud OSS, Baidu BOS, Oracle Cloud Infrastructure (beta); local filesystem. | Apache-2.0, permissive. | Prometheus TSDB blocks. | Recent data lives in Prometheus or Thanos Receive until blocks are uploaded. | Released. | [Object storage](https://thanos.io/tip/thanos/storage.md/) |

## Files, Volumes, and Repositories

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| JuiceFS | S3 and S3-compatible, GCS, Azure Blob, Alibaba Cloud OSS, Tencent COS, and others. | Apache-2.0, permissive (community edition). | File data blocks. | Metadata lives in a separate engine such as Redis, MySQL, SQLite, or TiKV. | Released. | [README](https://github.com/juicedata/juicefs#readme) |
| Walgit | S3 and S3-compatible, GCS; in-memory store for tests. | MIT, permissive. | Repository data, operation log, manifest, and LFS objects. | A push is acknowledged only after the manifest compare-and-swap succeeds in the bucket. Servers are disposable caches. | No tagged releases. | [README](https://github.com/tobi/walgit#readme) |
| ZeroFS | S3 and S3-compatible, GCS, Azure Blob; local disk. | AGPL-3.0, network copyleft. A commercial license is also offered. | File data segments and LSM-tree metadata. | Durability differs by protocol: NBD FLUSH and FUA return after data is durable, and 9P `fsync` returns after stable storage. NFS reports buffered writes as stable, and tested clients do not send COMMIT on `fsync`. Requires conditional writes; stores without them need Redis. | Released. | [README](https://github.com/Barre/ZeroFS#readme) |

## Building Storage Systems

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| Graft | S3-compatible (AWS S3, MinIO, R2); filesystem and in-memory. | MIT OR Apache-2.0, permissive. | Transactional page storage for volumes. | Alpha; the maintainers ask to be contacted before production use. | Alpha. | [README](https://github.com/orbitinghail/graft#readme), [Configuration](https://graft.rs/docs/sqlite/config/) |
| SlateDB | S3 and S3-compatible, GCS, Azure Blob, through the Rust `object_store` crate. | Apache-2.0, permissive. | Write-ahead log, sorted tables, and manifest. | Writes return after updating memory. Call `await_durable` on a write handle, or `flush`, to wait for object-storage durability. | Pre-1.0 releases. | [README](https://github.com/slatedb/slatedb#readme) |
| Tonbo | S3 and S3-compatible (R2, MinIO); local filesystem. | Apache-2.0, permissive. | Write-ahead log, Parquet tables, and manifest. | The manifest is committed with compare-and-swap on S3, so no coordinator is needed. | Alpha. | [README](https://github.com/tonbo-io/tonbo#readme) |
| Ursa | S3, GCS, Azure Blob. | Apache-2.0, permissive. | Write-ahead log, compacted objects, and offset index. | Embedded in a broker; Ursa for Apache Kafka pairs it with Oxia for metadata. Optional Iceberg or Delta Lake materialization. | Released. | [README](https://github.com/openlakestream/ursa#readme) |
| wal3 | S3 and S3-compatible, through Chroma's storage layer. | Apache-2.0, permissive. | Log fragments, snapshots, and manifest. | Internal library in the Chroma repository, not a standalone service. Uses conditional writes and needs no other locking or coordination service. | Released as part of Chroma. | [README](https://github.com/chroma-core/chroma/blob/main/rust/wal3/README.md) |
| Woodpecker | MinIO-compatible, AWS S3, GCS, Azure, Alibaba Cloud OSS. | Apache-2.0 outside `server/`; `server/` is AGPL-3.0-only OR SSPL-1.0, at the user's choice. SSPL is not OSI-approved; AGPL is network copyleft. | Log data. | Requires etcd for metadata and coordination. Service mode adds a separate LogStore cluster. | Pre-1.0 releases. | [README](https://github.com/zilliztech/woodpecker#readme), [License](https://github.com/zilliztech/woodpecker/blob/master/LICENSE.txt) |

## Related Tools

| Project | Backends | License | Object-storage role | Dependencies and caveats | Status | Sources |
| --- | --- | --- | --- | --- | --- | --- |
| DuckDB | S3 and S3-compatible through `httpfs`; other stores through extensions. | MIT, permissive. | Reads and writes data files such as Parquet. | Its own database file and write-ahead log are local; DuckLake adds object-backed transactional tables. | Released. | [S3 API support](https://duckdb.org/docs/stable/core_extensions/httpfs/s3api) |
| Litestream | S3 and S3-compatible, GCS, Azure Blob, and others. | Apache-2.0, permissive. | SQLite snapshots and write-ahead log replicas. | SQLite commits locally first and replication is asynchronous. Intended for backup and recovery. | Released. | [How it works](https://litestream.io/how-it-works/) |
