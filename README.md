# PuppyGraph Demo GA360 Analytics

## Prerequisites
- `docker-compose.yaml` in the current directory.
- Data file at `~/Downloads/sample_data.csv`. (adjust accordingly)
- SQL files (`hit_tables.sql`, `nodes.sql`, `edges.sql`) in the current directory.
- Assumed MySQL Root Password: `mysql123`
- Assumed MySQL Database Name: `journey`

---

```bash
# --- 1. Launch Containers ---

# Launch PuppyGraph container
docker run -p 8081:8081 -p 8182:8182 -p 7687:7687 -e PUPPYGRAPH_PASSWORD=puppygraph123 -e QUERY_TIMEOUT=5m -d --name puppy --rm --pull=always puppygraph/puppygraph:stable

# Launch MySQL container defined in docker-compose.yml
docker compose up -d
```

---

## 2. Copy Data and Create/Load Schema (Execute Inside MySQL Shell)

 2a. Copy the CSV file to the MySQL container's secure directory
```bash
docker cp ~/Downloads/sample_data.csv mysql-server:/var/lib/mysql-files/sample_data.csv
```

 2b. Execute sql script to create database, table, and load data
```bash
docker exec -i mysql-server mysql -uroot -pmysql123
-- Database Management
CREATE DATABASE journey;
USE journey;
```

-- Table Creation (as provided)
```bash
CREATE TABLE sample_data (
    visitId bigint,
    visitNumber int,
    visitStartTime int,
    date date,
    fullVisitorId varchar(255),
    total_visits int,
    total_hits int,
    total_pageviews int,
    total_bounces int,
    total_session_quality int,
    total_revenue bigint,
    source varchar(255),
    medium varchar(255),
    campaign varchar(255),
    keyword varchar(255),
    device varchar(50),
    browser varchar(50),
    operatingSystem varchar(50),
    country varchar(100),
    city varchar(100),
    hit_number int,
    page_path text,
    page_title text,
    is_entrance varchar(5),
    is_exit varchar(5),
    time int,
    action_type int,
    action_step int,
    transaction_id varchar(255),
    event_category varchar(255),
    event_action varchar(255),
    event_label varchar(255),
    product_names text,
    product_skus text
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
# Execute the hit_tables.sql file
docker exec -i mysql-server mysql -uroot -pmysql123 my_demo_db < hit_tables.sql

# Execute the nodes.sql file
docker exec -i mysql-server mysql -uroot -pmysql123 my_demo_db < nodes.sql

# Execute the edges.sql file
docker exec -i mysql-server mysql -uroot -pmysql123 my_demo_db < edges.sql
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
