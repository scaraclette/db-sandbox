# Partitioning Concepts

Definition: The process where very large tables are divided into multiple smaller parts. By splitting a larger table into smaller, individual tables, queries that access only a fraction of the data can run faster because there is less data to scan.

Pros of Partitioning:
1. Improves query performance when accessing a single partition
2. Sequential scan vs scattered index scan
3. Easy bulk loading (attach partition)
4. Archive old data that are barely accessed into cheap storage.

Cons of Paritioning:
1. Updates that move rows from a partition to another (slow or fail sometimes)
2. Inefficient queries could accidentally scan all partitions resulting in slower performance.
3. Schema changes can be challenging (DBMS coudl manage it though)

TODO:
1. Different indexings (example parallel index scan)
2. Compare time it takes for scanning a partitioned tabel vs non-partitioned table.

## Example Use Case

Create basic table without partition
1. Run pg through docker
``` 
docker run --name pgmain -d -e POSTGRES_PASSWORD=postgres postgres
```
2. Run container bash and pg
```
docker exec -it pgmain bash
psql -U postgres
```
3. Create a table and populate with 1,000,000 rows
```
create table grades_org (id serial not null, g int not null );
INSERT INTO grades_org(g) SELECT floor(random() * 100) FROM generate_series (0, 1000000);
```
4. Create index
```
CREATE INDEX grades_org_index ON grades_org(g);
```
5. Try doing different queries
```
explain analyze select count(*) from grades_org where g between 30 and 35;
```
See analysis and compare with partitioned table

Create partitioned tables
1. Create base table
```
create table grades_parts (id serial not null, g int not null) partition by range(g);
```
2. Create partitioned tables
```
create table g0035 (like grades_parts including indexes);
create table g3560 (like grades_parts including indexes);
create table g6080 (like grades_parts including indexes);
create table g80100 (like grades_parts including indexes);
```
3. Attached partitioned tables to main tables
```
alter table grades_parts attach partition g0035 for values from (0) to (35);
alter table grades_parts attach partition g3560 for values from (35) to (60);
alter table grades_parts attach partition g6080 for values from (60) to (80);
alter table grades_parts attach partition g80100 for values from (80) to (100);
```
4. Populate data in partitioned tables. Note that every insertion, each value will be automatically paritioned. 
```
insert into grades_parts select * from grades_org;
```
5. Check table inputs
```
select count(*) from g0035;
select count(*) from g3560;
select count(*) from g6080;
select count(*) from g80100;
```
6. Create Index for the partitioned database. Automatically creates indexes for all partition
```
create index grades_parts_idx on grades_parts(g);
```