# Lakehouse Docker Environment

A fully containerized lakehouse architecture for analytics and data management, combining object storage, SQL query engine, table catalog, metadata store, and schema registry—all running in Docker containers on a single network.

---

## 🚀 Quick Start

1. **Prerequisites**

   * [Docker](https://www.docker.com/get-started)
   * [Docker Compose](https://docs.docker.com/compose/install)
   * An external Docker network named `lakehouse_net`:

     ```bash
     docker network create lakehouse_net
     ```

2. **Launch the stack**

   ```bash
   docker-compose up -d
   ```

3. **Verify health**
   Each service includes a health check. Use:

   ```bash
   docker ps
   ```

   to confirm all containers report `healthy`.

---

## 🏗 Architecture Overview

```text
+-------------+       +-----------+       +--------------+
|  PostgreSQL |<----->| Iceberg   |<----->|   MinIO      |
|   Metadata  |       | REST      |       |   Storage    |
+-------------+       +-----------+       +--------------+
      ^                     |                   ^
      |                     v                   |
 +---------+             +------+             +------+
 |Apicurio |<----------->|Trino |<------------|Users |
 |Registry |   Schemas   | SQL  |  Queries    | BI/ML|
 +---------+             +------+             +------+
```

* **MinIO**: S3‑compatible object storage (data lake)
* **Trino**: Distributed SQL query engine
* **Iceberg REST**: Table catalog interface backed by PostgreSQL
* **PostgreSQL**: Metadata store for Iceberg catalog & Apicurio
* **Apicurio Registry & UI**: Schema management for Avro/JSON

---

## 📦 Services & Endpoints

| Service          | Host\:Port       | Purpose                         | UI/API URL                                 |
| ---------------- | ---------------- | ------------------------------- | ------------------------------------------ |
| **MinIO**        | `localhost:9000` | Object storage                  | Console: `http://localhost:9001`           |
| **Trino**        | `localhost:8091` | SQL engine for analytics        | Web UI: `http://localhost:8091`            |
| **Iceberg REST** | `localhost:8181` | REST catalog for Iceberg tables | API: `http://localhost:8181`               |
| **PostgreSQL**   | `localhost:5432` | Metadata database               | `postgres://admin:password@localhost:5432` |
| **Apicurio API** | `localhost:5544` | Schema registry API             | `http://localhost:5544/apis/registry/v3`   |
| **Apicurio UI**  | `localhost:8888` | Schema registry web interface   | `http://localhost:8888`                    |

---

## ⚙️ Configuration

All configurations and persistent data mount points are defined in `docker-compose.yml` and local subdirectories:

```
├── docker-compose.yml        # Service definitions
├── persistent/data/          # MinIO storage volume
├── trino/
│   ├── etc/                  # Trino config files
│   └── data/                 # Local Trino data
└── postgres_data/            # PostgreSQL data volume
```

### Environment Variables

* **MinIO**

  * `MINIO_ROOT_USER=minioadmin`
  * `MINIO_ROOT_PASSWORD=minioadmin`
* **PostgreSQL**

  * `POSTGRES_USER=admin`
  * `POSTGRES_PASSWORD=password`
  * `POSTGRES_DB=demo_catalog`
* **Iceberg REST**

  * `CATALOG_URI=jdbc:postgresql://postgres/demo_catalog`
  * `CATALOG_WAREHOUSE=s3://warehouse`
  * `CATALOG_S3_ENDPOINT=http://minio:9000`
* **Apicurio**

  * `APICURIO_DATASOURCE_URL=jdbc:postgresql://postgres/apicurio-registry`

---

## 🔄 Shutdown & Cleanup

* **Stop containers**:

  ```bash
  docker-compose down
  ```
* **Remove volumes**:

  ```bash
  docker-compose down -v
  ```
* **Remove external network**:

  ```bash
  docker network rm lakehouse_net
  ```

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## 📬 Contact

Questions, feedback, or collaborations? Open an Issue or Pull Request on this repository.
