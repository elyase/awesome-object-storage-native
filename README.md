# Awesome Object Storage Native [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> Open-source databases, application runtimes, and storage engines that use object storage for durable state, logs, indexes, or transactional datasets.

Entries are open-source implementations with a documented configuration in which object storage, such as Amazon S3, an S3-compatible service, Google Cloud Storage, or Azure Blob Storage, holds authoritative state, retained records, indexes, or transactional datasets. Some commit directly to object storage; others acknowledge writes through a separate durable tier first. Object-storage native does not mean bucket-only infrastructure: many entries also need a catalog, coordinator, or metadata database. Backup-only tools and general file-query engines are listed under Related Tools.

The [comparison](COMPARISON.md) records object-storage backends, licenses, the role object storage plays, dependencies, and caveats for each entry. Experimental implementations are kept in a [separate list](EXPERIMENTAL.md).

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
- [Articles and References](#articles-and-references)

## Stateful Applications and Durable Actors

- [Celld](https://github.com/denoland/celld) - Self-hosted Workers applications and Durable Objects with per-object SQLite and bucket-held state.
- [Rivet Actors](https://github.com/rivet-dev/rivet) - Stateful actors with durable SQLite, realtime connections, queues, and S3-tiered storage.
- [Terse Durable Actors](https://github.com/TerseAI/durable-actors) - Stateful TypeScript and Python actors with state snapshots in Google Cloud Storage.

## Streaming and Messaging

- [AutoMQ](https://github.com/AutoMQ/automq) - Kafka-compatible event streaming with shared logs on S3-compatible object storage.
- [Nisshi](https://github.com/nisshi-io/nisshi) - Kafka-compatible broker with selectable storage engines, including S3.
- [S2 Lite](https://github.com/s2-streamstore/s2#s2-lite) - Self-hostable server for the S2 durable streams API, built on SlateDB.
- [Ursa for Apache Kafka](https://github.com/openlakestream/kafka) - Kafka distribution with opt-in diskless topics stored through Ursa.

## Databases and Data Processing

- [Apache Druid](https://github.com/apache/druid) - Real-time analytical database with durable segments in object-backed deep storage.
- [ClickHouse](https://github.com/ClickHouse/ClickHouse) - Analytical database whose MergeTree tables can use S3 or Azure Blob Storage disks.
- [Embucket](https://github.com/Embucket/embucket) - DataFusion query engine with partial Snowflake compatibility and Iceberg tables in Amazon S3 Tables.
- [Neon](https://github.com/neondatabase/neon) - PostgreSQL with separated compute and storage, branching, and object-backed history.
- [RisingWave](https://github.com/risingwavelabs/risingwave) - Streaming SQL database that keeps state, tables, and materialized views in object storage.
- [XTDB](https://github.com/xtdb/xtdb) - Bitemporal SQL database with object-backed storage and a separately configured transaction log.

## Search and Retrieval

- [Chroma](https://github.com/chroma-core/chroma) - Search database whose distributed deployment uses object-backed indexes and the wal3 log.
- [HelixDB](https://github.com/HelixDB/helix-db) - Graph database with vector and full-text search over SlateDB storage on S3-compatible buckets.
- [LanceDB](https://github.com/lancedb/lancedb) - Embedded vector and multimodal database over Lance datasets in S3, Google Cloud Storage, or Azure Blob Storage.
- [Milvus](https://github.com/milvus-io/milvus) - Vector database with collection data and indexes in object storage and a separately configured write-ahead log.
- [OpenData Vector](https://github.com/opendata-oss/opendata/tree/main/vector) - Early vector database with metadata filtering on SlateDB storage.

## Transactional Tables and Array Datasets

- [Apache Hudi](https://github.com/apache/hudi) - Lakehouse tables with upserts, deletes, and incremental queries, keeping data and metadata in cloud storage.
- [Apache Iceberg](https://github.com/apache/iceberg) - Analytical table format with object-backed files and catalog-coordinated commits.
- [Apache Paimon](https://github.com/apache/paimon) - Lakehouse tables for streaming updates, change data, and analytical queries.
- [Delta Lake](https://github.com/delta-io/delta) - Transactional analytical tables with an object-backed log, also implemented natively in Rust by [delta-rs](https://github.com/delta-io/delta-rs).
- [DuckLake](https://github.com/duckdb/ducklake) - Transactional Parquet datasets with data files in object storage and a SQL database as the catalog.
- [Icechunk](https://github.com/earth-mover/icechunk) - Transactional Zarr array storage with snapshots, branches, and no external catalog.

## Observability

- [Grafana Loki](https://github.com/grafana/loki) - Log aggregation and LogQL queries over object-backed chunks and indexes.
- [Grafana Mimir](https://github.com/grafana/mimir) - Scalable Prometheus metrics with object-backed blocks and a separate ingestion tier.
- [Grafana Tempo](https://github.com/grafana/tempo) - Distributed tracing and TraceQL over object-backed trace blocks.
- [GreptimeDB](https://github.com/GreptimeTeam/greptimedb) - Observability database for metrics, logs, and traces with object-backed columnar storage.
- [InfluxDB 3 Core](https://github.com/influxdata/influxdb) - Time series database that stores its write-ahead log and Parquet data in object storage.
- [OpenObserve](https://github.com/openobserve/openobserve) - Logs, metrics, and traces with Parquet data in object storage.
- [Parseable](https://github.com/parseablehq/parseable) - SQL log analytics with data stored in object storage.
- [Quickwit](https://github.com/quickwit-oss/quickwit) - Search engine for logs and traces with indexes stored in object storage.
- [Thanos](https://github.com/thanos-io/thanos) - Global Prometheus queries and long-term metrics in object-backed TSDB blocks.

## Files, Volumes, and Repositories

- [JuiceFS](https://github.com/juicedata/juicefs) - Shared POSIX filesystem with file data in object storage and metadata in a separate database.
- [Walgit](https://github.com/tobi/walgit) - Git and Git LFS server that keeps repositories in a bucket and commits pushes with manifest compare-and-swap.
- [ZeroFS](https://github.com/Barre/ZeroFS) - Filesystem and block server over NFS, 9P, and NBD with data and metadata in object storage.

## Building Storage Systems

- [Graft](https://github.com/orbitinghail/graft) - Alpha transactional page storage over object storage, with a SQLite extension.
- [SlateDB](https://github.com/slatedb/slatedb) - Embedded LSM key-value store that writes its write-ahead log and sorted tables to object storage.
- [Tonbo](https://github.com/tonbo-io/tonbo) - Alpha embedded database with Parquet tables and manifest compare-and-swap on S3-compatible storage.
- [Ursa](https://github.com/openlakestream/ursa) - Embeddable stream storage engine with object-backed logs and optional Iceberg or Delta Lake materialization.
- [wal3](https://github.com/chroma-core/chroma/tree/main/rust/wal3) - Chroma's Rust log library built from immutable object fragments and conditional manifest updates.
- [Woodpecker](https://github.com/zilliztech/woodpecker) - Write-ahead log library and service on object storage, with etcd for metadata and coordination.

## Related Tools

- [DuckDB](https://github.com/duckdb/duckdb) - In-process analytical database that can read and write Parquet, CSV, and JSON files on object storage.
- [Litestream](https://github.com/benbjohnson/litestream) - Asynchronous SQLite replication to object storage for backup and recovery.

## Articles and References

- [An Introduction to WAL-on-S3 Architectures](https://choplin.dev/slides/wal-on-s3/) - Akihiro Okuno's 41-slide Scalar Inc. tech talk on commit placement, fencing, batching, and durability tradeoffs across databases and streaming systems. ([PDF](https://choplin.dev/slides/wal-on-s3/wal-on-s3-architectures.pdf))
- [Git at any scale](https://cursor.com/blog/git-at-any-scale) - Design of a Git service that uses S3-compatible storage as the source of truth for repositories.
- [KIP-1150: Diskless Topics](https://cwiki.apache.org/confluence/spaces/KAFKA/pages/345377898/KIP-1150+Diskless+Topics) - Accepted Apache Kafka proposal for topics whose durability comes from object storage.
- [OSWALD](https://github.com/nvartolomei/oswald) - Write-ahead log design built only on object storage primitives.
- [Replacing NATS with S3](https://rivet.dev/blog/2026-10-04-replacing-our-message-broker-with-s3/) - Forthcoming Rivet 3.0 design that sends latency-tolerant broadcasts through per-node S3 logs and request-reply traffic directly between nodes.
- [S3 WAL Collection](https://github.com/Vanlightly/s3-wal-collection) - Catalog of write-ahead log designs on object storage, with TLA+ specifications.
- [turbopuffer Architecture](https://turbopuffer.com/docs/architecture) - Search engine design with its write-ahead log and indexes on object storage.
- [Zero-Disk S3 Storage for SQLite](https://rivet.dev/blog/2026-07-31-how-we-built-the-first-zero-disk-s3-tiered-storage-engine-for-sqlite/) - Rivet's released SQLite storage, which commits to a replicated hot tier and moves idle data to S3.

## Contributing

Contributions are welcome. Read the [contribution guidelines](CONTRIBUTING.md) first.
