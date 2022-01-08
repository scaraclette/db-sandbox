# Sharding 

What is Sharding? Split 1 million rows table into 5 database instances with the same schema. Database lives in different nodes/servers.

Questions:
1. Where would a random row go?

## Consistent Hashing
Example of having 3 shards.

O       -        O        -        0

Input example
-> Hash("input 1") = shard 1
-> Hash("input 2") = shard 3

Simple hash function implementation: num("input2") % 3

## Horizontal Partitioning vs Sharding
- HP splits big table into multiple tables in the same database
- Sharding splits big table into multiple tables across multiple database servers
- HP table name changes (or schema)
- Sharding everything is the same but server changes.

## Example
Create a sample app:
- Spin up 3 postgres instances with identical schema 
- Write to the sharded database
- Read from the sharded database