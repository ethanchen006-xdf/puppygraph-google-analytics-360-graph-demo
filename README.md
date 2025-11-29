# PuppyGraph Demo GA360 Analytics

## Prerequisites
- `./docker-compose.yaml`
- Data file at `./data/sample_data.csv`
- SQL files (`./sql-queries/hit_tables.sql`, `./sql-queries/nodes.sql`, `./sql-queries/edges.sql`
- Assumed MySQL Root Password: `mysql123`
- Assumed MySQL Database Name: `journey`

---
## 1.  Launch Containers
Launch PuppyGraph and MySQL container defined in docker-compose.yml
```bash
docker compose up -d
```

---

## 2. Copy Data and Create/Load Schema (Execute Inside MySQL Shell)

 2a. Copy the CSV file to the MySQL container's secure directory
```bash
docker cp ./data/sample_data.csv mysql-server:/var/lib/mysql-files/sample_data.csv
```

 2b. Create Tables and Import Data
```bash
docker exec -it mysql-server mysql -uroot -pmysql123
```
-- Database Creation
``` bash
CREATE DATABASE journey;
USE journey;
```

-- Table Creation (as provided)
```bash
CREATE TABLE sample_data (
    visitId BIGINT,
    visitNumber INT,
    visitStartTime BIGINT,
    date DATE,
    fullVisitorId BIGINT,
    total_visits INT,
    total_hits INT,
    total_pageviews INT,
    total_bounces INT,
    total_session_quality INT,
    total_revenue DECIMAL(10,2),
    source VARCHAR(255),
    medium VARCHAR(255),
    campaign VARCHAR(255),
    keyword VARCHAR(255),
    device VARCHAR(100),
    browser VARCHAR(100),
    operatingSystem VARCHAR(100),
    country VARCHAR(100),
    city VARCHAR(100),
    hit_number INT,
    page_path TEXT,
    page_title TEXT,
    is_entrance TINYINT,
    is_exit TINYINT,
    time BIGINT,
    action_type VARCHAR(50),
    action_step INT,
    transaction_id VARCHAR(255),
    event_category VARCHAR(255),
    event_action VARCHAR(255),
    event_label VARCHAR(255),
    product_names TEXT,
    product_skus TEXT,
    product_categories TEXT
);

```

-- Data Loading
```bash
LOAD DATA INFILE '/var/lib/mysql-files/sample_data.csv'
INTO TABLE sample_data
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

---

## 3. Run Preparatory SQL Files (Derive Graph Tables)

```bash
docker exec -i mysql-server mysql -uroot -pmysql123 journey < ./sql-queries/hit_tables.sql
docker exec -i mysql-server mysql -uroot -pmysql123 journey < ./sql-queries/nodes.sql
docker exec -i mysql-server mysql -uroot -pmysql123 journey < ./sql-queries/edges.sql

```

---

## 4. PuppyGraph Configuration Steps (Manual Web UI)

1.  **Access UI:** Go to **`http://localhost:8081`**.
    * **Username:** `puppygraph`
    * **Password:** `puppygraph123`

2.  **Create Schema/Catalog:**
    * Click **"Create Schema"**.
    * **Engine:** `mysql8`
    * **Name:** *[Any name]*
    * **Username:** `mysqluser`
    * **Password:** `mysqlpassword`
    * **JDBC Connection String:** `jdbc:mariadb://mysql:3306`
    * Click **Save and Submit**.

3.  **Add Nodes:**
    * Click **"Add Node from Database"**.
    * Select table **`nodes`**.
    * Click **"Add Node"**.

4.  **Add Edges:**
    * Click **"Add Edge from Database"**.
    * Select table **`edges`**.
    * **From Key:** `source_node_id`
    * **To Key:** `target_node_id`
    * Click **Submit**.

---

## 5. Run Demo Queries

Proceed to the PuppyGraph Query Editor to run your demo Gremlin queries.
