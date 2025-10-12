start docker in terminal

docker run -p 8081:8081 -p 8182:8182 -p 7687:7687 -e PUPPYGRAPH_PASSWORD=puppygraph123  -e QUERY_TIMEOUT=5m -d --name puppy --rm --pull=always puppygraph/puppygraph:stable

docker compose up -d

docker exec -it mysql-server mysql -uroot -p

Input the root password (default root password: mysql123) of mysql-server to access the MySQL client shell.

Copy sample_data.csv to docker through terminal

docker cp ~/Downloads/sample_data.csv mysql-server:/var/lib/mysql-files/sample_data.csv

create database
use database
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


LOAD DATA INFILE '/var/lib/mysql-files/sample_data.csv'
INTO TABLE sample_data
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

run the attached sql queries in workbench or terminal
hit_tables.sql first
then nodes.sql and edges.sql

then go into puppygraph through localhost
username puppygraph
password puppygraph123

select create schema
use mysql8

name the catalog anything;
(from the docker yaml file)
username: mysqluser
password: mysqlpassword 
jdbc connection string: jdbc:mariadb://mysql:3306

save and submit


then,
add node from database 
from table nodes
and add node

add edge from database
from table edges
from key source_node_id
to key target_node_id

then submit

then you can copy my demo gremlin queries




